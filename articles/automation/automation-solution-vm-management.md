---
title: "VMs de início/paragem durante a solução de off-hours | Microsoft Docs"
description: "A solução Gestão de VMs inicia e para as suas Máquinas Virtuais do Azure Resource Manager de acordo com uma agenda e monitorizam proativamente a partir do Log Analytics."
services: automation
documentationCenter: 
authors: eslesar
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: 2ff2208f62c24c460c9d17533e28fd007549828b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>VMs de início/paragem durante a solução de off-hours na automatização do Azure

As VMs de início/paragem durante off-hours solução é iniciado e interrompe as máquinas virtuais do Azure agendas definido pelo utilizador, fornece informações através da análise de registos e envia mensagens de correio eletrónico opcionais, tirando partido [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Suporta o Azure Resource Manager e VMs clássicas para a maioria dos cenários. 

Esta solução fornece uma capacidade de automatização descentralizada para que os clientes pretendem a reduzir os custos tirar partido de recursos sem servidor, de baixo custos. As funcionalidades incluem:

* VMs de agenda para iniciar/parar
* Agendar VMs para iniciar/parar ascendentemente utilizando as etiquetas do Azure (não suportado para VMs clássicas)
* Parar automática que VMS com base na utilização de CPU baixa

## <a name="prerequisites"></a>Pré-requisitos

- Os runbooks funcionam com uma [conta Run As do Azure](automation-offering-get-started.md#authentication-methods).  A conta Run As é o método de autenticação preferido, porque utiliza a autenticação de certificados em vez de palavras-passe, que podem expirar ou ser alteradas frequentemente.  

- Esta solução só pode gerir as VMs que estão na mesma subscrição onde reside a conta de automatização.  

- Esta solução implementa apenas as seguintes regiões do Azure - Sudeste da Austrália, Canadá Central, Índia Central, EUA leste, leste do Japão, Sudeste asiático, RU Sul e Europa Ocidental.  
    
    > [!NOTE]
    > Os runbooks gerir a agenda VM pode visar VMs em qualquer região.  

- Enviar notificações por e-mail ao concluir os runbooks de início e a paragem da VM, durante a integração do Azure Marketplace, tem de selecionar selecione **Sim** implementar SendGrid. 

    > [!IMPORTANT]
    > SendGrid é um serviço de terceiros, para o suporte com SendGrid contacte [SendGrid](https://sendgrid.com/contact/).  
    >
   
    Limitações com SendGrid são os seguintes:

    * Máximo de uma conta de SendGrid por utilizador por subscrição
    * Máximo de duas contas SendGrid por subscrição

Se tiver implementado uma versão anterior desta solução, primeiro terá de eliminá-lo da sua conta antes de implementar esta versão.  

## <a name="solution-components"></a>Componentes da solução

Esta solução inclui pré-configurada runbooks, as agendas e integração com a análise de registos, que permite-lhe personalizar o arranque e encerramento das máquinas virtuais para suite sua empresa precisa. 

### <a name="runbooks"></a>Runbooks

A tabela seguinte lista os runbooks implementados à sua conta de automatização.  Não é recomendado que efetuar alterações ao código de runbook, mas em vez disso, escrever o seus próprios runbook para a nova funcionalidade.

> [!NOTE]
> Não executar diretamente qualquer runbook com o nome "Subordinado" acrescentado ao fim do respetivo nome.
>

Inclui todos os runbooks do principal a *WhatIf* parâmetro, que quando definido como **verdadeiro**, suporta com detalhes sobre o comportamento exato runbook executa quando executar sem o *WhatIf* parâmetro e valida o correto VMs estão a ser direcionadas.  Os Runbooks só efetua as ações definidas quando o *WhatIf* parâmetro está definido como **falso**. 

|**Runbook** | **Parâmetros** | **Descrição**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamada a partir do runbook principal apenas. Cria alertas per recurso base para o cenário de AutoStop.| 
|AutoStop_CreateAlert_Parent | WhatIf: VERDADEIRO ou FALSO <br> VMList | Cria ou atualiza as regras de alertas do Azure em VMs nos grupos direcionados subscrição ou recurso. <br> VMList: Separados por vírgulas lista de VMs.  Por exemplo, *vm1 vm2, vm3*| 
|AutoStop_Disable | Nenhum | Desative alertas de AutoStop e a agenda predefinida.| 
|AutoStop_StopVM_Child | WebHookData | Chamado a partir apenas o runbook principal. Regras de alerta chamar este runbook e faz o trabalho de parar a VM.|  
|Bootstrap_Main | Nenhum | Utilizado uma vez para configurações de arranque de configuração, como webhookURI que normalmente não está acessível a partir do Azure Resource Manager. Este runbook será removido automaticamente se a implementação tornou-se com êxito.|  
|ScheduledStartStop_Child | VMName <br> Ação: Parar ou iniciar <br> resourceGroupName | Chamado a partir apenas o runbook principal. Não a execução real de paragem ou de início para parar agendada.|  
|ScheduledStartStop_Parent | Ação: Parar ou iniciar <br> WhatIf: VERDADEIRO ou FALSO | Isto produzirá efeito no todas as VMs na subscrição, a menos que edita o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** que será restringi-la para executar apenas em estes grupos de recursos de destino. Também pode excluir VMs específicas atualizando o **External_ExcludeVMNames** variável. WhatIf comporta-se os mesmos que outros runbooks.|  
|SequencedStartStop_Parent | Ação: Parar ou iniciar <br> WhatIf: VERDADEIRO ou FALSO | Crie uma tag chamada **SequenceStart** e outra tag chamado **SequenceStop** em cada VM que pretende o início da sequência\\parar atividade. O valor da etiqueta deve ser um número inteiro positivo (1,2,3) que corresponde à ordem que pretende iniciar\\parar por ordem ascendente. WhatIf comporta-se os mesmos que outros runbooks. <br> **Nota: VMs devem ser em grupos de recursos definida External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames em variáveis de automatização do Azure e tem as etiquetas adequadas para ações entre em vigor.**|

### <a name="variables"></a>Variáveis

A tabela seguinte lista as variáveis criadas na sua conta de automatização.  É recomendado que só modificar variáveis de prefixo **externo**, modificar variáveis de prefixo **interno** fará com que os efeitos indesejáveis.  

|**Variável** | **Descrição**|
---------|------------|
|External_AutoStop_Condition | Este é o operador condicional necessário para configurar a condição antes de acionar um alerta. Os valores aceitáveis são **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, **LessThanOrEqual**.|  
|External_AutoStop_Description | Para parar a VM se a % de CPU exceder o limiar de alerta.|  
|External_AutoStop_MetricName | Nome da métrica de desempenho a regra de alerta do Azure está a ser configuradas para.| 
|External_AutoStop_Threshold | Limiar para a regra de alerta do Azure especificada na variável *External_AutoStop_MetricName*. Percentagem valores podem variar entre 1 e 100.|  
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo que será aplicado ao tamanho da janela selecionados para avaliar a condição. Os valores aceitáveis são **médio**, **mínimo**, **máximo**, **Total**, **último**.|  
|External_AutoStop_TimeWindow | O tamanho da janela durante o qual o Azure irá analisar métrica selecionada para acionar um alerta. Este parâmetro aceita entrada no formato timespan. Os valores possíveis são de cinco minutos para seis horas.|  
|External_EmailFromAddress | Especifica o remetente do e-mail.|  
|External_EmailSubject | Especifica o texto para a linha do assunto do e-mail.|  
|External_EmailToAddress | Especifica o destinatário ou destinatários do e-mail. Separar os nomes com uma vírgula.|  
|External_ExcludeVMNames | Introduza os nomes de VM a serem excluídos, separando os nomes com uma vírgula sem espaços.|  
|External_IsSendEmail | Especifica a opção para enviar notificações de e-mail após a conclusão.  Especifique **Sim** ou **não** para não enviar correio eletrónico.  Esta opção deve ser **não** se não tiver criado SendGrid durante a implementação inicial.|  
|External_Start_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando os valores com uma vírgula, direcionada para ações de início.|  
|External_Stop_ResourceGroupNames | Especifica um orar mais grupos de recursos, separando os valores com vírgulas, direcionada para ações de paragem.|  
|Internal_AutomationAccountName | Especifica o nome da conta de automatização.|  
|Internal_AutoSnooze_WebhookUri | Especifica o que URI de Webhook chamado para o cenário de AutoStop.|  
|Internal_AzureSubscriptionId | Especifica o ID de subscrição do Azure.|  
|Internal_ResourceGroupName | Especifica o nome de grupo de recursos da conta de automatização do Azure.|  
|Internal_SendGridAccountName | Especifica o nome de conta do SendGrid.|  
|Internal_SendGridPassword | Especifica a palavra-passe de conta do SendGrid.|  

<br>

Em todos os cenários, o **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, e **External_ExcludeVMNames** variáveis são necessárias para direcionada para VMs com a exceção que fornece uma vírgula separados lista de VMs para o **AutoStop_CreateAlert_Parent** runbook. Ou seja, as VMs têm de residir em grupos de recursos de destino para ações de início/paragem para ocorrer. A lógica um pouco funciona da seguinte forma de política do Azure, na medida em que pode no grupo de subscrição ou recurso de destino e ter ações herdadas por VMs recentemente criadas. Esta abordagem evita a necessidade de manter uma agenda separada para cada VM e gerir início/paragem no dimensionamento.

### <a name="schedules"></a>Agendas

A tabela seguinte lista cada as agendas predefinidas criadas na sua conta de automatização.  Pode modificá-las ou criar os seus próprios agendamentos personalizados.  Por predefinição cada estas agendas estão desativadas, exceto para **Scheduled_StartVM** e **agendada StopVM**.

Não se recomenda para ativar todos os agendamentos, porque este foi possível criar uma sobreposição como a agenda de que executa uma ação.  Em vez disso, será melhor determinar quais as otimizações de que pretende executar e modificar em conformidade.  Ver os cenários de exemplo na secção Descrição geral para obter explicações mais.   

|**ScheduleName** | **Frequência** | **Descrição**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | É executada a cada oito horas | Executa o runbook AutoStop_CreateAlert_Parent a cada 8 horas, por sua vez, irá parar com base em valores da VM "External_Start_ResourceGroupNames", "External_Stop_ResourceGroupNames" e "External_ExcludeVMNames" em variáveis de automatização do Azure.  Em alternativa, pode especificar uma lista separada por vírgulas de VMs utilizando o parâmetro de "VMList".|  
|Scheduled_StopVM | Definido pelo utilizador, todos os dias | Executa o runbook Scheduled_Parent com um parâmetro de "Parar de" todos os dias às tempo indicado.  Automaticamente irá parar todas as VMS que cumpram as regras definidas através de variáveis de recurso.  Recomendamos a ativar a sister agendar,-StartVM agendada.|  
|Scheduled_StartVM | Definido pelo utilizador, todos os dias | Executa o runbook Scheduled_Parent com um parâmetro de *iniciar* diariamente o momento de determinado.  Irá iniciar automaticamente todas as VM que cumpram as regras definidas e parte das variáveis adequadas.  Recomenda-se ativar a sister agenda, **agendada StopVM**.|
|StopVM sequenciado | 1:00:00 (UTC), cada sexta-feira | Executa o runbook Sequenced_Parent com um parâmetro de *parar* cada sexta-feira determinado momento.  Será sequencialmente (ascendente) parar todas as VMS com uma etiqueta de **SequenceStop** definido e fazem parte das variáveis adequadas.  Consulte a secção de Runbooks para obter mais detalhes sobre as variáveis de recurso e valores de etiqueta.  É recomendável a ativar a sister agenda, **Sequenced-StartVM**.|
|Sequenciado-StartVM | 1:00 PM (UTC), cada segunda-feira | Executa o runbook Sequenced_Parent com um parâmetro de *iniciar* cada segunda-feira no momento especificado.  Será sequencialmente início (descendente) todas as VMS com uma etiqueta de **SequenceStart** definido e fazem parte das variáveis adequadas.  Consulte a secção de Runbooks para obter mais detalhes sobre as variáveis de recurso e valores de etiqueta.  Recomenda-se ativar a sister agenda, **Sequenced StopVM**.|

<br>

## <a name="configuration"></a>Configuração

Execute os passos seguintes para adicionar a solução Iniciar/Parar VMs fora do horário comercial [Preview] à sua conta de Automatização e, depois, configure as variáveis para a personalizar.

1. No portal do Azure, clique em **Novo**.<br> ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. No painel de Marketplace, escreva **iniciar VM**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **VMs de início/paragem durante off-hours [pré-visualização]** de resultados da pesquisa.  
3. No **VMs de início/paragem durante off-hours [pré-visualização]** painel para a solução selecionada, reveja as informações de resumo e, em seguida, clique em **criar**.  
4. O **Adicionar solução** painel aparece em que lhe for pedido para configurar a solução antes de importá-lo na sua subscrição de automatização.<br><br> ![Painel Adicionar Solução de Gestão de VMs](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  No **Adicionar solução** painel, selecione **área de trabalho** e aqui selecionou uma área de trabalho do OMS que está ligada à mesma subscrição do Azure que está a ser a conta de automatização ou crie uma nova área de trabalho.  Se não tiver uma área de trabalho, pode selecionar **criar nova área de trabalho** e o **área de trabalho OMS** painel efetue o seguinte: 
   - Especifique um nome para a **Área de Trabalho do OMS**.
   - Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   - Em **Grupo de Recursos**, pode criar um grupo de recursos novos ou selecionar um já existente.  
   - Selecione uma **Localização**.  Atualmente as únicas localizações fornecidas para a seleção são **Sudeste da Austrália**, **Canadá Central**, **Índia Central**, **EUA Leste**, **Este do Japão**, **Sudeste asiático**, **sul do RU**, e **Europa Ocidental**.
   - Selecione um **Escalão de preço**.  A solução é oferecida em dois escalões, gratuito e escalão pago do OMS.  O escalão gratuito tem um limite quanto à quantidade de dados recolhidos diariamente, ao período de retenção e aos minutos de tempo de execução de trabalhos de runbook.  O escalão pago do OMS não tem limite quanto à quantidade de dados recolhidos diariamente.  

        > [!NOTE]
        > Enquanto a camada por GB (autónomo) paga é apresentada como uma opção, não é aplicável.  Se o selecionar e avançar com a criação desta solução na sua subscrição, a criação falha.  Esta opção será contemplada quando a solução for lançada oficialmente.<br>Se utilizar esta solução, a mesma só vai utilizar minutos de trabalhos de automatização e ingestão de registos.  A solução não acrescenta nós do OMS adicionais ao seu ambiente.  

6. Depois de fornecer as informações necessárias no painel **Área de Trabalho do OMS**, clique em **Criar**.  Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu.  Será reencaminhado para o painel **Adicionar Solução**.  
7. No painel **Adicionar Solução**, selecione **Conta de Automatização**.  Se estiver a criar uma área de trabalho do OMS novo, terá também de criar uma conta de Automatização nova, que vai ser associada à área de trabalho do OMS especificada anteriormente, incluindo a subscrição, o grupo de recursos e a região do Azure.  Pode selecionar **Criar conta de Automatização** e, no painel **Adicionar conta de Automatização**, indique o seguinte: 
  - No campo **Nome**, introduza o nome da conta de Automatização.

    Todas as outras opções são preenchidas automaticamente com base na área de trabalho do OMS selecionada e não podem ser modificadas.  O método de autenticação predefinido para os runbooks incluídos nesta solução é a conta Run As do Azure.  Depois de clicar em **OK**, as opções de configuração são validadas e a conta de Automatização é criada.  Pode acompanhar o progresso em **Notificações**, no menu. 

    Caso contrário, pode selecionar uma conta Run As de Automatização existente.  Tenha em atenção que a conta que selecionar não pode já estar associada a outra área de trabalho do OMS, caso contrário, será apresentada uma mensagem no painel para o informar.  Se já estiver associada, terá de selecionar uma conta Run As de Automatização diferente ou criar uma nova.<br><br> ![Conta de Automatização Já Associada à Área de Trabalho do OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Por fim, no painel **Adicionar Solução**, selecione **Configuração** e é apresentado o painel **Parâmetros**.<br><br> ![Painel de parâmetros para a solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  No painel **Parâmetros**, é-lhe pedido que:  
   - Especifique os **Nomes de ResourceGroup de Destino**, que é um nome de grupo de recursos que contém as VMs a serem geridas por esta solução.  Pode introduzir mais do que um nome e separe cada um com uma vírgula (valores não são maiúsculas e minúsculas).  Se quiser segmentar todas as VMs em todos os grupos de recursos da subscrição, a utilização de um caráter universal é suportada.
   - Specity o **VM excluir lista (cadeia)**, que é o nome do on ou mais máquinas virtuais do grupo de recursos de destino.  Pode introduzir mais do que um nome e separe cada um com uma vírgula (valores não são maiúsculas e minúsculas).  Utilizar um caráter universal é suportado.
   - Selecione uma **Agenda**, que é uma data e hora recorrente para iniciar e parar as VMs no grupo ou grupos de recursos de destino.  Por predefinição, a agenda está configurada para o fuso horário UTC e selecionar uma região diferente não está disponível.  Se pretender configurar a agenda para o seu fuso horário específico depois de configurar a solução, consulte [modificar a agenda de arranque e encerramento](#modifying-the-startup-and-shutdown-schedule) abaixo.
   - Para receber **notificações por correio eletrónico** do SendGrid, aceite o valor predefinido de **Sim** e forneça um endereço de correio eletrónico válido.  Se selecionar **não** e mais tarde decidir que pretende receber notificações por e-mail, terá de voltar a implementar a solução novamente a partir do Azure marketplace.  

10. Depois de concluída a configuração das definições iniciais necessárias para a solução, selecione **Criar**.  Todas as definições serão validadas e, em seguida, tenta implementar a solução na sua subscrição.  Este processo pode demorar alguns segundos até ser concluído e pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="collection-frequency"></a>Frequência da recolha

Automatização registo e a tarefa de fluxo dados da tarefa é ingeridos para o repositório de análise de registos a cada cinco minutos.  

## <a name="using-the-solution"></a>Utilizar a solução

Quando adicionar a solução de gestão de VM, na sua área de trabalho de análise de registos do Portal do Azure, o **StartStopVM vista** mosaico é adicionado ao dashboard.  Este mosaico mostra uma contagem e a representação gráfica das tarefas de runbooks para a solução que tenham iniciado e foi concluída com êxito.<br><br> ![Mosaico Vista StartStopVM da Gestão de VMs](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Na sua conta de automatização, pode aceder e gerir a solução, selecionando o **área de trabalho** opção e na página de análise de registos, selecione **soluções** no painel esquerdo.  No **soluções** página, selecione a solução **Start-Stop-VM [área de trabalho]** da lista.<br><br> ![Lista de soluções na análise de registos](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Selecionar a solução irá apresentar o **Start-Stop-VM [área de trabalho]** painel de solução, onde poderá consultar detalhes importantes, tais como o **StartStopVM** na peça de mosaico, como a área de trabalho de análise de registos, que Mostra uma contagem e a representação gráfica das tarefas de runbook para a solução que foram iniciadas e concluiu com êxito.<br><br> ![Página de solução de gestão de atualizações de automatização](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

A partir daqui também pode efetuar mais análise de registos da tarefa ao clicar no mosaico do anel do dashboard de solução mostra o histórico de tarefas, consultas de pesquisa de registo predefinido, e mudar para o portal de registo avançadas de análise para procurar com base nas suas consultas de pesquisa.  

Inclui todos os runbooks do principal a *WhatIf* parâmetro, que quando definido como **verdadeiro**, suporta com detalhes sobre o comportamento exato runbook executa quando executar sem o *WhatIf* parâmetro e valida o correto VMs estão a ser direcionadas.  Os Runbooks só efetua as ações definidas quando o *WhatIf* parâmetro está definido como **falso**.  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>Cenário 1: Diariamente paragem/início VMs através de uma subscrição ou grupos de recursos de destino 

Esta é a configuração predefinida quando implementar a solução pela primeira vez.  Por exemplo, pode configurá-la para parar todas as VMs através de uma subscrição no evening quando deixar o trabalho e iniciá-los da manhã quando estiver novamente no escritório. Quando configurar as agendas * * agendada-StartVM "e **agendada StopVM** durante a implementação, iniciar e parar a VMs de destino.
>[!NOTE]
>O fuso horário é o seu fuso horário atual quando configura o parâmetro da hora de agendamento; No entanto, é armazenado em formato UTC na automatização do Azure.  Não é necessário efetuar qualquer conversão do fuso horário, como isto é processado durante a implementação.

Pode controlar que as VMs estão no âmbito, configurando as duas variáveis - **External_Start_ResourceGroupNames**, * * External_Stop_ResourceGroupNames, e **External_ExcludeVMNames**.  

>[!NOTE]
> O valor de variável **nomes ResourceGroup de destino**"são armazenadas como o valor para ambos **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** variáveis. Para obter mais granularidade, pode modificar cada uma destas variáveis para visar grupos de recursos diferentes.  Para utilização de ação de início **External_Start_ResourceGroupNames** e para utilização de ação de paragem **External_Stop_ResourceGroupNames** em vez disso. Novas VMs são adicionadas automaticamente no início e parar agendas.

Para testar e validar a configuração, inicie manualmente o **ScheduledStartStop_Parent** runbook e defina o *ação* parâmetro **iniciar** ou **parar**  e *WhatIf* parâmetro **verdadeiro**.<br><br> ![Configurar os parâmetros do runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Isto permite-lhe pré-visualizar a ação que iria ter ocorrido e efetue as alterações necessárias antes de implementar em produção VMs.  Assim que estiver confortável, que pode executar manualmente o runbook com o parâmetro definido como **falso** ou permitir que a agenda de automatização **agenda-StartVM** e **agenda-StopVM**executar a sua agenda prescrita automaticamente os seguintes.

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>Cenário 2: Parar/início VMs através de uma subscrição com etiquetas de sequência
Num ambiente que inclua dois ou mais componentes em várias VMs suportar uma carga de trabalho distribuída, é importante que suportam a sequência de que componentes são iniciadas/paradas por ordem.  Para tal, efetuando os seguintes passos.

1. Adicionar um **SequenceStart** e **SequenceStop** etiquetas com um valor de número inteiro positivo para VMs em toda a sua subscrição abrangidos **External_Start_ResourceGroupNames**e  **External_Stop*ResourceGroupNames** variáveis.  As ações de início e paragem serão efetuadas por ordem ascendente.  Para saber como marcar uma VM, consulte [marcar a Máquina Virtual do Windows no Azure](../virtual-machines/windows/tag.md) e [Tag uma Máquina Virtual Linux no Azure](../virtual-machines/linux/tag.md)
2. Modifique as agendas **Sequenced-StartVM** e **Sequenced StopVM** para a data e hora para satisfazer os seus requisitos e ativar o agendamento.  

Para testar e validar a configuração, inicie manualmente o **SequencedStartStop_Parent** runbook e defina o *ação* parâmetro **iniciar** ou **parar**  e *WhatIf* parâmetro **verdadeiro**.<br><br> ![Configurar os parâmetros do runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Isto permite-lhe pré-visualizar a ação que iria ter ocorrido e efetue as alterações necessárias antes de implementar em produção VMs.  Assim que estiver confortável, que pode executar manualmente o runbook com o parâmetro definido como **falso** ou permitir que a agenda de automatização **Sequenced-StartVM** e **Sequenced-StopVM**executar a sua agenda prescrita automaticamente os seguintes.  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>Cenário 3: Paragem/início automático VMs através de uma subscrição com base na utilização da CPU
Para ajudar a gerir o custo de VMs em execução na sua subscrição, esta solução pode ajudar a avaliar as VMs do Azure que não são utilizados durante períodos de pico, tal como após horas e automaticamente encerrá-las se a utilização do processador é inferior a % de x.  

Por predefinição, a solução está pré-configurada para avaliar a métrica de percentagem de CPU e utilização média estiver 5% ou menos.  Este é controlado pelas seguintes variáveis e pode ser modificada se os respetivos valores predefinidos não cumprem os requisitos:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Só pode ativar o direcionada para a ação contra uma subscrição e o grupo de recursos ou a lista específica de VMs, mas não ambos.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>A ação de paragem em relação a um grupo de recursos e de subscrição de destino

1. Configurar o **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** variáveis para especificar o destino VMs.  
2. Ativar e atualizar o **Schedule_AutoStop_CreateAlert_Parent** agenda.
3. Execute o **AutoStop_CreateAlert_Parent** runbook com o *ação* parâmetro definido como **iniciar** e o *WhatIf* parâmetro definido como  **Verdadeiro** para pré-visualizar as suas alterações.

#### <a name="target-stop-action-by-vm-list"></a>Ação de paragem de destino pela lista VM

1. Executar o **AutoStop_CreateAlert_Parent** runbook com o *ação* parâmetro definido como **iniciar**, adicione uma lista separada por vírgulas de VMs no *VMList* parâmetro e o *WhatIf* parâmetro definido como **verdadeiro** para pré-visualizar as suas alterações.  
2. Configurar o **External_ExcludeVMNames** parâmetros com uma vírgula separados lista de VMs (VM1, VM2, VM3).
3. Este cenário não honrar o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames** varabies.  Para este cenário, terá de criar a agenda de automatização própria. Para obter mais informações, consulte [agendar um runbook na automatização do Azure](../automation/automation-schedules.md).

Agora que tem uma agenda para parar as VMs com base na utilização de CPU, terá de ativar uma o abaixo agendas iniciá-las.

* Ação de iniciação de destino através da subscrição e grupo de recursos.  Consulte os passos em [cenário n. º 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) para teste e ativação **agendada-StartVM** agenda.
* Ação de iniciação de destino por subscrição, grupo de recursos e a etiqueta.  Consulte os passos em [cenário n. º 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) para teste e ativação "Sequenced-StartVM" agendar.


### <a name="configuring-e-mail-notifications"></a>Configurar as notificações por e-mail

Para configurar notificações por e-mail depois de implementar a solução, pode modificar as seguintes três variáveis:

* External_EmailFromAddress - especifique o endereço de correio eletrónico do remetente
* Lista de endereços de correio eletrónico de valores separados por External_EmailToAddress - uma vírgula (user@hotmail.com, user@outlook.com) para receber notificações por e-mail
* External_IsSendEmail - se definido como **Sim**, irá receber e-mails e para desativar notificações por e-mail, defina o valor para **não**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modificar as agendas de arranque e encerramento

Gerir as agendas de arranque e encerramento nesta solução segue os mesmos passos conforme descritos em [agendar um runbook na automatização do Azure](automation-schedules.md).     

## <a name="log-analytics-records"></a>Registos do Log Analytics

A Automatização cria dois tipos de registos no repositório do OMS.

### <a name="job-logs"></a>Registos de trabalhos

Propriedade | Descrição|
----------|----------|
Autor da chamada |  Quem iniciou a operação.  Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
Categoria | Classificação do tipo de dados.  Para a Automatização, o valor é JobLogs.|
CorrelationId | GUID que é o Id de Correlação do trabalho do runbook.|
JobId | GUID que é o Id do trabalho do runbook.|
operationName | Especifica o tipo de operação efetuada no Azure.  Para a Automatização, o valor será Job.|
resourceId | Especifica o tipo de recurso no Azure.  Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir.  Para a Automatização, o valor é Automatização do Azure.|
ResourceType | Especifica o tipo de recurso no Azure.  Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
resultType | O estado do trabalho do runbook.  Os valores possíveis são:<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>- Bem-sucedido|
resultDescription | Descreve o estado do resultado do trabalho do runbook.  Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído|
RunbookName | Especifica o nome do runbook.|
SourceSystem | Especifica o sistema de origem dos dados submetidos.  Para a Automatização, o valor será :OpsManager|
StreamType | Especifica o tipo de evento. Os valores possíveis são:<br>- Verboso<br>- Saída<br>- Erro<br>- Aviso|
SubscriptionId | Especifica o ID de subscrição do trabalho.
Hora | Data e hora da execução do trabalho do runbook.|


### <a name="job-streams"></a>Fluxos de trabalho

Propriedade | Descrição|
----------|----------|
Autor da chamada |  Quem iniciou a operação.  Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
Categoria | Classificação do tipo de dados.  Para a Automatização, o valor é JobStreams.|
JobId | GUID que é o Id do trabalho do runbook.|
operationName | Especifica o tipo de operação efetuada no Azure.  Para a Automatização, o valor será Job.|
ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
resourceId | Especifica o Id de recurso no Azure.  Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir.  Para a Automatização, o valor é Automatização do Azure.|
ResourceType | Especifica o tipo de recurso no Azure.  Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
resultType | O resultado do trabalho do runbook no momento em que o evento foi gerado.  Os valores possíveis são:<br>- InProgress|
resultDescription | Inclui o fluxo de saída do runbook.|
RunbookName | O nome do runbook.|
SourceSystem | Especifica o sistema de origem dos dados submetidos.  Para a Automatização, o valor será OpsManager|
StreamType | O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Em Curso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso|
Hora | Data e hora da execução do trabalho do runbook.|

Quando faz uma pesquisa de registos que devolva registos da categoria **JobLogs** ou **JobStreams**, pode selecionar a vista **JobLogs** ou **JobStreams**, que apresenta um conjunto de mosaicos que resumem as atualizações devolvidas pela pequisa.

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de trabalhos que esta solução recolhe. 

Consulta | Descrição|
----------|----------|
Localizar a tarefa de runbook ScheduledStartStop_Parent que foram concluídos com êxito | Procurar categoria = = "JobLogs" &#124; onde (RunbookName_s = = "ScheduledStartStop_Parent") &#124; onde (ResultType = = "Concluída") &#124; resumir AggregatedValue = existente pelo ResultType, bin (TimeGenerated, 1h) &#124; Ordenar por TimeGenerated desc|
Localizar a tarefa de runbook SequencedStartStop_Parent que foram concluídos com êxito | Procurar categoria = = "JobLogs" &#124; onde (RunbookName_s = = "SequencedStartStop_Parent") &#124; onde (ResultType = = "Concluída") &#124; resumir AggregatedValue = existente pelo ResultType, bin (TimeGenerated, 1h) &#124; Ordenar por TimeGenerated desc

## <a name="removing-the-solution"></a>Remover a solução

Se decidir que já não necessita de utilizar a solução qualquer adicional, pode eliminá-la a conta de automatização.  A eliminar a solução irá remover apenas os runbooks, não irá eliminar os agendamentos ou variáveis que foram criadas quando a solução foi adicionada.  Esses recursos, terá de eliminar manualmente se não estiver a utilizá-los com outros runbooks.  

Para eliminar a solução, execute os seguintes passos:

1.  A partir da sua conta de automatização, selecione **área de trabalho** no painel esquerdo.  
2.  No **soluções** página, selecione a solução **Start-Stop-VM [área de trabalho]**.  No **VMManagementSolution [área de trabalho]** página, a partir do menu, clique em **eliminar**.<br><br> ![Eliminar a solução de gestão VM](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  No **Eliminar solução** janela, confirme que pretende eliminar a solução.
4.  Verificar as informações e a solução é eliminada, pode controlar o progresso em **notificações** no menu.  Será devolvido para o **soluções** página depois de iniciado o processo de remoção de solução.  

A conta de automatização e a área de trabalho de análise de registos não são eliminados como parte deste processo.  Se não pretender manter a área de trabalho de análise de registos, terá de eliminar manualmente.  Isto pode ser conseguido também do portal do Azure.   A partir da home ecrã no portal do Azure, selecione **Log Analytics** e, em seguida, no **Log Analytics** painel, selecione a área de trabalho e clique em **eliminar** no menu no Painel de definições da área de trabalho.  
      
## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como construir consultas de pesquisa diferentes e rever os registos dos trabalhos da Automatização com o Log Analytics, veja [Log searches in Log Analytics (Pesquisas de registos no Log Analytics)](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre a execução dos runbooks, como monitorizar trabalhos de runbooks e outros detalhes técnicos, veja [Track a runbook job (Acompanhar um trabalho de runbook)](automation-runbook-execution.md)
- Para saber mais sobre a análise de registos e as origens de recolha de dados, consulte [dados de armazenamento do Azure recolha na descrição geral da análise de registos](../log-analytics/log-analytics-azure-storage.md)






   


---
title: "Solução Iniciar/Parar VMs fora do horário comercial [Preview] | Microsoft Docs"
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
ms.date: 06/01/2017
ms.author: magoedte
ms.openlocfilehash: b4271d07858eacf2fa55e748f276c8252b0dedf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="startstop-vms-during-off-hours-preview-solution-in-automation"></a>Solução Iniciar/Parar VMs fora do horário comercial [Preview] na Automatização

A solução Iniciar/Parar VMs fora do horário comercial [Pré-visualização] inicia e para as suas máquinas virtuais do Azure Resource Manager de acordo com uma agenda definida pelo utilizador e proporciona informações sobre o êxito dos trabalhos da Automatização que iniciam e param as suas máquinas virtuais com o OMS Log Analytics.  

## <a name="prerequisites"></a>Pré-requisitos

- Os runbooks funcionam com uma [conta Run As do Azure](automation-offering-get-started.md#authentication-methods).  A conta Run As é o método de autenticação preferido, porque utiliza a autenticação de certificados em vez de palavras-passe, que podem expirar ou ser alteradas frequentemente.  

- Esta solução só pode gerir as VMs que estão na mesma subscrição onde reside a conta de automatização.  

- A solução só pode ser implementada nas regiões do Azure seguintes: Sudeste da Austrália, E.U.A. Leste, Sudeste Asiático e Europa Ocidental.  Os runbooks que gerem a agenda das VMs podem segmentar VMs em qualquer região.  

- Para enviar notificações por e-mail quando os runbooks de início e fim de VMs são concluídos, é necessária uma subscrição do Office 365 de nível empresarial.  

## <a name="solution-components"></a>Componentes da solução

Esta solução consiste nos seguintes recursos que serão importados e adicionados à sua conta de Automatização.

### <a name="runbooks"></a>Runbooks

Runbook | Descrição|
--------|------------|
CleanSolution-MS-Mgmt-VM | Este runbook remove todos os recursos contidos e agendas quando elimina a solução da sua subscrição.|  
SendMailO365-MS-Mgmt | Este runbook envia um e-mail através do Office 365 Exchange.|
StartByResourceGroup-MS-Mgmt-VM | Este runbook destina-se a iniciar VMs (clássicas e baseadas no ARM) que residem numa determinada lista de grupos de recursos do Azure.
StopByResourceGroup-MS-Mgmt-VM | Este runbook destina-se a parar VMs (clássicas e baseadas no ARM) que residem numa determinada lista de grupos de recursos do Azure.|
<br>

### <a name="variables"></a>Variáveis

Variável | Descrição|
---------|------------|
Runbook **SendMailO365-MS-Mgmt** ||
SendMailO365-IsSendEmail-MS-Mgmt | Especifica se os runbooks StartByResourceGroup-MS-Mgmt-VM e StopByResourceGroup-MS-Mgmt-VM podem enviar notificações por e-mail após a conclusão.  Selecione **Verdadeiro** para ativar e **Falso** para desativar os alertas por e-mail. O valor predefinido é **Falso**.| 
**StartByResourceGroup-MS-Mgmt-VM** Runbook ||
StartByResourceGroup-ExcludeList-MS-Mgmt-VM | Introduza os nomes VM que serão excluídas da operação de gestão Separe os nomes utilizando semi-colon(;) sem espaços. Os valores são sensíveis a maiúsculas e minúsculas e é suportado o caráter universal (asterisco).|
StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | Texto que pode ser anexado ao início do corpo da mensagem do e-mail.|
StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | Especifica o nome da Conta de Automatização que contém o runbook E-mail  **Não modifique esta variável..**|
StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt | Especifica o nome do runbook E-mail  É utilizada pelos runbooks StartByResourceGroup-MS-Mgmt-VM e StopByResourceGroup-MS-Mgmt-VM para enviar e-mails.  **Não modifique esta variável..**|
StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | Especifica o nome do Grupo de recursos que contém o runbook E-mail.  **Não modifique esta variável..**|
StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | Especifica o texto para a linha do assunto do e-mail.|  
StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | Especifica o destinatário ou destinatários do e-mail.  Introduza os nomes separados utilizando semi-colon(;) sem espaços.|
StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | Introduza os nomes VM que serão excluídas da operação de gestão Separe os nomes utilizando semi-colon(;) sem espaços. Os valores são sensíveis a maiúsculas e minúsculas e é suportado o caráter universal (asterisco).  O valor predefinido (asterisco) incluirá todos os grupos de recursos na subscrição.|
StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | Especifica a subscrição que contém as VMs a serem geridas por esta solução.  Tem de ser a mesma subscrição na qual a conta de Automatização da solução reside.|
Runbook **StopByResourceGroup-MS-Mgmt-VM** ||
StopByResourceGroup-ExcludeList-MS-Mgmt-VM | Introduza os nomes VM que serão excluídas da operação de gestão Separe os nomes utilizando semi-colon(;) sem espaços. Os valores são sensíveis a maiúsculas e minúsculas e é suportado o caráter universal (asterisco).|
StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | Texto que pode ser anexado ao início do corpo da mensagem do e-mail.|
StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | Especifica o nome da Conta de Automatização que contém o runbook E-mail  **Não modifique esta variável..**|
StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | Especifica o nome do Grupo de recursos que contém o runbook E-mail.  **Não modifique esta variável..**|
StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | Especifica o texto para a linha do assunto do e-mail.|  
StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | Especifica o destinatário ou destinatários do e-mail.  Introduza os nomes separados utilizando semi-colon(;) sem espaços.|
StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | Introduza os nomes VM que serão excluídas da operação de gestão Separe os nomes utilizando semi-colon(;) sem espaços. Os valores são sensíveis a maiúsculas e minúsculas e é suportado o caráter universal (asterisco).  O valor predefinido (asterisco) incluirá todos os grupos de recursos na subscrição.|
StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | Especifica a subscrição que contém as VMs a serem geridas por esta solução.  Tem de ser a mesma subscrição na qual a conta de Automatização da solução reside.|  
<br>

### <a name="schedules"></a>Agendas

Agenda | Descrição|
---------|------------|
StartByResourceGroup-Schedule-MS-Mgmt | Agendamento do runbook StartByResourceGroup, que executa o arranque de VMs geridas por esta solução. Quando criar, assume como fuso horário UTC.|
StopByResourceGroup-Schedule-MS-Mgmt | Agendamento do runbook StartByResourceGroup, que executa o encerramento de VMs geridas por esta solução. Quando criar, assume como fuso horário UTC.|

### <a name="credentials"></a>Credenciais

Credencial | Descrição|
-----------|------------|
O365Credential | Especifica uma conta de utilizador do Office 365 válida para enviar e-mails.  Só é necessária se a variável SendMailO365-IsSendEmail-MS-Mgmt estiver definida como **Verdadeiro**.

## <a name="configuration"></a>Configuração

Execute os passos seguintes para adicionar a solução Iniciar/Parar VMs fora do horário comercial [Preview] à sua conta de Automatização e, depois, configure as variáveis para a personalizar.

1. A partir do ecrã principal no portal do Azure, selecione o mosaico **Marketplace**.  Se o mosaico já não estiver afixado ao seu ecrã principal, a partir do painel de navegação à esquerda, selecione **Novo**.  
2. No painel Marketplace, escreva **Iniciar VM** na caixa de pesquisa e, em seguida, selecione a solução **Iniciar/Parar VMs fora do horário comercial [Preview]** nos resultados da pesquisa.  
3. No painel **Iniciar/Parar VMs fora do horário comercial [Preview]** da solução selecionada, reveja as informações de resumo e clique em **Criar**.  
4. É apresentado o painel **Adicionar Solução**, no qual lhe é pedido para configurar a solução antes de poder importá-la para a sua subscrição da Automatização.<br><br> ![Painel Adicionar Solução de Gestão de VMs](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5.  No painel **Adicionar Solução**, selecione **Área de Trabalho** e, aqui, selecione uma área de trabalho do OMS que esteja ligada à mesma subscrição do Azure da qual a conta de Automatização faz parte ou crie uma área de trabalho do OMS nova.  Se não tiver uma área de trabalho do OMS, pode selecionar **Criar Nova Área de Trabalho** e, no painel **Área de Trabalho do OMS**, faça o seguinte: 
   - Especifique um nome para a **Área de Trabalho do OMS**.
   - Selecione uma **Subscrição** à qual ligar ao escolher na lista pendente se a predefinição selecionada não é adequada.
   - Em **Grupo de Recursos**, pode criar um grupo de recursos novos ou selecionar um já existente.  
   - Selecione uma **Localização**.  Atualmente, as únicas localizações disponibilizadas para seleção são **Sudeste da Austrália**, **E.U.A. Leste**, **Sudeste Asiático** e **Europa Ocidental**.
   - Selecione um **Escalão de preço**.  A solução é oferecida em dois escalões, gratuito e escalão pago do OMS.  O escalão gratuito tem um limite quanto à quantidade de dados recolhidos diariamente, ao período de retenção e aos minutos de tempo de execução de trabalhos de runbook.  O escalão pago do OMS não tem limite quanto à quantidade de dados recolhidos diariamente.  

        > [!NOTE]
        > Embora o escalão pago Autónomo seja apresentado como uma opção, não é aplicável.  Se o selecionar e avançar com a criação desta solução na sua subscrição, a criação falha.  Esta opção será contemplada quando a solução for lançada oficialmente.<br>Se utilizar esta solução, a mesma só vai utilizar minutos de trabalhos de automatização e ingestão de registos.  A solução não acrescenta nós do OMS adicionais ao seu ambiente.  

6. Depois de fornecer as informações necessárias no painel **Área de Trabalho do OMS**, clique em **Criar**.  Enquanto as informações são confirmadas e a área de trabalho criada, pode acompanhar o progresso em **Notificações**, no menu.  Será reencaminhado para o painel **Adicionar Solução**.  
7. No painel **Adicionar Solução**, selecione **Conta de Automatização**.  Se estiver a criar uma área de trabalho do OMS novo, terá também de criar uma conta de Automatização nova, que vai ser associada à área de trabalho do OMS especificada anteriormente, incluindo a subscrição, o grupo de recursos e a região do Azure.  Pode selecionar **Criar conta de Automatização** e, no painel **Adicionar conta de Automatização**, indique o seguinte: 
  - No campo **Nome**, introduza o nome da conta de Automatização.

    Todas as outras opções são preenchidas automaticamente com base na área de trabalho do OMS selecionada e não podem ser modificadas.  O método de autenticação predefinido para os runbooks incluídos nesta solução é a conta Run As do Azure.  Depois de clicar em **OK**, as opções de configuração são validadas e a conta de Automatização é criada.  Pode acompanhar o progresso em **Notificações**, no menu. 

    Caso contrário, pode selecionar uma conta Run As de Automatização existente.  Tenha em atenção que a conta que selecionar não pode já estar associada a outra área de trabalho do OMS, caso contrário, será apresentada uma mensagem no painel para o informar.  Se já estiver associada, terá de selecionar uma conta Run As de Automatização diferente ou criar uma nova.<br><br> ![Conta de Automatização Já Associada à Área de Trabalho do OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Por fim, no painel **Adicionar Solução**, selecione **Configuração** e é apresentado o painel **Parâmetros**.  No painel **Parâmetros**, é-lhe pedido que:  
   - Especifique os **Nomes de ResourceGroup de Destino**, que é um nome de grupo de recursos que contém as VMs a serem geridas por esta solução.  Pode introduzir mais de um nome e separá-los por ponto e vírgula (os valores são sensíveis a maiúsculas e minúsculas).  Se quiser segmentar todas as VMs em todos os grupos de recursos da subscrição, a utilização de um caráter universal é suportada.
   - Selecione uma **Agenda**, que é uma data e hora recorrente para iniciar e parar as VMs no grupo ou grupos de recursos de destino.  Por predefinição, a agenda está configurada para o fuso horário UTC e selecionar uma região diferente não está disponível.  Se pretender configurar a agenda para o seu fuso horário específico depois de configurar a solução, consulte [modificar a agenda de arranque e encerramento](#modifying-the-startup-and-shutdown-schedule) abaixo.    

10. Depois de concluída a configuração das definições iniciais necessárias para a solução, selecione **Criar**.  Todas as definições serão validadas e, em seguida, tenta implementar a solução na sua subscrição.  Este processo pode demorar alguns segundos até ser concluído e pode acompanhar o progresso em **Notificações**, no menu. 

## <a name="collection-frequency"></a>Frequência da recolha

Os registos de trabalho de Automatização e os dados de fluxos de trabalhos são ingeridos no repositório do OMS de cinco em cinco minutos.  

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução de gestão de VMs, na área de trabalho do OMS, é adicionado o mosaico **Vista StartStopVM** ao dashboard do OMS.  Este mosaico apresenta uma contagem e uma representação gráfica dos trabalhos de runbooks da solução que foram iniciados e concluídos com êxito.<br><br> ![Mosaico Vista StartStopVM da Gestão de VMs](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Na sua conta de Automatização, pode aceder e gerir a solução ao selecionar o mosaico **Soluções** e, em seguida, no painel **Soluções**, selecionar a solução **Start-Stop-VM[Workspace]** na lista.<br><br> ![Lista de Soluções de Automatização](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

Ao selecionar a solução, é apresentado o painel da solução **Start-Stop-VM[Workspace]**, onde pode ver detalhes importantes, como o mosaico **StartStopVM**, como na área de trabalho do OMS, que mostra uma contagem e uma representação gráfica dos trabalhos de runbooks da solução que foram iniciados e concluídos com êxito.<br><br> ![Painel de Solução de VM de Automatização](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

A partir daqui pode também abrir a área de trabalho OMS e efetuar a análise dos registos de tarefa.  Basta clicar em **Todas as definições**, e no painel **Definições** selecione **Início Rápido** e, em seguida, no painel **Início Rápido** selecione **Portal OMS**.   Isto vai abrir um novo separador ou nova sessão do browser e apresentar a área de trabalho OMS associada à sua conta de automatização e subscrição.  


### <a name="configuring-e-mail-notifications"></a>Configurar as notificações por e-mail

Para ativar as notificações por e-mail quando os runbooks de início e fim de VMs são concluídos, terá de modificar a credencial **O365Credential** e, no mínimo, as variáveis seguintes:

 - SendMailO365-IsSendEmail-MS-Mgmt
 - StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
 - StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

Para configurar a credencial **O365Credential**, execute os passos seguintes:

1. Na sua conta de Automatização, clique em **Todas as Definições**, na parte superior da janela. 
2. No painel **Definições**, na secção **Recursos de Automatização**, selecione **Recursos**. 
3. No painel **Recursos**, selecione o mosaico **Credencial** e, no painel **Credencial**, selecione **O365Credential**.  
4. Introduza um nome de utilizador e uma palavra-passe do Office 365 válidos e clique em **Guardar** para guardar as alterações.  

Para configurar as variáveis realçadas anteriormente, execute os passos seguintes:

1. Na sua conta de Automatização, clique em **Todas as Definições**, na parte superior da janela. 
2. No painel **Definições**, na secção **Recursos de Automatização**, selecione **Recursos**. 
3. No painel **Recursos**, selecione o mosaico **Variáveis** e, no painel **Variáveis**, selecione a variável indicada acima e, depois, modifique o respetivo valor ao seguir a descrição do mesmo especificada na secção [Variável](##variables) anterior.  
4. Clique em **Guardar** para guardar as alterações à variável.   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>Modificar a agenda de arranque e encerramento

Gerir a agenda de arranque e encerramento nesta solução segue os mesmos passos descritos em [Agendar um runbook na Automatização do Azure](automation-schedules.md).  Lembre-se de que não é possível modificar a configuração de agenda.  Vai ter de desativar a agenda existente e, em seguida, crie uma nova e ligue ao runbook **StartByResourceGroup-MS-Mgmt-VM** ou **StopByResourceGroup-MS-Mgmt-VM** ao qual pretende que a agenda se aplique.   

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
Localizar trabalhos para o runbook StartVM que foram concluídos com êxito | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; measure count() by JobId_g|
Localizar trabalhos para o runbook StopVM que foram concluídos com êxito | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; measure count() by JobId_g
Mostrar estado do trabalho ao longo do tempo para runbooks StartVM e StopVM | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") | measure Count() by ResultType interval 1day|

## <a name="removing-the-solution"></a>Remover a solução

Se decidir que já não necessita de utilizar a solução qualquer adicional, pode eliminá-la a conta de automatização.  A eliminar a solução irá remover apenas os runbooks, não irá eliminar os agendamentos ou variáveis que foram criadas quando a solução foi adicionada.  Esses recursos, terá de eliminar manualmente se não estiver a utilizá-los com outros runbooks.  

Para eliminar a solução, execute os seguintes passos:

1.  A partir da sua conta de automatização, selecione o **soluções** mosaico.  
2.  No **soluções** painel, selecione a solução **Start-Stop-VM [área de trabalho]**.  No **VMManagementSolution [área de trabalho]** painel, a partir do menu, clique em **eliminar**.<br><br> ![Eliminar a solução de gestão VM](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  No **Eliminar solução** janela, confirme que pretende eliminar a solução.
4.  Verificar as informações e a solução é eliminada, pode controlar o progresso em **notificações** no menu.  Será devolvido para o **VMManagementSolution [área de trabalho]** painel depois de iniciado o processo de remoção de solução.  

A conta de automatização e a área de trabalho OMS não são eliminados como parte deste processo.  Se não pretender manter a área de trabalho do OMS, terá de eliminar manualmente.  Isto pode ser conseguido também do portal do Azure.   A partir da home ecrã no portal do Azure, selecione **Log Analytics** e, em seguida, no **Log Analytics** painel, selecione a área de trabalho e clique em **eliminar** no menu no Painel de definições da área de trabalho.  
      
## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como construir consultas de pesquisa diferentes e rever os registos dos trabalhos da Automatização com o Log Analytics, veja [Log searches in Log Analytics (Pesquisas de registos no Log Analytics)](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre a execução dos runbooks, como monitorizar trabalhos de runbooks e outros detalhes técnicos, veja [Track a runbook job (Acompanhar um trabalho de runbook)](automation-runbook-execution.md)
- Para saber mais sobre o OMS Log Analytics e as origens das coleções de dados, veja [Collecting Azure storage data in Log Analytics overview (Descrição geral da Recolha de dados do armazenamento do Azure no Log Analytics)](../log-analytics/log-analytics-azure-storage.md)






   


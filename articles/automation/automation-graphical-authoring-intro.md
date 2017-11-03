---
title: "A criação de gráficos na automatização do Azure | Microsoft Docs"
description: "Criação de gráficos permite-lhe criar runbooks de automatização do Azure sem trabalhar com o código. Este artigo fornece uma introdução à criação de gráficos e todos os detalhes necessários para começar a criar um runbook gráfico."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 4b6f840c-e941-4293-a728-b33407317943
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 137e8503b9759136510db59700c3032853246c89
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="graphical-authoring-in-azure-automation"></a>Gráfico de criação na automatização do Azure
## <a name="introduction"></a>Introdução
Criação gráfica permite-lhe criar runbooks de automatização do Azure sem complexidades do código subjacente do Windows PowerShell ou o fluxo de trabalho do PowerShell. Adicionar atividades para a tela de uma biblioteca de cmdlets e runbooks, ligá-las em conjunto e configurar para formar um fluxo de trabalho.  Se alguma vez já trabalhou com o System Center Orchestrator ou o Service Management Automation (SMA), em seguida, este deverá ter um aspeto familiar.   

Este artigo fornece uma introdução à criação de gráficos e conceitos que tem de começar a utilizar na criação de um runbook gráfico.

## <a name="graphical-runbooks"></a>Runbooks gráficos
Todos os runbooks na automatização do Azure são fluxos de trabalho do Windows PowerShell.  Os runbooks do gráfico e o fluxo de trabalho do PowerShell gráfico gerar código do PowerShell que é executado pelos trabalhadores de automatização, mas não é capazes de ver ou modificar diretamente.  Um runbook gráfico pode ser convertido para um runbook de fluxo de trabalho do PowerShell gráfico e vice-versa, mas não pode ser convertidos para um runbook textual. Um runbook textual existente não pode ser importado para o editor gráfico.  

## <a name="overview-of-graphical-editor"></a>Descrição geral do editor gráfico
Pode abrir o editor gráfico no portal do Azure, criar ou editar um runbook gráfico.

![Área de gráfica](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

As secções seguintes descrevem os controlos no editor gráfico.

### <a name="canvas"></a>Tela
A tela é onde criar o runbook.  Adicionar atividades a partir de nós no controlo da biblioteca para o runbook e ligue-o com ligações para definir a lógica do runbook.

Pode utilizar os controlos na parte inferior da tela para ampliar e reduzir.

![Área de gráfica](media/automation-graphical-authoring-intro/runbook-canvas-controls.png)

### <a name="library-control"></a>Controlo de biblioteca
O controlo da biblioteca é onde poderá selecionar [atividades](#activities) para adicionar ao runbook.  Adicione-os para a tela onde ligá-las para outras atividades.  Inclui quatro secções descritas na seguinte tabela.

| Section | Descrição |
|:--- |:--- |
| Cmdlets |Inclui todos os cmdlets que pode ser utilizados no runbook.  Cmdlets estão organizados por módulo.  Todos os módulos que têm instalado na sua conta de automatização estará disponíveis. |
| Runbooks |Inclui os runbooks na sua conta de automatização. Estes runbooks podem ser adicionados para a tela, para ser utilizado como runbooks subordinados. São apresentados apenas os runbooks do mesmo tipo de principal como o runbook que está a ser editado; para gráfico runbooks apenas baseada no PowerShell runbooks são apresentados, enquanto para runbooks do fluxo de trabalho do PowerShell gráfico são apresentados apenas PowerShell fluxo de trabalho baseada em runbooks. |
| Elementos |Inclui o [recursos de automatização](http://msdn.microsoft.com/library/dn939988.aspx) na sua conta de automatização que pode ser utilizada no runbook.  Quando adiciona um recurso a um runbook, irá adicionar uma atividade de fluxo de trabalho que obtém o elemento selecionado.  No caso de recursos de variável, pode selecionar se pretende adicionar uma atividade para obter a variável ou defina a variável. |
| Controlo do Runbook |Inclui atividades de controlo do runbook que podem ser utilizadas no runbook atual. A *junção* demora várias entradas e aguarda até que todos os foram concluídas antes de continuar o fluxo de trabalho. A *código* atividade é executada uma ou mais linhas de código do PowerShell ou o fluxo de trabalho do PowerShell, dependendo do tipo de runbook gráfico.  Pode utilizar esta atividade para código personalizado ou para uma funcionalidade que é difícil alcançar com outras atividades. |

### <a name="configuration-control"></a>Controlo de configuração
O controlo de configuração é onde pode fornecer detalhes de um objeto selecionado na tela. As propriedades disponíveis neste controlo dependerão do tipo de objeto selecionado.  Quando seleciona uma opção no controlo da configuração, será aberto em painéis adicionais para fornecer informações adicionais.

### <a name="test-control"></a>Controlo de teste
O controlo de teste não é apresentado quando o editor gráfico é iniciado pela primeira vez. É aberto quando interativamente [testar um runbook gráfico](#graphical-runbook-procedures).  

## <a name="graphical-runbook-procedures"></a>Procedimentos de runbook gráfico
### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportar e importar um runbook gráfico
Só pode exportar a versão publicada do runbook gráfico.  Se o runbook ainda não tiver sido publicado, a **exportação publicada** botão será desativado.  Ao clicar no **exportação publicada** botão, o runbook é transferido para o seu computador local.  O nome do ficheiro corresponde ao nome do runbook com um *graphrunbook* extensão.

![Exportar publicada](media/automation-graphical-authoring-intro/runbook-export.png)

Pode importar um ficheiro de runbook gráfico ou fluxo de trabalho do PowerShell gráfico selecionando o **importar** opção ao adicionar um runbook.   Quando seleciona o ficheiro de importação, pode manter o mesmo **nome** ou forneça um novo.  O campo de tipo de Runbook apresentará o tipo de runbook depois que avalia o ficheiro selecionado e se tentar selecionar um tipo diferente que não está correto, uma mensagem será apresentada salientar existem potenciais conflitos e durante a conversão, podem existir erros de sintaxe.  

![Importar o runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testar um runbook gráfico
Pode testar a versão de rascunho de um runbook no portal do Azure ao abandonar o fileparser a versão publicada do runbook inalterados, ou pode testar um runbook novo antes de foi publicado. Isto permite-lhe verificar se o runbook está a funcionar corretamente antes de substituir a versão publicada. Quando testa um runbook, o runbook de rascunho é executado e todas as ações que executar são concluídas. Nenhum histórico da tarefa é criado, mas o resultado é apresentado no painel de resultados do teste. 

Abra o controlo de teste para um runbook, abrindo o runbook para editar e, em seguida, clique em de **painel de teste** botão.

![Botão de painel de teste](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

O controlo de teste irá solicitar quaisquer parâmetros de entrada e pode iniciar o runbook ao clicar no **iniciar** botão.

![Botões de controlo de teste](media/automation-graphical-authoring-intro/runbook-test-start.png)

### <a name="publishing-a-graphical-runbook"></a>Publicar um runbook gráfico
Cada runbook na automatização do Azure tem um rascunho e uma versão publicada. Apenas a versão publicada está disponível para ser executado, e apenas a versão de rascunho pode ser editada. A versão publicada não é afetada por quaisquer alterações à versão de rascunho. Quando estiver pronta para estar disponível a versão de rascunho, em seguida, publica-o que substitui a versão publicada com a versão de rascunho.

Pode publicar um runbook gráfico abrindo o runbook para editar e, em seguida, clicando no **publicar** botão.

![Botão Publicar](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

Quando um runbook ainda não foi publicado, tem um Estado de **novo**.  Quando este for publicado, tem um Estado de **publicada**.  Se editar o runbook depois foi publicado e as versões de rascunho e a publicada são diferentes, o runbook tiver um Estado de **na edição**.

![Estados de Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png) 

Também tem a opção para reverter para a versão publicada do runbook.  Isto ausente emite quaisquer alterações efetuadas uma vez que o runbook foi publicado pela última vez e substitui a versão de rascunho do runbook com a versão publicada.

![Reverter para botão publicado](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)

## <a name="activities"></a>Atividades
As atividades são os blocos modulares de um runbook.  Uma atividade pode ser um cmdlet do PowerShell, um runbook subordinado ou uma atividade de fluxo de trabalho.  Adicionar uma atividade ao runbook ao clicar nela botão direito do rato no controlo da biblioteca e selecionando **adicionar à tela**.  Pode, em seguida, clique e arraste a atividade para colocá-lo em qualquer lugar na tela que pretender.  A localização do da atividade na tela não que tenha efeito a operação do runbook de qualquer forma.  Pode esquema runbook no entanto, pode encontrá-lo mais adequado visualizar a conclusão da operação. 

![Adicionar à tela](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Seleccione a actividade na tela, para configurar as respetivas propriedades e os parâmetros no painel de configuração.  Pode alterar o **etiqueta** da atividade para algo que é descritiva para si.  O cmdlet original ainda está a ser executado, basta está a alterar o respetivo nome a apresentar que será utilizado no editor gráfico.  A etiqueta tem de ser exclusiva dentro do runbook. 

### <a name="parameter-sets"></a>Conjuntos de parâmetros
Um conjunto de parâmetros define os parâmetros obrigatórios e opcionais que irão aceitar valores de um cmdlet específico.  Todos os cmdlets de ter, pelo menos, um parâmetro definido e alguns têm vários.  Se um cmdlet tem vários conjuntos de parâmetros, em seguida, tem de selecionar quais um utilizará antes de poder configurar parâmetros.  Os parâmetros que pode configurar irão depender o conjunto de parâmetros que escolher.  Pode alterar o conjunto de parâmetros utilizado por uma atividade selecionando **parâmetro definido** e selecionar outro conjunto.  Neste caso, os valores de parâmetro que configurou serão perdidos.

No exemplo seguinte, o cmdlet Get-AzureRmVM tem três conjuntos de parâmetros.  Não é possível configurar os valores de parâmetros depois de selecionar um dos conjuntos de parâmetros.  O conjunto de parâmetros ListVirtualMachineInResourceGroupParamSet é para devolver a todas as máquinas virtuais num grupo de recursos e tem um único parâmetro opcional.  É o GetVirtualMachineInResourceGroupParamSet para especificar a máquina virtual que pretende devolver e tem dois é obrigatória e um parâmetro opcional.

![Conjunto de parâmetros](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valores de parâmetros
Quando especificar um valor para um parâmetro, selecione uma origem de dados para determinar a forma como o valor será especificado.  Os valores válidos para este parâmetro dependem as origens de dados que estão disponíveis para um parâmetro específico.  Por exemplo, um valor nulo não poderá ser uma opção disponível para um parâmetro que não permite valores nulos.

| Origem de dados | Descrição |
|:--- |:--- |
| Valor constante |Escreva um valor para o parâmetro.  Isto só está disponível para os seguintes tipos de dados: Int32, Int64, String, booleano, DateTime, comutador. |
| Saída da atividade |Resultado de uma atividade que precede a atividade atual no fluxo de trabalho.  Serão apresentadas todas as atividades válidas.  Selecione apenas a atividade para utilizar o resultado para o valor do parâmetro.  Se a atividade produz um objeto com várias propriedades, em seguida, pode escrever nome da propriedade depois de selecionar a atividade. |
| Entrada do Runbook |Selecione um parâmetro de entrada do runbook como entrada para o parâmetro da atividade. |
| Recurso de variável |Selecione uma variável de automatização como entrada. |
| Recurso de credenciais |Selecione uma credencial de automatização como entrada. |
| Recurso de certificado |Selecione um certificado da automatização como entrada. |
| Recurso de ligação |Selecione uma ligação de automatização como entrada. |
| Expressão do PowerShell |Especifique simples [expressão do PowerShell](#powershell-expressions).  A expressão será avaliada antes da atividade e o resultado utilizado para o valor do parâmetro.  Pode utilizar variáveis para fazer referência à saída de uma atividade ou um parâmetro de entrada do runbook. |
| Não configurado |Limpa qualquer valor que foi configurada anteriormente. |

#### <a name="optional-additional-parameters"></a>Parâmetros adicionais opcionais
Todos os cmdlets terá a opção para fornecer parâmetros adicionais.  Estes são os parâmetros comuns do PowerShell ou outros parâmetros personalizados.  É-lhe apresentada uma caixa de texto, onde pode fornecer os parâmetros que utilizam a sintaxe do PowerShell.  Por exemplo, para utilizar o **verboso** parâmetro comum, tem de especificar **"-Verbose: $True"**.

### <a name="retry-activity"></a>Repita a atividade
**Comportamento de tentativas** permite uma atividade para ser executado múltiplas vezes até uma determinada condição for cumprida, muito como um ciclo.  Pode utilizar esta funcionalidade para as atividades que deve ser executada várias vezes, são propensas ao erro e pode precisar de mais do que um tentativa para o sucesso ou testar as informações de saída da atividade de dados válido.    

Quando ativar a repetição de uma atividade, pode definir um atraso e uma condição.  O atraso é o tempo (medido em segundos ou minutos) que o runbook aguardará antes de executar novamente a atividade.  Se não for especificado nenhum atraso, em seguida, a atividade será executado novamente imediatamente depois de terminar. 

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-delay.png)

A condição de repetição é uma expressão do PowerShell que é avaliada depois de cada vez que a atividade é executada.  Se a expressão é resolvido para verdadeiro, em seguida, a atividade é executada novamente.  Se a expressão é resolvido para False, em seguida, a atividade não é executado novamente e o runbook passa para a atividade seguinte. 

![Atraso de repetição de atividade](media/automation-graphical-authoring-intro/retry-condition.png)

A condição de tentativas pode utilizar uma variável chamada $RetryData que fornece acesso a informações sobre as repetições de atividade.  Esta variável tem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| NumberOfAttempts |Número de vezes que a atividade tiver sido executada. |
| Saída |Resultado da última execução da atividade. |
| TotalDuration |Tempo decorrido desde que a atividade foi iniciada pela primeira vez. |
| StartedAt |Hora em formato UTC que a atividade foi iniciado pela primeira vez. |

Seguem-se exemplos de atividade Repita condições.

    # Run the activity exactly 10 times.
    $RetryData.NumberOfAttempts -ge 10 

    # Run the activity repeatedly until it produces any output.
    $RetryData.Output.Count -ge 1 

    # Run the activity repeatedly until 2 minutes has elapsed. 
    $RetryData.TotalDuration.TotalMinutes -ge 2

Depois de configurar uma condição de tentativas de uma atividade, a atividade inclui dois ajudas visuais para relembrá.  Um é apresentado na atividade e o outro é quando reveja a configuração da atividade.

![Indicadores de Visual de repetição de atividade](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controlo de Script de fluxo de trabalho
Um controlo de código é uma atividade especial que aceita o script do PowerShell ou o fluxo de trabalho do PowerShell, dependendo do tipo de runbook gráfico que está a ser criado para fornecer a funcionalidade que pode caso contrário, não estar disponível.  Não pode aceitar parâmetros, mas pode utilizar variáveis para a atividade saída e o runbook os parâmetros de entrada.  Qualquer saída da atividade é adicionada no barramento de dados, exceto não se tiver nenhuma saída ligação caso em que é adicionado à saída do runbook.

Por exemplo, o seguinte código efetua cálculos de data utilizando uma variável de entrada do runbook chamada $NumberOfDays.  Em seguida, envia um período de tempo calculado data como saída para ser utilizado por atividades subsequentes no runbook.

    $DateTimeNow = (Get-Date).ToUniversalTime()
    $DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
    $DateTimeStart


## <a name="links-and-workflow"></a>Fluxo de trabalho e de ligações
A **ligação** um runbook gráfico ligam duas atividades.  É apresentado na tela como uma seta a apontar da atividade de origem para a atividade de destino.  As atividades são executadas na direção de seta com a atividade de destino iniciar após a conclusão da atividade de origem.  

### <a name="create-a-link"></a>Criar uma ligação
Crie uma ligação entre duas atividades, selecionando a atividade de origem e clicando no círculo na parte inferior da forma.  Arraste a seta para a atividade de destino e a versão.

![Criar uma ligação](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Selecione a ligação para configurar as respetivas propriedades no painel de configuração.  Isto irá incluir o tipo de ligação que está descrito na seguinte tabela.

| Tipo de ligação | Descrição |
|:--- |:--- |
| Pipeline |A atividade de destino é executada uma vez para cada objeto de saída da atividade de origem.  A atividade de destino não é executado se a atividade de origem resulta em nenhuma saída.  Saída da atividade de origem está disponível como um objeto. |
| Sequência |A atividade de destino é executada apenas uma vez.  Recebe uma matriz de objetos da atividade de origem.  Saída da atividade de origem está disponível como uma matriz de objetos. |

### <a name="starting-activity"></a>Atividade de partida
Um runbook gráfico irá começar a utilizar quaisquer atividades que não tenham uma ligação de entrada.  Isto, muitas vezes, será apenas uma atividade que seria agem como a atividade de partida para o runbook.  Se várias atividades não dispõe de uma ligação de entrada, o runbook será iniciada, executando a aplicação em paralelo.  Em seguida, seguirá as ligações para executar outras atividades como cada uma for concluída.

### <a name="conditions"></a>Condições
Quando especificar uma condição de uma ligação, a atividade de destino apenas é executada se a condição é resolvido para verdadeiro.  Normalmente, utilizará uma variável de $ActivityOutput numa condição para obter o resultado da atividade de origem.  

Para uma ligação de pipeline, especifique uma condição para um único objeto e a condição for avaliada para cada objeto de saída pela atividade de origem.  A atividade de destino é executada, em seguida, para cada objeto que satisfaça a condição.  Por exemplo, com uma atividade de origem de Get-AzureRmVm, a seguinte sintaxe pode ser utilizada para uma ligação de pipeline condicional para obter apenas as máquinas virtuais no grupo de recursos com o nome *Group1*.  

    $ActivityOutput['Get Azure VMs'].Name -match "Group1"

Para uma ligação de sequência, a condição é avaliada apenas uma vez, uma vez que uma única matriz é devolvida que contém todos os objetos saída da atividade de origem.  Por este motivo, uma ligação de sequência não pode ser utilizada para filtragem como uma ligação de pipeline mas basta irá determinar se é ou não a atividade seguinte é executada. Tome por exemplo o seguinte conjunto de atividades no nosso runbook iniciar VM.<br> ![Ligação condicional com sequências](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)<br>
Existem três ligações de sequência diferente que estiver a verificar os valores fornecidos para duas runbook parâmetros de entrada que representa o nome da VM e o nome do grupo de recursos para determinar qual é a ação adequada a tomar - iniciar uma única VM, iniciar todas as VMs no grupo de recurso, ou todas as VMs numa subscrição.  Para a ligação de sequência entre ligar ao Azure e obter única VM, eis a lógica de condição:

    <# 
    Both VMName and ResourceGroupName runbook input parameters have values 
    #>
    (
    (($VMName -ne $null) -and ($VMName.Length -gt 0))
    ) -and (
    (($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
    )

Quando utilizar uma ligação condicional, os dados disponíveis da atividade de origem para outras atividades nessa sucursal serão filtrados pela condição.  Se uma atividade é a origem para várias ligações, os dados disponíveis para as atividades em cada sucursal dependerão a condição na ligação nessa sucursal.

Por exemplo, o **Start-AzureRmVm** atividade no runbook abaixo inicia todas as máquinas virtuais.  Tem duas ligações condicionais.  A primeira ligação condicional utiliza a expressão *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - eq $true* para filtrar se a atividade de Start-AzureRmVm concluída com êxito.  O segundo utiliza a expressão *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true* para filtrar se a atividade de Start-AzureRmVm Falha ao iniciar a máquina virtual.  

![Exemplo de ligação condicional](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualquer atividade que segue a primeira ligação e utiliza a saída da atividade de Get-AzureVM só irá obter as máquinas virtuais que foram iniciadas ao tempo que Get-AzureVM foi executada.  Qualquer atividade que segue na segunda ligação só irá obter as máquinas virtuais que foram interrompidas momento em que Get-AzureVM foi executada.  Qualquer atividade seguintes na ligação terceira irá obter todas as máquinas virtuais, independentemente da respetiva estado de execução.

### <a name="junctions"></a>Junctions
Uma junção é uma atividade especial que irá esperar até que concluíram todos os ramos de entrada.  Isto permite-lhe executar múltiplas atividades em paralelo e certifique-se de que todos os tenham concluído antes de continuar.

Enquanto uma junção pode ter um número ilimitado de ligações a receber, não mais do que um dessas ligações pode ser um pipeline.  O número de ligações de sequência a receber não é restrita.  Terá permissão para criar a junção com várias ligações de pipeline de entrada e guarde o runbook, mas ocorrerá uma falha quando for executada.

O exemplo abaixo faz parte de um runbook que inicia um conjunto de máquinas virtuais em simultâneo transferir patches a aplicar a essas máquinas.  Uma junção é utilizada para se certificar de que ambos os processos sejam concluídos antes do runbook continua.

![Junção](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciclos
Um ciclo é quando uma ligações de atividade de destino para a atividade de origem ou de outra atividade que eventualmente ligações apoiam a respetiva origem.  Atualmente ciclos não são permitidos na criação de gráficos.  Se o runbook tiver um ciclo, irá guardar corretamente mas irá receber um erro quando é executada.

![Ciclo de](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Partilha de dados entre atividades
Todos os dados de saída por uma atividade com uma ligação de saída são escritos para o *barramento de dados* para o runbook.  Qualquer atividade no runbook pode utilizar dados no barramento de dados para preencher os valores dos parâmetros ou incluir no código de script.  Uma atividade pode aceder a saída de qualquer atividade anterior no fluxo de trabalho.     

Como os dados são escritos no barramento de dados dependem do tipo de ligação na atividade.  Para um **pipeline**, os dados são o resultado como objetos de múltiplos.  Para um **sequência** é a ligação, os dados de saída como uma matriz.  Se houver apenas um valor, será resultado como uma matriz de elemento único.

Pode aceder a dados no barramento de dados utilizando um dos dois métodos.  Primeiro está a utilizar um **saída da atividade** origem de dados para preencher um parâmetro de outra atividade.  Se o resultado é um objeto, pode especificar uma única propriedade.

![Saída da atividade](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Também pode obter o resultado de uma atividade num **expressão do PowerShell** origem de dados ou a partir de um **Script de fluxo de trabalho** atividade com uma variável de ActivityOutput.  Se o resultado é um objeto, pode especificar uma única propriedade.  Variáveis de ActivityOutput use a seguinte sintaxe.

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName 

### <a name="checkpoints"></a>Pontos de verificação
Pode definir [pontos de verificação](automation-powershell-workflow.md#checkpoints) num runbook gráfico fluxo de trabalho do PowerShell selecionando *runbook de ponto de verificação* em qualquer atividade.  Isto faz com que um ponto de verificação ser definida depois da atividade é executada.

![Ponto de verificação](media/automation-graphical-authoring-intro/set-checkpoint.png)

Pontos de verificação só estão ativados em runbooks do fluxo de trabalho do PowerShell gráfico, não se encontra disponível em runbooks gráficos.  Se o runbook utiliza cmdlets do Azure, deve seguir qualquer atividade checkpointed com um Add-AzureRMAccount no caso do runbook está suspenso e reinicia a partir de uma função de trabalho diferentes neste ponto de verificação. 

## <a name="authenticating-to-azure-resources"></a>Autenticação nos recursos do Azure
Os Runbooks na automatização do Azure que gerir recursos do Azure irá exigir a autenticação no Azure.  O [conta Run As](automation-offering-get-started.md#creating-an-automation-account) (também referido como um principal de serviço) é o método predefinido para aceder aos recursos do Azure Resource Manager na sua subscrição com runbooks de automatização.  Pode adicionar esta funcionalidade para um runbook gráfico adicionando o **AzureRunAsConnection** recurso de ligação, que está a utilizar o PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet, e [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet para a tela. Esta situação é ilustrada no seguinte exemplo.<br>![Execute como atividades de autenticação](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)<br>
A atividade obter ligação Run As (ou seja, Get-AutomationConnection), está configurado com uma origem de dados de valor constante denominada AzureRunAsConnection.<br>![Executar como configuração da ligação](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)<br>
A atividade seguinte, Add-AzureRmAccount, adiciona autenticada conta Run as para utilização no runbook.<br>
![Conjunto de parâmetros de Add-AzureRmAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)<br>
Para os parâmetros **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, e **TENANTID** terá de especificar o nome da propriedade para o caminho do campo porque a atividade produz um objeto com várias propriedades.  Caso contrário, quando executar o runbook, ocorrerá uma falha tentar autenticar.  Este é o que precisa, no mínimo, para autenticar o runbook com a conta Run As.

Para manter efeitos de compatibilidade para os subscritores que tem criado uma conta de automatização utilizando um [conta de utilizador do Azure AD](automation-create-aduser-account.md) para gerir a implementação clássica do Azure ou para os recursos do Azure Resource Manager, o método de autenticação é o cmdlet Add-AzureAccount com um [recurso de credencial](automation-credentials.md) que representa um utilizador do Active Directory com acesso à conta do Azure.

Pode adicionar esta funcionalidade para um runbook gráfico adicionando um recurso de credencial para a tela seguida por uma atividade de Add-AzureAccount.  Adicionar-AzureAccount utiliza a atividade de credencial de entrada.  Esta situação é ilustrada no seguinte exemplo.

![Atividades de autenticação](media/automation-graphical-authoring-intro/authentication-activities.png)

Tem de autenticar no início do runbook e depois de cada ponto de verificação.  Isto significa que adicionar uma atividade de adição Add-AzureAccount após qualquer atividade Checkpoint-Workflow. Não precisa de uma atividade de credencial de adição, uma vez que pode utilizar o mesmo 

![Saída da atividade](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook entrada e saída
### <a name="runbook-input"></a>Entrada do Runbook
Um runbook pode necessitar de entrada de um utilizador quando são iniciados runbook através do portal do Azure ou a partir de outro runbook se o atual é utilizada como elemento subordinado.
Por exemplo, se tiver um runbook que cria uma máquina virtual, poderá ter de fornecer informações tais como o nome da máquina virtual e outras propriedades sempre que iniciar o runbook.  

Aceitar entrada de um runbook, definindo um ou mais parâmetros de entrada.  Fornecer valores para estes parâmetros sempre que o runbook for iniciado.  Quando inicia um runbook com o portal do Azure, que irá solicitar-lhe fornecer valores para cada um dos parâmetros de entrada do runbook.

Pode aceder a parâmetros de entrada para um runbook ao clicar no **entrada e saída** botão na barra de ferramentas do runbook.  

![Saída de entrada do Runbook](media/automation-graphical-authoring-intro/runbook-edit-input-output.png) 

Esta ação abre o **entrada e saída** controlo onde pode editar um parâmetro de entrada existente ou crie um novo ao clicar em **Adicionar entrada**. 

![Adicionar a entrada](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Cada parâmetro de entrada é definido pelas propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |O nome exclusivo do parâmetro.  Isto só pode conter carateres alfanuméricos e não pode conter um espaço. |
| Descrição |Uma descrição opcional para o parâmetro de entrada. |
| Tipo |Tipo de dados esperado para o valor do parâmetro.  O portal do Azure irá fornecer um controlo adequado para o tipo de dados para cada parâmetro, quando for solicitada entrada. |
| Obrigatório |Especifica se deve ser fornecido um valor para o parâmetro.  Não é possível iniciar o runbook se não fornecer um valor para cada parâmetro obrigatório que não tem um valor predefinido especificado. |
| Valor predefinido |Especifica que valor é utilizado para o parâmetro se não for fornecido.  Isto pode ser nulo ou um valor específico. |

### <a name="runbook-output"></a>Resultado do runbook
Dados criados por qualquer atividade que não tem uma ligação de saída serão adicionados ao [saída do runbook](http://msdn.microsoft.com/library/azure/dn879148.aspx).  O resultado é guardado com a tarefa de runbook e está disponível para um runbook principal quando o runbook é utilizado como elemento subordinado.  

## <a name="powershell-expressions"></a>Expressões de PowerShell
Uma das vantagens de criação de gráficos é fornecer-lhe a capacidade de criar um runbook com o mínimo de conhecimento do PowerShell.  Atualmente, é necessário saber pouco do PowerShell embora para preencher determinados [valores de parâmetro](#activities) e para a definição [ligação condições](#links-and-workflow).  Esta secção fornece uma introdução rápida aos expressões de PowerShell para os utilizadores que não deve estar familiarizados com o mesmo.  Detalhes completos do PowerShell estão disponíveis em [Scripting com o Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). 

### <a name="powershell-expression-data-source"></a>Origem de dados de expressão do PowerShell
Pode utilizar uma expressão do PowerShell como uma origem de dados para povoar o valor de um [parâmetro de atividade](#activities) com os resultados de algum código do PowerShell.  Isto pode ser uma única linha de código que efetua algumas função simple ou várias linhas que efetuar algumas lógica complexa.  Qualquer saída de um comando que não está atribuído a uma variável é de saída para o valor do parâmetro. 

Por exemplo, o seguinte comando teria de saída a data atual. 

    Get-Date

Os seguintes comandos criar uma cadeia a partir da data atual e atribua-a uma variável.  O conteúdo da variável, em seguida, é enviado para a saída 

    $string = "The current date is " + (Get-Date)
    $string

Os seguintes comandos avaliar a data atual e devolvem uma cadeia que indica se o dia atual é um fim de semana ou um dia da semana. 

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }


### <a name="activity-output"></a>Saída da atividade
Para utilizar o resultado de uma atividade anterior no runbook, utilize a variável de $ActivityOutput com a seguinte sintaxe.

    $ActivityOutput['Activity Label'].PropertyName

Por exemplo, pode ter uma atividade com uma propriedade que requer que o nome de uma máquina virtual caso em que pode utilizar a seguinte expressão.

    $ActivityOutput['Get-AzureVm'].Name

Se a propriedade necessária a máquina virtual de objeto em vez de apenas uma propriedade, iria devolver todo o objeto com a seguinte sintaxe.

    $ActivityOutput['Get-AzureVm']

Também pode utilizar o resultado de uma atividade numa expressão mais complexa, tais como o seguinte concatena texto para o nome da máquina virtual.

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### <a name="conditions"></a>Condições
Utilize [operadores de comparação](https://technet.microsoft.com/library/hh847759.aspx) para comparar valores ou a determinar se um valor corresponde a um padrão especificado.  Uma comparação devolve um valor de $true ou $false.

Por exemplo, a seguinte condição determina se a máquina virtual de uma atividade com o nome *Get-AzureVM* está atualmente *parado*. 

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

A seguinte condição verifica se a mesma máquina virtual está em qualquer Estado diferente de *parado*.

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

Pode associar várias condições utilizando um [operador lógico](https://technet.microsoft.com/library/hh847789.aspx) como **- e** ou **- ou**.  Por exemplo, a seguinte condição verifica se a mesma máquina virtual no exemplo anterior está num Estado de *parado* ou *parar*.

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping") 


### <a name="hashtables"></a>Tabelas hash
[Tabelas hash](http://technet.microsoft.com/library/hh847780.aspx) são pares nome/valor que são úteis para devolver um conjunto de valores.  Propriedades de determinadas atividades podem esperar uma tabela hash em vez de um valor simple.  Também pode ver como tabela hash que referida como um dicionário. 

Criar uma tabela hash com a seguinte sintaxe.  Uma tabela hash pode conter qualquer número de entradas mas cada é definida por um nome e valor.

    @{ <name> = <value>; [<name> = <value> ] ...}

Por exemplo, a seguinte expressão cria uma tabela hash para ser utilizado na origem de dados para um parâmetro de atividade que espera uma tabela hash com os valores para uma pesquisa na internet.

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

O exemplo seguinte utiliza o resultado de uma atividade chamado *obter ligação do Twitter* para preencher uma tabela hash.

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## <a name="next-steps"></a>Passos Seguintes
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md) 
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para compreender como autenticar utilizando a conta de automatização Run As, consulte [configurar Azure conta Run As](automation-sec-configure-azure-runas-account.md)


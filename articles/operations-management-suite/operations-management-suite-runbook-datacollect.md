---
title: "Recolha de dados de análise de registos com um runbook na automatização do Azure | Microsoft Docs"
description: "Passo a passo tutorial que explica a criação de um runbook na automatização do Azure para recolher dados para o repositório do OMS para análise pelo análise de registos."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 59f674c9c6404da7f5384539189f41a4ba1a939a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Recolher dados de análise de registos com um runbook de automatização do Azure
Pode recolher uma quantidade significativa de dados da análise de registos a partir de uma variedade de origens, incluindo [origens de dados](../log-analytics/log-analytics-data-sources.md) nos agentes e também [dados recolhidos a partir do Azure](../log-analytics/log-analytics-azure-storage.md).  Há um cenários embora onde que precisa para recolher dados que não se encontra acessível através destas origens padrão.  Nestes casos, pode utilizar o [API de Recoletor de dados de HTTP](../log-analytics/log-analytics-data-collector-api.md) para escrever dados para análise de registos a partir de qualquer cliente de REST API.  Um método comum para efetuar a recolha de dados está a utilizar um runbook na automatização do Azure.   

Este tutorial explica o processo para criar e agendar um runbook na automatização do Azure para escrever dados para análise de registos.


## <a name="prerequisites"></a>Pré-requisitos
Este cenário requer os seguintes recursos configurados na sua subscrição do Azure.  Tanto podem ser uma conta gratuita.

- [Área de trabalho de análise de registo](../log-analytics/log-analytics-get-started.md).
- [Conta de automatização do Azure](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Descrição geral do cenário
Para este tutorial, irá escrever um runbook que recolhe informações sobre as tarefas de automatização.  Os Runbooks na automatização do Azure estão implementados com o PowerShell, pelo que irá começar ao escrever e testar um script no editor de automatização do Azure.  Depois de verificar que está a recolher as informações necessárias, irá escrever dados para análise de registos e verificar o tipo de dados personalizados.  Por fim, irá criar uma agenda para iniciar o runbook em intervalos regulares.

> [!NOTE]
> Pode configurar a automatização do Azure para enviar as informações da tarefa ao Log Analytics sem este runbook.  Este cenário é principalmente utilizado para suportar o tutorial e recomenda-se que enviar os dados para uma área de trabalho de teste.  


## <a name="1-install-data-collector-api-module"></a>1. Instalar o módulo de API do Recoletor de dados
Cada [pedido a partir da API de Recoletor de dados de HTTP](../log-analytics/log-analytics-data-collector-api.md#create-a-request) tem de ser formatado corretamente e que inclua um cabeçalho de autorização.  Pode fazê-lo no seu runbook, mas pode reduzir a quantidade de código necessário, através de um módulo que simplifica este processo.  É um módulo que pode utilizar [OMSIngestionAPI módulo](https://www.powershellgallery.com/packages/OMSIngestionAPI) na galeria do PowerShell.

Para utilizar um [módulo](../automation/automation-integration-modules.md) num runbook, tem de ser instalado na sua conta de automatização.  Qualquer runbook na mesma conta, em seguida, pode utilizar as funções no módulo.  Pode instalar um novo módulo selecionando **ativos** > **módulos** > **adicionar um módulo** na sua conta de automatização.  

A galeria do PowerShell que dá-lhe a opção rápida para implementar um módulo diretamente à sua conta de automatização, pelo que pode utilizar esta opção para este tutorial.  

![Módulo de OMSIngestionAPI](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Aceda a [galeria do PowerShell](https://www.powershellgallery.com/).
2. Procurar **OMSIngestionAPI**.
3. Clique em de **implementar a automatização do Azure** botão.
4. Selecione a sua conta de automatização e clique em **OK** para instalar o módulo.


## <a name="2-create-automation-variables"></a>2. Criar variáveis de automatização
[As variáveis da automatização](..\automation\automation-variables.md) contêm valores que podem ser utilizados por todos os runbooks na sua conta de automatização.  Efetuam os runbooks mais flexíveis, permitindo-lhe alterar estes valores, sem necessitar de editar o runbook real. Todos os pedidos a partir da API de Recoletor de dados de HTTP requer o ID e a chave da área de trabalho OMS e recursos de variável são ideais armazenar estas informações.  

![Variáveis](media/operations-management-suite-runbook-datacollect/variables.png)

1. No portal do Azure, navegue até à sua conta de automatização.
2. Selecione **variáveis** em **recursos partilhados**.
2. Clique em **adicionar uma variável** e criar as duas variáveis na tabela seguinte.

| Propriedade | Valor de ID da área de trabalho | Valor de chave da área de trabalho |
|:--|:--|:--|
| Nome | WorkspaceId | WorkspaceKey |
| Tipo | Cadeia | Cadeia |
| Valor | Colar o ID da área de trabalho da sua área de trabalho de análise de registos. | Colar com o site primário ou a chave secundária da sua área de trabalho de análise de registos. |
| Encriptados | Não | Sim |



## <a name="3-create-runbook"></a>3. Criar o runbook

A automatização do Azure tem um editor no portal de onde pode editar e testar o runbook.  Tem a opção para utilizar o editor de scripts para trabalhar com [PowerShell diretamente](../automation/automation-edit-textual-runbook.md) ou [criar um runbook gráfico](../automation/automation-graphical-authoring-intro.md).  Para este tutorial, irá trabalhar com um script do PowerShell. 

![Editar o runbook](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Navegue até à sua conta de automatização.  
2. Clique em **Runbooks** > **adicionar um runbook** > **criar um novo runbook**.
3. Para o nome do runbook, escreva **tarefas de automatização de recolher**.  Para o tipo de runbook, selecione **PowerShell**.
4. Clique em **criar** para criar o runbook e iniciar o editor.
5. Copie e cole o seguinte código no runbook.  Consulte os comentários no script para obter explicações do código.
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Add-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Runbook de teste
A automatização do Azure inclui um ambiente para [testar o runbook](../automation/automation-testing-runbook.md) antes de o publicar.  Pode inspecionar os dados recolhidos pelo runbook e certifique-se de que escreve ao Log Analytics conforme esperado antes de a publicar para produção. 
 
![Runbook de teste](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Clique em **guardar** para guardar o runbook.
1. Clique em **painel de teste** para abrir o runbook no ambiente de teste.
3. Uma vez que o runbook tiver parâmetros, é-lhe pedido que introduza valores para os mesmos.  Introduza o nome do grupo de recursos e a automatização de conta que irá recolher informações de tarefa do.
4. Clique em **iniciar** no início do runbook.
3. O runbook será iniciado com um Estado de **em fila** antes de ficar para **executar**.  
3. O runbook deve apresentar uma saída verbosa com as tarefas recolhidas no formato json.  Se não existe nenhuma tarefa estiver listada, em seguida, pode ter existido não existem tarefas que criou na conta de automatização na última hora.  Tente iniciar qualquer runbook na conta de automatização e, em seguida, execute novamente o teste.
4. Certifique-se de que o resultado não mostra os erros no comando post ao Log Analytics.  Deve ter uma mensagem semelhante ao seguinte.

    ![Saída de POST](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Verificar os registos na análise de registos
Depois do runbook foi concluída no teste e verificou que o resultado foi recebeu com êxito, pode verificar que os registos foram criados utilizando um [pesquisa de registo na análise de registos](../log-analytics/log-analytics-log-searches.md).

![Saída de registo](media/operations-management-suite-runbook-datacollect/log-output.png)

1. No portal do Azure, selecione a área de trabalho de análise de registos.
2. Clique em **Iniciar pesquisa**.
3. Escreva o seguinte comando `Type=AutomationJob_CL` e clique no botão de procura. Tenha em atenção que o tipo de registo inclui _CL que não esteja especificado no script.  Esse sufixo é acrescentado automaticamente para o tipo de registo para indicar que um tipo de registo personalizado.
4. Deverá ver um ou mais registos devolvidos que indica que o runbook está a funcionar conforme esperado.


## <a name="6-publish-the-runbook"></a>6. Publicar o runbook
Depois de verificar que o runbook está a funcionar corretamente, tem de publicá-lo, pelo que pode executá-lo na produção.  Pode continuar a editar e testar o runbook sem modificar a versão publicada.  

![Publicar o runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Regressar à sua conta de automatização.
2. Clique em **Runbooks** e selecione **tarefas de automatização de recolher**.
3. Clique em **editar** e, em seguida, **publicar**.
4. Clique em **Sim** quando lhe for pedido para confirmar que pretende substituir a versão publicada anteriormente.

## <a name="7-set-logging-options"></a>7. Definir opções de registo 
Para teste, só era possível ver [uma saída verbosa](../automation/automation-runbook-output-and-messages.md#message-streams) porque definido a variável de $VerbosePreference no script.  Para produção, tem de definir as propriedades de registo para o runbook se pretender ver uma saída verbosa.  Para o runbook utilizado neste tutorial, esta ação apresenta os dados json que está a ser enviados para análise de registos.

![Registo e rastreio](media/operations-management-suite-runbook-datacollect/logging.png)

1. Nas propriedades de runbook selecione **registo e rastreio** em **definições de Runbooks**.
2. Altere a definição **criar registos verbosos** para **no**.
3. Clique em **Guardar**.

## <a name="8-schedule-runbook"></a>8. Agendar o runbook
A forma mais comum para iniciar um runbook que recolhe dados de monitorização é agendá-la para serem executados automaticamente.  Fazê-lo através da criação de um [agenda na automatização do Azure](../automation/automation-schedules.md) e ligá-la ao runbook.

![Agendar o runbook](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. Nas propriedades de runbook, selecione **agendas** em **recursos**.
2. Clique em **adicionar uma agenda** > **associar um agendamento ao runbook** > **criar uma nova agenda**.
5. Digite os seguintes valores para a agenda e clique em **criar**.

| Propriedade | Valor |
|:--|:--|
| Nome | Hora a hora a AutomationJobs |
| É iniciado | Selecione sempre, pelo menos, 5 minutos posterior à hora atual. |
| Recorrência | Recorrente |
| Repetir cada | 1 hora |
| Expiração do conjunto | Não |

Assim que a agenda for criada, tem de definir os valores de parâmetros que vão ser utilizados sempre que esta agenda inicia o runbook.

6. Clique em **configurar parâmetros e definições de execução**.
7. Preencha os valores para o **ResourceGroupName** e **AutomationAccountName**.
8. Clique em **OK**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Verifique se o runbook inicia numa agenda
Everytime um runbook é iniciado, [é criada uma tarefa](../automation/automation-runbook-execution.md) e qualquer resultado com sessão iniciada.  Na verdade, estas são as mesmas tarefas que o runbook está a recolher.  Pode verificar que o runbook inicia conforme esperado, verificando as tarefas de runbook após a hora de início para a agenda foi efectuada com êxito.

![Tarefas](media/operations-management-suite-runbook-datacollect/jobs.png)

1. Nas propriedades de runbook, selecione **tarefas** em **recursos**.
2. Deverá ver uma lista de tarefas para cada vez que o runbook foi iniciado.
3. Clique das tarefas para ver os detalhes.
4. Clique em **todos os registos** para ver os registos e de saída do runbook.
5. Desloque-se na parte inferior para localizar uma entrada semelhante para a imagem abaixo.<br>![Verboso](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Clique nesta entrada para ver os dados de detalhado json que lhe foi enviados para análise de registos.



## <a name="next-steps"></a>Passos seguintes
- Utilize [estruturador de vistas](../log-analytics/log-analytics-view-designer.md) para criar uma vista que apresenta os dados que já recolhidos para o repositório de análise de registos.
- O runbook do pacote um [solução de gestão](operations-management-suite-solutions-creating.md) para distribuir pelos clientes.
- Saiba mais sobre [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Saiba mais sobre [da automatização do Azure](https://docs.microsoft.com/azure/automation/).
- Saiba mais sobre o [API de Recoletor de dados de HTTP](../log-analytics/log-analytics-data-collector-api.md).
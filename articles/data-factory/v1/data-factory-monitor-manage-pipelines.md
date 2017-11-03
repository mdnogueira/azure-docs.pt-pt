---
title: Monitorizar e gerir pipelines com o portal do Azure e o PowerShell | Microsoft Docs
description: "Saiba como utilizar o portal do Azure e o Azure PowerShell para monitorizar e gerir as fábricas de dados do Azure e os pipelines que criou."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: ccc0755385d2f170939e5c19f32b168132b6839b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Monitorizar e gerir pipelines do Azure Data Factory com o portal do Azure e o PowerShell
> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Utilizar aplicações de gestão e monitorização](data-factory-monitor-manage-app.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [monitorizar e gerir pipelines do Data Factory na versão 2](../monitor-visually.md).

> [!IMPORTANT]
> A aplicação de monitorização e gestão fornece um melhor suporte para monitorizar e gerir os seus pipelines de dados e quaisquer problemas de resolução de problemas. Para obter mais informações sobre como utilizar a aplicação, consulte [monitorizar e gerir pipelines de fábrica de dados utilizando a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md). 


Este artigo descreve como monitorizar, gerir e depurar pipelines com o portal do Azure e o PowerShell. O artigo também fornece informações sobre como criar alertas e ser notificado sobre falhas.

## <a name="understand-pipelines-and-activity-states"></a>Compreender os pipelines e Estados de atividade
Ao utilizar o portal do Azure, pode:

* Ver a fábrica de dados como um diagrama.
* Ver as atividades num pipeline.
* Ver os conjuntos de dados de entrada e de saída.

Esta secção também descreve como um setor de conjunto de dados passa a partir de um Estado para outro Estado.   

### <a name="navigate-to-your-data-factory"></a>Navegue para a fábrica de dados
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **fábricas de dados** no menu à esquerda. Se não o vir, clique em **mais serviços >**e, em seguida, clique em **fábricas de dados** sob o **INTELLIGENCE + análise** categoria.

   ![Procurar Tudo > fábricas de dados](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. No **fábricas de dados** painel, selecione a fábrica de dados que está interessado em.

    ![Selecione a fábrica de dados](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Deverá ver a home page da fábrica de dados.

   ![Painel data factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Vista de diagrama de fábrica de dados
O **diagrama** vista de uma fábrica de dados fornece um painel único de transparência para monitorizar e gerir a fábrica de dados e os respetivos recursos. Para ver o **diagrama** ver da fábrica de dados, clique em **diagrama** na home page da fábrica de dados.

![Vista de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Pode ampliar, reduzir, ampliar para ajustar, ampliar para 100%, o esquema do diagrama de bloqueio e posicione automaticamente pipelines e conjuntos de dados. Também pode ver as informações de linhagem de dados (ou seja, Mostrar itens a montante e a jusante dos itens selecionados).

### <a name="activities-inside-a-pipeline"></a>Atividades dentro de um pipeline
1. O pipeline com o botão direito e, em seguida, clique em **abrir pipeline** para ver todas as atividades no pipeline, juntamente com conjuntos de dados de entrada e de saída para as atividades. Esta funcionalidade é útil quando o pipeline inclui mais de uma atividade e pretender compreender a linhagem operacional de um pipeline único.

    ![Menu Abrir pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. No exemplo seguinte, verá uma atividade de cópia no pipeline com uma entrada e um resultado. 

    ![Atividades dentro de um pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Pode navegar para a home page da fábrica de dados clicando a **fábrica de dados** ligação no trilho no canto superior esquerdo.

    ![Navegue de volta para a fábrica de dados](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Ver o estado de cada atividade dentro de um pipeline
Pode ver o estado atual de uma atividade ao visualizar o estado de qualquer um dos conjuntos de dados que são produzidos pela atividade.

Fazendo duplo clique em de **OutputBlobTable** no **diagrama**, pode ver todos os setores que são produzidos pelo execuções de atividade diferente dentro de um pipeline. Pode ver que a atividade de cópia foi executada com êxito para as último oito horas e produzido os setores no **pronto** estado.  

![Estado do pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Os setores de conjunto de dados na fábrica de dados podem ter um dos seguintes Estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Substate</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">A aguardar</td><td>ScheduleTime</td><td>A hora não são fornecidos para o setor ser executado.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências a montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas a executar outros setores.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade está em pausa e não é possível executar os setores até que a atividade é retomada.</td>
</tr>
<tr>
<td>Tente novamente</td><td>Execução da atividade está a ser repetida.</td>
</tr>
<tr>
<td>Validação</td><td>Validação ainda não começou.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Validação está a aguardar para ser repetida.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Em curso</td><td>A validação</td><td>Validação está em curso.</td>
</tr>
<td>-</td>
<td>O setor está a ser processado.</td>
</tr>
<tr>
<td rowspan="4">Falha</td><td>ServiceHost</td><td>A execução da atividade demorou mais do que as permitidas pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>O setor foi cancelado pelo ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>Falha na validação.</td>
</tr>
<tr>
<td>-</td><td>O setor falhou ser gerado e/ou validar.</td>
</tr>
<td>Pronto</td><td>-</td><td>O setor está pronto para consumo.</td>
</tr>
<tr>
<td>Foi ignorada</td><td>Nenhuma</td><td>O setor não está a ser processado.</td>
</tr>
<tr>
<td>Nenhuma</td><td>-</td><td>Um setor existia com um Estado diferente, mas foi reposto.</td>
</tr>
</table>



Pode ver os detalhes sobre um setor clicando uma entrada do setor no **atualizado setores recentemente** painel.

![Detalhes de setor](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Se o setor foi executado múltiplas vezes, verá várias linhas no **atividade é executada** lista. Pode ver detalhes sobre uma atividade executar clicando a entrada na execução de **atividade é executada** lista. A lista mostra todos os ficheiros de registo, juntamente com uma mensagem de erro se existir um. Esta funcionalidade é útil para ver e registos de depuração sem ter de deixar a fábrica de dados.

![Detalhes da execução da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Se o setor não se encontra no **pronto** Estado, pode ver os setores a montante que não estão preparadas e estão a bloquear o execução do setor atual o **setores a montante que não estão prontos** lista. Esta funcionalidade é útil quando o setor estiver no **a aguardar** estado e pretender compreender as dependências a montante que o setor está a aguardar no.

![Setores a montante que não estão prontos](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagrama de estado do conjunto de dados
Depois de implementar uma fábrica de dados e pipelines de ter um período válido de Active Directory, o conjunto de dados setores de transição de um Estado para outro. Atualmente, o estado do setor segue o seguinte diagrama de estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

O fluxo de transição de estado de conjunto de dados no data factory é o seguinte: a aguardar -> no-progresso/em curso (validação) -> pronto/com falhas.

O setor é iniciado num **a aguardar** Estado, a aguardar que as condições prévias ser cumpridos antes de ser executada. Em seguida, a atividade começa a ser executada e o setor entra num **em curso** estado. A execução da atividade pode ou não bem-sucedidos. O setor está marcado como **pronto** ou **falha**, com base no resultado da execução.

Pode repor o setor para voltar atrás do **pronto** ou **falha** estado para o **a aguardar** estado. Também pode marcar o estado do setor **ignorar**, que impede a atividade executar e não a processar o setor.

## <a name="pause-and-resume-pipelines"></a>Colocar em pausa e retomar pipelines
Pode gerir os seus pipelines de utilizando o Azure PowerShell. Por exemplo, pode colocar em pausa e retomar pipelines ao executar os cmdlets do PowerShell do Azure. 

> [!NOTE] 
> A vista de diagrama não suporta a colocação em pausa e retomar a pipelines. Se pretender utilizar uma interface de utilizador, utilize a aplicação de monitorização e gestão. Para obter mais informações sobre como utilizar a aplicação, consulte [monitorizar e gerir pipelines de fábrica de dados utilizando a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md) artigo. 

Pode colocar em pausa/suspender pipelines utilizando o **Suspend-AzureRmDataFactoryPipeline** cmdlet do PowerShell. Este cmdlet é útil quando não pretende executar os seus pipelines de até um problema ser corrigido. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Depois de corrigir o problema com o pipeline, pode retomar o pipeline suspenso, executando o seguinte comando do PowerShell:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Por exemplo:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Depurar pipelines
O Azure Data Factory fornece capacidades avançadas de depuração e resolução de problemas de pipelines utilizando o portal do Azure e o Azure PowerShell.

> [! Tenha em atenção} é mais fácil erros troubleshot com a aplicação de gestão e monitorização. Para obter mais informações sobre como utilizar a aplicação, consulte [monitorizar e gerir pipelines de fábrica de dados utilizando a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md) artigo. 

### <a name="find-errors-in-a-pipeline"></a>Encontrar erros num pipeline
Se falhar a execução de atividades num pipeline, o conjunto de dados é produzido pelo pipeline está em estado de erro devido à falha. Pode depurar e resolver erros no Azure Data Factory através dos seguintes métodos.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Utilizar o portal do Azure para um erro de depuração
1. No **tabela** painel, clique o setor de problema que tenha o **estado** definido como **falha**.

   ![Painel de tabela com o setor do problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. No **setor de dados** painel, clique em que a atividade executada que falhou.

   ![Setor de dados com um erro](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. No **detalhes da execução da atividade** painel, pode transferir os ficheiros que estão associados com o processamento do HDInsight. Clique em **transferir** para Estado/stderr transferir o ficheiro de registo de erros que contém detalhes sobre o erro.

   ![Execução do painel de detalhes com o erro da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Utilize o PowerShell para depurar um erro
1. Inicie o **Azure PowerShell**.
2. Execute o **Get-AzureRmDataFactorySlice** comando para ver os setores e os respetivos Estados. Deverá ver um setor com o estado **falha**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Por exemplo:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Substitua **StartDateTime** com a hora de início da sua pipeline. 
3. Agora, execute o **Get-AzureRmDataFactoryRun** cmdlet para obter detalhes sobre a atividade executar para o setor.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Por exemplo:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    O valor de StartDateTime é a hora de início para o setor de erro/problema que anotou no passo anterior. A data / hora deve estar entre aspas.
4. Deverá ver um resultado com detalhes sobre o erro que é semelhante ao seguinte:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Pode executar o **guardar AzureRmDataFactoryLog** cmdlet com o valor de Id, o resultado e transferir os ficheiros de registo utilizando o **- DownloadLogsoption** para o cmdlet.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Falhas de voltar a executar num pipeline

> [!IMPORTANT]
> É mais fácil resolver erros e execute novamente setores falhadas utilizando a aplicação de gestão e monitorização. Para obter mais informações sobre como utilizar a aplicação, consulte [monitorizar e gerir pipelines de fábrica de dados utilizando a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
Depois de resolver problemas e depuração de falhas num pipeline, pode voltar a executar falhas ao navegar para o setor de erro e clicando no **executar** botão na barra de comandos.

![Voltar a executar um setor falhado](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Caso o setor falhou validação devido a uma falha de política (por exemplo, se os dados não estiver disponíveis), pode corrigir a falha e validar novamente clicando a **validar** botão na barra de comandos.

![Corrija os erros e validar](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Pode voltar a executar falhas utilizando o **Set-AzureRmDataFactorySliceStatus** cmdlet. Consulte o [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) tópico para a sintaxe e outros detalhes sobre o cmdlet.

**Exemplo:**

O exemplo a seguir define o estado de todos os setores para a tabela 'DAWikiAggregatedData' para 'A aguardar' no Azure data factory 'WikiADF'.

'Tipodeatualização' está definida como 'UpstreamInPipeline', que significa que os Estados de cada setor para a tabela e todas as tabelas de (montante) dependentes estão definidos 'A aguardar'. O valor de possíveis para este parâmetro é 'Indivíduo'.

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Criar alertas
Eventos de utilizador de registos do Azure quando um recurso do Azure (por exemplo, uma fábrica de dados) é criado, actualizado ou eliminado. Pode criar alertas nestes eventos. Pode utilizar o Data Factory para capturar várias métricas e criar alertas nas métricas. Recomendamos que utilize eventos para a monitorização em tempo real e utilize as métricas para efeitos de histórico.

### <a name="alerts-on-events"></a>Alertas de eventos
Os eventos do Azure fornecem informações úteis sobre o que acontece no seus recursos do Azure. Quando estiver a utilizar o Azure Data Factory, os eventos são gerados quando:

* Uma fábrica de dados é criada, actualizada ou eliminada.
* O processamento de dados (como "executa") tem foi iniciado ou foi concluída.
* Um cluster do HDInsight a pedido é criado ou removido.

Pode criar alertas nestes eventos de utilizador e configurá-los para enviar notificações por e-mail para o administrador e coadministrators da subscrição. Além disso, pode especificar endereços de e-mail adicionais de utilizadores que necessitam para receber notificações por e-mail quando as condições são cumpridas. Esta funcionalidade é útil quando pretende ser notificado sobre falhas e não pretender monitorizar continuamente a fábrica de dados.

> [!NOTE]
> Atualmente, o portal não mostra alertas de eventos. Utilize o [aplicação de monitorização e gestão](data-factory-monitor-manage-app.md) para ver todos os alertas.


#### <a name="specify-an-alert-definition"></a>Especifique uma definição de alerta
Para especificar uma definição de alerta, crie um ficheiro JSON que descreve as operações que pretende ser alertado no. No exemplo seguinte, o alerta envia uma notificação por e-mail para a operação de RunFinished. Para ser específico, uma notificação por e-mail é enviada quando uma execução na fábrica de dados foi concluída e a execução falhou (estado = FailedExecution).

```JSON
{
    "contentVersion": "1.0.0.0",
     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters": {},
    "resources":
    [
        {
            "name": "ADFAlertsSlice",
            "type": "microsoft.insights/alertrules",
            "apiVersion": "2014-04-01",
            "location": "East US",
            "properties":
            {
                "name": "ADFAlertsSlice",
                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                "isEnabled": true,
                "condition":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    "dataSource":
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        "operationName": "RunFinished",
                        "status": "Failed",
                        "subStatus": "FailedExecution"   
                    }
                },
                "action":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails": [ "<your alias>@contoso.com" ]
                }
            }
        }
    ]
}
```

Pode remover **subestado** da definição JSON se não pretende ser alertado em caso de falha específico.

Este exemplo configura o alerta para todas as fábricas de dados na sua subscrição. Se pretender que o alerta ser configurados para uma fábrica de dados específica, pode especificar a fábrica de dados **resourceUri** no **dataSource**:

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

A tabela seguinte fornece a lista de operações disponíveis e Estados (e substatuses).

| Nome da operação | Estado | Subestado |
| --- | --- | --- |
| RunStarted |Foi iniciada |A Iniciar |
| RunFinished |Não foi possível / foi concluída com êxito |FailedResourceAllocation<br/><br/>Bem-sucedido<br/><br/>FailedExecution<br/><br/>ServiceHost<br/><br/>< cancelada<br/><br/>Validação<br/><br/>Abandonado |
| OnDemandClusterCreateStarted |Foi iniciada | |
| OnDemandClusterCreateSuccessful |Bem-sucedido | |
| OnDemandClusterDeleted |Bem-sucedido | |

Consulte [criar regra de alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) para obter detalhes sobre os elementos JSON que são utilizados no exemplo.

#### <a name="deploy-the-alert"></a>Implementar o alerta
Para implementar o alerta, utilize o cmdlet PowerShell do Azure **New-AzureRmResourceGroupDeployment**, conforme mostrado no exemplo seguinte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

Após a implementação do grupo de recursos foi concluída com êxito, consulte as seguintes mensagens:

```
VERBOSE: 7:00:48 PM - Template is valid.
WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
Please update scripts to remove this parameter.
VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

> [!NOTE]
> Pode utilizar o [criar regra de alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) API REST para criar uma regra de alerta. O payload JSON é semelhante ao exemplo de JSON.  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>Obter a lista de implementações do grupo de recursos do Azure
Para obter a lista de implementações do grupo de recursos do Azure implementadas, utilize o cmdlet **Get-AzureRmResourceGroupDeployment**, conforme mostrado no exemplo seguinte:

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>Resolver problemas de eventos de utilizador
1. Pode ver todos os eventos que são gerados depois de clicar no **métricas e operações** mosaico.

    ![Mosaico de métricas e operações](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Clique em de **eventos** mosaico para verem os eventos.

    ![Mosaico de eventos](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. No **eventos** painel, pode ver detalhes sobre eventos, eventos filtrados e assim sucessivamente.

    ![Painel de eventos](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Clique num **operação** na lista de operações que causa um erro.

    ![Selecione uma operação](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. Clique num **erro** eventos para ver detalhes sobre o erro.

    ![Erro de evento](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Consulte [cmdlets Azure Insight](https://msdn.microsoft.com/library/mt282452.aspx) para os cmdlets do PowerShell que pode utilizar para adicionar, obter ou remova os alertas. Seguem-se alguns exemplos de como utilizar o **Get-AlertRule** cmdlet:

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
Properties :
Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
Condition   :
DataSource :
EventName             :
Category              :
Level                 :
OperationName         : RunFinished
ResourceGroupName     :
ResourceProviderName  :
ResourceId            :
Status                : Failed
SubStatus             : FailedExecution
Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0

Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
Location   : West US
Name       : FailedExecutionRunsWest3
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Execute os seguintes comandos de get-help para ver os detalhes e exemplos para o cmdlet Get-AlertRule.

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


Se vir os eventos de geração de alertas no painel do portal, mas não receber notificações por e-mail, verifique se o endereço de e-mail especificado está definido para receber e-mails de remetentes externos. Os e-mails de alerta podem ter sido bloqueados pelas definições de e-mail.

### <a name="alerts-on-metrics"></a>Alertas nas métricas
Fábrica de dados, pode capturar várias métricas e criar alertas nas métricas. Pode monitorizar e criar alertas nas métricas seguintes para os setores na fábrica de dados:

* **Falha de execução**
* **É executado com êxito**

Estas métricas são úteis e ajudá-lo a obter uma descrição geral das execuções de global falhadas e bem-sucedidas na fábrica de dados. As métricas são emitidas sempre que há uma execução de setor. No início da hora, estas métricas são agregadas e enviadas para a sua conta do storage. Para ativar as métricas, configure uma conta de armazenamento.

#### <a name="enable-metrics"></a>Ativar as métricas
Para ativar as métricas, clique em seguinte do **Data Factory** painel:

**Monitorização** > **métrica** > **definições de diagnóstico** > **diagnóstico**

![Ligação de diagnóstico](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

No **diagnóstico** painel, clique em **no**, selecione a conta de armazenamento e clique em **guardar**.

![Painel de diagnóstico](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Poderá demorar até uma hora para as métricas ser visível a **monitorização** painel porque a agregação de métricas acontece por hora.

### <a name="set-up-an-alert-on-metrics"></a>Configurar um alerta nas métricas
Clique em de **métricas do Data Factory** mosaico:

![Mosaico de métricas de fábrica de dados](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

No **métrica** painel, clique em **+ Adicionar alerta** na barra de ferramentas.
![Painel de métrico de fábrica de dados > Adicionar alerta](./media/data-factory-monitor-manage-pipelines/add-alert.png)

No **adicionar uma regra de alerta** página, efetuar os passos seguintes e clique em **OK**.

* Introduza um nome para o alerta (exemplo: "alerta de falha").
* Introduza uma descrição para o alerta (exemplo: "enviar um e-mail quando ocorre uma falha").
* Selecione uma métrica ("Falha executa" vs. "Com êxito execuções").
* Especifique uma condição e um valor de limiar.   
* Especifique o período de tempo.
* Especifique se deve ser enviado um e-mail para os proprietários, contribuintes e leitores.

![Painel de métrico de fábrica de dados > Adicionar regra de alerta](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

Depois da regra de alerta é adicionada com êxito, o painel fecha e ver o novo alerta no **métrica** painel.

![Painel de métrico de fábrica de dados > novo alerta adicionada](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

Também deverá ver o número de alertas no **regras de alerta** mosaico. Clique em de **regras de alerta** mosaico.

![Painel métrico data factory – regras de alertas](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

No **alertas regras** painel, pode ver todos os alertas existentes. Para adicionar um alerta, clique em **Adicionar alerta** na barra de ferramentas.

![Painel de regras de alertas](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Notificações de alerta
Depois da regra de alerta corresponde a condição, deve obter um e-mail que indica que o alerta está ativado. Depois do problema seja resolvido e a condição do alerta não corresponde ao já, receberá uma mensagem de e-mail que indica que o alerta é resolvido.

Este comportamento é diferente de eventos em que é enviada uma notificação em cada caso de falha que uma regra de alerta qualificam para.

### <a name="deploy-alerts-by-using-powershell"></a>Implementar os alertas utilizando o PowerShell
Pode implementar alertas com base nas métricas da mesma forma que faria para eventos.

**Definição do alerta**

```JSON
{
    "contentVersion" : "1.0.0.0",
    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters" : {},
    "resources" : [
    {
            "name" : "FailedRunsGreaterThan5",
            "type" : "microsoft.insights/alertrules",
            "apiVersion" : "2014-04-01",
            "location" : "East US",
            "properties" : {
                "name" : "FailedRunsGreaterThan5",
                "description" : "Failed Runs greater than 5",
                "isEnabled" : true,
                "condition" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                    "dataSource" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                        "metricName" : "FailedRuns"
                    },
                    "threshold" : 5.0,
                    "windowSize" : "PT3H",
                    "timeAggregation" : "Total"
                },
                "action" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails" : ["abhinav.gpt@live.com"]
                }
            }
        }
    ]
}
```

Substitua *subscriptionId*, *resourceGroupName*, e *dataFactoryName* na amostra com os valores adequados.

*metricName* atualmente suporta dois valores:

* FailedRuns
* SuccessfulRuns

**Implementar o alerta**

Para implementar o alerta, utilize o cmdlet PowerShell do Azure **New-AzureRmResourceGroupDeployment**, conforme mostrado no exemplo seguinte:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

Deverá ver a seguinte mensagem após uma implementação com êxito:

```
VERBOSE: 12:52:47 PM - Template is valid.
VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


DeploymentName    : FailedRunsGreaterThan5
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 7/27/2015 7:52:56 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           
```

Também pode utilizar o **adicionar AlertRule** cmdlet para implementar uma regra de alerta. Consulte o [adicionar AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) tópico para obter detalhes e exemplos.  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Mover uma fábrica de dados para um grupo de recursos diferente ou uma subscrição
Pode mover uma fábrica de dados para um grupo de recursos diferente ou uma subscrição diferente, utilizando o **mover** comando barra botão na home page da fábrica de dados.

![Mover a fábrica de dados](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Pode também mover quaisquer recursos relacionados (tais como alertas que estão associados a fábrica de dados), juntamente com a fábrica de dados.

![Mover a caixa de diálogo de recursos](./media/data-factory-monitor-manage-pipelines/MoveResources.png)

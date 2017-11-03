---
title: "Registo de diagnóstico Cosmos BD do Azure | Microsoft Docs"
description: "Utilize este tutorial para ajudá-lo com a base de dados do Azure Cosmos registo."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: mimig
ms.openlocfilehash: 407a9a3be4ae8a9b00a953914e6b4414d8dac8b6
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Registo de diagnóstico Cosmos BD do Azure

Assim que tiver iniciado a utilizar um ou mais bases de dados de BD do Cosmos do Azure, pode querer monitorizar como e quando as bases de dados são acedidos. Diagnóstico de registo na base de dados do Azure Cosmos permite-lhe executar esta monitorização. Ao ativar o registo de diagnóstico, pode enviar registos para [Storage do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/), e/ou exportá-las para [Log Analytics](https://azure.microsoft.com/services/log-analytics/), que faz parte de [ Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite).

![Registo de diagnóstico para o armazenamento, os Event Hubs ou através de análise de registos do Operations Management Suite](./media/logging/azure-cosmos-db-logging-overview.png)

Utilize este tutorial para começar com Azure Cosmos DB registo através do portal do Azure, CLI ou PowerShell.

## <a name="what-is-logged"></a>O que é registado?

* Todos os pedidos de REST (SQL do DocumentDB) API autenticados são registados, que inclui os pedidos falhados resultantes de permissões de acesso, erros de sistema ou pedidos incorretos. Suporte para o MongoDB, gráfico e APIs de tabela não está atualmente disponível.
* Operações de na base de dados autónomo, que inclui as operações CRUD em todos os documentos, contentores e bases de dados.
* Operações sobre chaves de conta, que incluem a criar, modificar ou eliminar estas chaves.
* Pedidos não autenticados que resultam numa resposta 401. Por exemplo, pedidos que não têm um token de portador ou pedidos incorretamente formulados ou expirados ou com um token inválido.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, tem de ter os seguintes recursos:

* Um existente do Azure Cosmos DB conta, base de dados e contentor. Para obter instruções sobre como criar estes recursos, consulte [criar uma conta de base de dados utilizando o portal do Azure](create-documentdb-dotnet.md#create-a-database-account), [amostras CLI](cli-samples.md), ou [exemplos do PowerShell](powershell-samples.md).

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Ative o registo no portal do Azure

1. No [portal do Azure](https://portal.azure.com), no seu Azure Cosmos DB conta, clique em **registos de diagnóstico** na navegação à esquerda e, em seguida, clique em **ative os diagnósticos**.

    ![Ativar o registo de diagnóstico para a BD do Cosmos do Azure no portal do Azure](./media/logging/turn-on-portal-logging.png)

2. No **definições de diagnóstico** página, efetue o seguinte: 

    * **Nome**. Introduza um nome para os registos para o criar.

    * **Arquivo para uma conta de armazenamento**. Para utilizar esta opção, terá de uma conta de armazenamento existente para ligar a. Para criar uma nova conta do storage no portal, consulte [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md) e siga as instruções para criar uma conta para fins gerais do Resource Manager. Em seguida, regresse a esta página no portal para selecionar a sua conta do storage. Pode demorar alguns minutos para as contas de armazenamento recentemente criada aparece no menu pendente.
    * **Fluxo para um hub de eventos**. Para utilizar esta opção, terá de um existente Hub de eventos espaço de nomes e event hub para ligar a. Para criar um espaço de nomes de Event Hubs, consulte [criar um espaço de nomes de Event Hubs e um hub de eventos no portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, regresse a esta página no portal para selecionar o nome de espaço de nomes e a política de Hub de eventos.
    * **Enviar ao Log Analytics**.     Para utilizar esta opção, utilize uma área de trabalho existente ou crie uma nova área de trabalho de análise de registos, seguindo os passos para [criar uma nova área de trabalho](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) no portal. Para obter mais informações sobre a visualização dos registos na análise de registos, consulte [ver os registos na análise de registos](#view-in-loganalytics).
    * **Inicie sessão DataPlaneRequests**. Selecione esta opção para registar os diagnósticos para contas do DocumentDB, gráfico e API de tabela. Se estiver arquivar a uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos são autodeleted após o período de retenção expira.
    * **Inicie sessão MongoRequests**. Selecione esta opção para registar os diagnósticos para contas de API do MongoDB. Se estiver arquivar a uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos são autodeleted após o período de retenção expira.
    * **Pedidos de métricos**. Selecione esta opção para armazenar dados verbosos no [Azure métricas](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftdocumentdbdatabaseaccounts-cosmosdb). Se estiver arquivar a uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos são autodeleted após o período de retenção expira.

3. Clique em **Guardar**.

    Se receber um erro que indica "Falha ao atualizar os diagnósticos para \<nome da área de trabalho >. A subscrição \<id de subscrição > não está registada para utilizar insights. " Siga o [resolver problemas do diagnóstico do Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage) as instruções para registar a conta, em seguida, repita este procedimento.

    Se pretender alterar a forma como em que os registos de diagnóstico são guardados no futuro em qualquer momento, pode regressar a esta página em qualquer altura para modificar as definições de registo de diagnóstico para a sua conta.

## <a name="turn-on-logging-using-cli"></a>Ativar o registo a com a CLI

Para ativar as métricas e registo de diagnóstico utilizando a CLI do Azure, utilize os seguintes comandos:

- Para ativar o armazenamento dos registos de diagnóstico numa conta do Storage, utilize este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   O `resourceId` é o nome da conta de base de dados do Azure Cosmos. O `storageId` é o nome da conta do storage para o qual pretende enviar os registos.

- Para ativar a transmissão em fluxo de registos de diagnóstico para um Hub de eventos, utilize este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   O `resourceId` é o nome da conta de base de dados do Azure Cosmos. O `serviceBusRuleId` é uma cadeia com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para permitir o envio de registos de diagnóstico para uma área de trabalho de análise de registos, utilize este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

## <a name="turn-on-logging-using-powershell"></a>Ativar o registo com o PowerShell

Para ativar o registo com o PowerShell, terá de Azure Powershell, com uma versão mínima 1.0.1.

Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

Se já tiver instalado o Azure PowerShell e não souber a versão, a partir da consola do PowerShell, escreva `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Ligar às suas subscrições
Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:  

```powershell
Login-AzureRmAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. O Azure PowerShell obtém todas as subscrições associadas a esta conta e, por defeito, utiliza a primeira.

Se tiver várias subscrições, poderá ter de especificar uma subscrição utilizada para criar o seu Cofre de Chaves do Azure. Escreva o seguinte para ver as subscrições da sua conta:

```powershell
Get-AzureRmSubscription
```

Em seguida, para especificar a subscrição que está associada a conta de base de dados do Azure Cosmos que está a iniciar sessão, escreva:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se tiver várias subscrições associadas à sua conta é importante especificar a subscrição.
>
>

Para obter mais informações sobre como configurar o Azure PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

### <a id="storage"></a>Criar uma nova conta de armazenamento para os seus registos
Apesar de poder utilizar uma conta de armazenamento existente para os seus registos, neste tutorial vamos criar uma nova conta de armazenamento dedicada aos registos de base de dados do Azure Cosmos. Para sua comodidade, estão a armazenar os detalhes da conta de armazenamento numa variável designada **sa**.

Para facilitar ainda mais a gestão, neste tutorial utilizamos o mesmo grupo de recursos que contém a nossa base de dados do Azure Cosmos DB. Substitua os valores para ContosoResourceGroup, contosocosmosdblogs e 'Norte Central nos' para os seus próprios valores, conforme aplicável:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Se optar por utilizar uma conta de armazenamento existente, tem de utilizar a mesma subscrição como a sua subscrição da base de dados do Azure Cosmos e tem de utilizar o modelo de implementação Resource Manager, em vez do modelo de implementação clássica.
>
>

### <a id="identify"></a>Identificar a conta de base de dados do Azure Cosmos para os seus registos
Defina o nome da conta de base de dados do Azure Cosmos como uma variável com o nome **conta**, onde ResourceName é o nome da conta de base de dados do Azure Cosmos.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Ativar registo
Para ativar o registo de base de dados do Azure Cosmos, utilize o cmdlet Set-AzureRmDiagnosticSetting, juntamente com as variáveis para a nova conta de armazenamento, a conta de base de dados do Azure Cosmos e a categoria para o qual gostaria de ativar registos. Execute o seguinte comando, a definição de **-ativado** sinalizador para **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

O resultado para o comando deve assemelhar-se do seguinte:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Isto confirma que o registo está agora ativado para a base de dados, guardar a informação à sua conta de armazenamento.

Opcionalmente, pode também definir a política de retenção dos seus registos de forma a que os registos mais antigos sejam eliminados automaticamente. Por exemplo, defina a política de retenção utilizando o sinalizador **- RetentionEnabled** para **$true** e defina o parâmetro **- RetentionInDays** **90** para que os registos com mais de 90 dias sejam eliminados automaticamente.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Aceder aos seus registos
BD do Azure do Cosmos nos registos **DataPlaneRequests** categoria são armazenadas no **insights-registos--plane-pedidos de dados** contentor na conta de armazenamento que indicou. 

Primeiro, crie uma variável para o nome do contentor. Isto irá ser utilizado em todo o resto da passagem.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Para listar todos os blobs neste contentor, escreva:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

O resultado será algo parecido com isto:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Como pode neste resultado, os blobs seguem uma convenção de nomenclatura:`resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores data e hora utilizam o UTC.

Porque a mesma conta de armazenamento pode ser utilizada para recolher registos para vários recursos, o ID de recurso completamente qualificado no nome do blob é muito útil para aceder ou transferir os blobs de que precisa. Mas antes de podermos fazer isso, teremos primeiro de saber como transferir todos os blobs.

Primeiro, crie uma pasta para transferir os blobs. Por exemplo:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Encaminhe esta lista através de 'Get-AzureStorageBlobContent' para transferir os blobs para a nossa pasta de destino:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Quando executar este segundo comando, o  **/**  delimitador dos nomes blob cria uma estrutura de pasta completa sob a pasta de destino. Esta estrutura de pastas é utilizada para transferir e armazenar os blobs como ficheiros.

Para transferir seletivamente blobs, utilize carateres universais. Por exemplo:

* Se tiver várias bases de dados e pretender transferir registos para apenas uma base de dados, com o nome CONTOSOCOSMOSDB3:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Se tiver vários grupos de recursos e pretender transferir os registos para apenas um grupo de recursos, utilize `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Se pretende transferir todos os registos do mês de Julho de 2017, utilize `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Além disso:

* Para consultar o estado das definições de diagnóstico para o seu recurso de base de dados:`Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Para desativar o registo de **DataPlaneRequests** categoria para o seu recurso de conta de base de dados:`Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


Os blobs que são devolvidos em cada uma das seguintes consultas são armazenados como texto, formatados como um blob JSON, conforme mostrado no seguinte código. 

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Para saber mais sobre os dados de cada blob JSON, consulte [interpretar os registos da base de dados do Azure Cosmos](#interpret).

## <a name="managing-your-logs"></a>Gerir os seus registos

Os registos são disponibilizados na sua conta de duas horas desde o momento que a operação de base de dados do Azure Cosmos foi efetuada. Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.
* O período de retenção para pedidos de plane dados arquivados para uma conta de armazenamento está configurado no portal quando **registo DataPlaneRequests** está selecionada. Para alterar essa definição, consulte [ative o registo no portal do Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Ver registos na análise de registos

Se tiver selecionado o **enviar ao Log Analytics** opção quando ativou o registo, dados de diagnóstico da sua coleção seja reencaminhados para a análise de registos dentro de duas horas. Isto significa que se observar a análise de registos imediatamente depois de desativar o registo, que não veja todos os dados. Apenas, aguarde duas horas e tente novamente. 

Antes de visualizar os seus registos, poderá ser útil verificar e ver se a sua área de trabalho de análise de registos foi atualizada para utilizar o novo idioma de consulta de análise de registos. Para verificar isto, abra o [portal do Azure](https://portal.azure.com), clique em **Log Analytics** no lado esquerdo até ao momento, em seguida, selecione o nome da área de trabalho conforme mostrado na imagem seguinte. O **área de trabalho OMS** é apresentada a página, conforme mostrado na imagem seguinte.

![Análise de registos no portal do Azure](./media/logging/azure-portal.png)

Se vir a mensagem seguinte no **área de trabalho OMS** página, a sua área de trabalho não foi atualizada para utilizar o novo idioma. Para obter mais informações sobre a atualização para o novo idioma de consulta, consulte [atualizar a sua área de trabalho do Log Analytics do Azure para a pesquisa do novo registo](../log-analytics/log-analytics-log-search-upgrade.md). 

![Notificação de atualização de análise do registo](./media/logging/upgrade-notification.png)

Para ver os dados de diagnóstico na análise de registos, abra a página de pesquisa de registo do menu à esquerda ou a área de gestão da página, conforme mostrado na imagem seguinte.

![Opções de pesquisa de registo no portal do Azure](./media/logging/log-analytics-open-log-search.png)

Agora que ativou a recolha de dados, execute o seguinte exemplo de pesquisa de registo, utilizando a linguagem de consulta nova, para ver os registos mais recentes dez `AzureDiagnostics | take 10`.

![Exemplo demorar 10 pesquisa de registo](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Consultas

Seguem-se algumas consultas adicionais que pode introduzir no **pesquisa registo** caixa para ajudar a monitorizar os contentores de BD do Cosmos do Azure. Estas consultas trabalham com o [novo idioma](../log-analytics/log-analytics-log-search-upgrade.md). 

Para saber mais sobre o significado dos dados devolvidos pela pesquisa cada registo, consulte [interpretar os registos da base de dados do Azure Cosmos](#interpret).

* Todos os registos de diagnóstico da base de dados do Azure Cosmos o período de tempo especificado.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Mais de dez recentemente registado eventos.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Todas as operações, agrupadas por tipo de operação.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Todas as operações, agrupadas por recurso.

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Todas as atividades de utilizador, agrupadas por recurso. Tenha em atenção que se trata de um registo de atividade, não um registo de diagnóstico.

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* As operações demoram mais de 3 milissegundos.

    ```
    AzureDiagnostics | where toint(duration_s) > 3000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* O agente está a executar as operações.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Quando foram executadas operações de execução longa.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Para obter informações adicionais sobre como utilizar o novo idioma de pesquisa de registo, consulte [registo compreender procura na análise de registos](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretar os seus registos

Armazenados no Storage do Azure e análise de registos de dados de diagnóstico utilizam um esquema muito semelhante. 

A tabela seguinte descreve o conteúdo de cada entrada de registo.

| Campo de armazenamento do Azure ou uma propriedade | Propriedade de análise do registo | Descrição |
| --- | --- | --- |
| hora | TimeGenerated | A data e hora (UTC) quando ocorreu a operação. |
| resourceId | Recurso | A conta de base de dados do Azure Cosmos para o qual os registos estão ativados.|
| categoria | Categoria | Para os registos de base de dados do Azure Cosmos DataPlaneRequests é o valor apenas disponível. |
| operationName | OperationName | Nome da operação. Este valor pode ser qualquer uma das seguintes operações: criar, atualizar, leitura, ReadFeed, eliminar, substituir, executar, SqlQuery, consulta, JSQuery, Head, HeadFeed ou Upsert.   |
| propriedades | n/d | O conteúdo deste campo é descrito nas linhas seguintes. |
| ActivityId | activityId_g | O GUID exclusivo para a operação com sessão iniciada. |
| UserAgent | userAgent_s | Uma cadeia que especifica o agente de utilizador do cliente está a efetuar o pedido. O formato é {nome do agente de utilizador} / {version}.|
| resourceType | ResourceType | O tipo de recurso acedido. Este valor pode ser qualquer um dos seguintes tipos de recurso: base de dados, coleção, documentos, anexo, um utilizador, permissão, StoredProcedure, acionador, UserDefinedFunction ou oferta. |
| statusCode |statusCode_s | O estado de resposta da operação. |
| requestResourceId | ResourceId | ResourceId relativas a pedido, pode apontar para databaseRid, collectionRid ou documentRid dependendo da operação efetuada.|
| clientIpAddress | clientIpAddress_s | Endereço IP do cliente. |
| requestCharge | requestCharge_s | O número de RUs utilizado pela operação |
| collectionRid | collectionId_s | O ID exclusivo para a coleção.|
| Duração | duration_s | A duração da operação, numa ticks. |
| requestLength | requestLength_s | O comprimento do pedido, em bytes. |
| responseLength | responseLength_s | O comprimento da resposta, em bytes.|
| resourceTokenUserRid | resourceTokenUserRid_s | Isto é não vazia quando [tokens de recurso](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) são utilizados para autenticação e de pontos para o ID de recurso do utilizador. |

## <a name="next-steps"></a>Passos seguintes

- Para obter uma compreensão dos não só como ativar o registo, mas também as categorias de métricas e registo suportadas do Azure de várias a serviços ler o [descrição geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [descrição geral do Azure Os registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artigos.
- Estes artigos para saber mais sobre os event hubs de leitura:
   - [Quais são os Event Hubs do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Leitura [transferir métricas e registos de diagnóstico do armazenamento do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
- Leitura [registo compreender procura na análise de registos](../log-analytics/log-analytics-log-search-new.md)

---
title: "Integração de grelha de evento e do Event Hubs do Azure"
description: Descreve como utilizar a grelha de eventos do Azure e do Event Hubs para migrar dados para um SQL Data Warehouse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: f7d2b1970cb7b1330b3d9bdff7987a90fa381392
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Macrodados fluxo para um armazém de dados

Azure [eventos grelha](overview.md) é um serviço de encaminhamento de evento inteligente permite-lhe reagir a notificações de aplicações e serviços. O [amostra capturar os Hubs de eventos e eventos grelha](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra como utilizar os Hubs de eventos do Azure captura com grelha de eventos do Azure para perfeitamente migrar dados a partir de um hub de eventos para um SQL Data Warehouse.

![Descrição geral da aplicação](media/event-grid-event-hubs-integration/overview.png)

Como os dados são enviados para o hub de eventos, captura obtém dados a partir do fluxo e gera blobs de armazenamento com os dados no formato Avro. Quando captura gera o blob, acionado um evento. Grelha de evento distribui dados sobre o evento para subscritores. Neste caso, os dados de eventos são enviados para o ponto final de funções do Azure. Os dados do evento incluem o caminho do blob gerado. A função utiliza esse URL para obter o ficheiro e enviá-lo para o armazém de dados.

Neste artigo, pode:

* Implemente a infraestrutura do seguinte:
  * Hub de eventos com captura ativada
  * Conta de armazenamento para os ficheiros de captura
  * Plano de serviço de aplicações do Azure para alojar a aplicação de função
  * Aplicação de função para processar o evento
  * SQL Server para alojar o armazém de dados
  * Armazém de dados do SQL Server para armazenar os dados migrados
* Criar uma tabela no armazém de dados
* Adicionar código para a aplicação de função
* Subscrever o evento
* Executar a aplicação que envia dados para o hub de eventos
* Ver os dados migrados no armazém de dados

## <a name="about-the-event-data"></a>Sobre os dados do evento

Grelha de evento distribui dados de eventos para os subscritores. O exemplo seguinte mostra os dados de eventos para a criação de um ficheiro de captura. Em particular, tenha em atenção o `fileUrl` propriedade no `data` objeto. A aplicação de função obtém este valor e utiliza-o para obter o ficheiro de captura.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Visual studio 2017 versão 15.3.2 ou superior](https://www.visualstudio.com/vs/) com cargas de trabalho para: desenvolvimento de ambiente de trabalho de .NET, programação do Azure, desenvolvimento de ASP.NET e web, desenvolvimento de Node.js e desenvolvimento do Python.
* O [projeto de exemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) transferido para o seu computador.

## <a name="deploy-the-infrastructure"></a>Implementar a infraestrutura

Para simplificar este artigo, implementar a infraestrutura necessária com um modelo do Resource Manager. Para ver os recursos que são implementados, ver o [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Suporta a grelha de evento para a versão de pré-visualização, **westus2** e **westcentralus** regiões. Utilize um destas regiões para a localização do grupo de recursos.

Para a CLI do Azure, utilize:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

Para o PowerShell, utilize:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Forneça um valor de palavra-passe quando lhe for pedido.

## <a name="create-a-table-in-sql-data-warehouse"></a>Criar uma tabela no SQL Data Warehouse

Adicionar uma tabela para o armazém de dados, executando o [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) script. Para executar o script, utilize o Visual Studio ou o Editor de consultas no portal.

Se o script a executar:

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>Publicar a aplicação de funções do Azure

1. Abra o [projeto de exemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) no Visual Studio 2017 (15.3.2 ou superior).

2. No Explorador de soluções, faça duplo clique **FunctionDWDumper**e selecione **publicar**.

   ![Publicar a aplicação de função](media/event-grid-event-hubs-integration/publish-function-app.png)

3. Selecione **aplicação de função do Azure** e **selecionar existente**. Selecione **OK**.

   ![Aplicação de função do destino](media/event-grid-event-hubs-integration/pick-target.png)

4. Selecione a aplicação de função que tenha implementado através do modelo. Selecione **OK**.

   ![Selecione a aplicação de função](media/event-grid-event-hubs-integration/select-function-app.png)

5. Quando o Visual Studio tiver configurado o perfil, selecione **publicar**.

   ![Selecione publicar](media/event-grid-event-hubs-integration/select-publish.png)

6. Depois de publicar a função, vá para o [portal do Azure](https://portal.azure.com/). Selecione a aplicação de grupo e a função de recursos.

   ![Aplicação de função de vista](media/event-grid-event-hubs-integration/view-function-app.png)

7. Selecione a função.

   ![Selecione a função](media/event-grid-event-hubs-integration/select-function.png)

8. Obter o URL para a função. Terá este URL ao criar a subscrição de evento.

   ![Obter o URL de função](media/event-grid-event-hubs-integration/get-function-url.png)

9. Copie o valor.

   ![Copie o URL](media/event-grid-event-hubs-integration/copy-url.png)

## <a name="subscribe-to-the-event"></a>Subscrever o evento

Pode utilizar a CLI do Azure ou o portal para subscrever o evento. Este artigo mostra ambas as abordagens.

### <a name="portal"></a>Portal

1. A partir do espaço de nomes dos Event Hubs, selecione **eventos grelha** à esquerda.

   ![Selecione a grelha de eventos](media/event-grid-event-hubs-integration/select-event-grid.png)

2. Adicione uma subscrição de evento.

   ![Adicionar subscrição de evento](media/event-grid-event-hubs-integration/add-event-subscription.png)

3. Fornece valores para a subscrição de evento. Utilize o URL de funções do Azure que copiou. Selecione **Criar**.

   ![Indique os valores de subscrição](media/event-grid-event-hubs-integration/provide-values.png)

### <a name="azure-cli"></a>CLI do Azure

Para subscrever o evento, execute o seguinte comando:

```azurecli-interactive
az eventgrid resource event-subscription create -g rgDataMigrationSample --provider-namespace Microsoft.EventHub --resource-type namespaces --resource-name <your-EventHubs-namespace> --name captureEventSub --endpoint <your-function-endpoint>
```

## <a name="run-the-app-to-generate-data"></a>Executar a aplicação para gerar dados

Terminar de configurar o seu hub de eventos, o SQL data warehouse, aplicação de função do Azure e de subscrição de evento. A solução está pronta para migrar os dados do hub de eventos do armazém de dados. Antes de executar uma aplicação que gera dados para o hub de eventos, terá de configurar alguns valores.

1. No portal, selecione o seu espaço de nomes do hub de eventos. Selecione **cadeias de ligação**.

   ![Selecione as cadeias de ligação](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Selecione **RootManageSharedAccessKey**

   ![Selecione a chave](media/event-grid-event-hubs-integration/show-root-key.png)

3. Cópia **cadeia de ligação - chave primária**

   ![Chave de cópia](media/event-grid-event-hubs-integration/copy-key.png)

4. Volte ao seu projeto de Visual Studio. No projeto WindTurbineDataGenerator, abra **program.cs**.

5. Substitua os dois valores constantes. Utilize o valor copiado para **EventHubConnectionString**. Utilize o nome de hub de eventos para **EventHubName**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://tfdatamigratens.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilar a solução. Execute a aplicação de WindTurbineGenerator.exe. Após alguns minutos, consulta a tabela no seu armazém de dados para dados migrados.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à grelha de eventos, consulte [sobre eventos grelha](overview.md).
* Para uma introdução ao Event Hubs captura, consulte [ativar Event Hubs capturar utilizando o portal do Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para obter mais informações sobre como configurar e executar o exemplo, consulte [amostra capturar os Hubs de eventos e eventos grelha](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
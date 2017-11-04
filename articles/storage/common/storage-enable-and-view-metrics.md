---
title: "Ativar as métricas do storage no portal do Azure | Microsoft Docs"
description: "Como ativar as métricas do storage para os serviços Blob, fila, tabela e ficheiro"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: tamram
ms.openlocfilehash: 8abb4f968c1fa84e03c8cc807826d3684713847a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Ativar as métricas do Storage do Azure e visualizar dados de métricas
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Descrição geral
As métricas do Storage está ativada por predefinição quando cria uma nova conta de armazenamento. Pode configurar a monitorização através de [portal do Azure](https://portal.azure.com) ou o Windows PowerShell, ou através de programação através de uma das bibliotecas de cliente de armazenamento.

Pode configurar um período de retenção para os dados das métricas: este período determina quanto ao armazenamento dos serviço mantém as métricas e os encargos, para o espaço necessário para armazená-las. Normalmente, deve utilizar um período de retenção mais curto minuto com base nas métricas que métricas horárias devido ao espaço extra significativo necessário com base nas métricas minuto. Escolha um período de retenção, de modo a que tem tempo suficiente para analisar os dados e transferir quaisquer métricas que pretende manter para fins de relatórios ou de análise offline. Lembre-se de que também são cobradas para transferência de dados de métricas da sua conta de armazenamento.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Como ativar as métricas no portal do Azure
Siga estes passos para ativar as métricas no [portal do Azure](https://portal.azure.com):

1. Navegue até à sua conta do storage.
1. Selecione **diagnóstico** do **Menu** painel.
1. Certifique-se de que **estado** está definido como **no**.
1. Selecione as métricas para os serviços que pretende monitorizar.
1. Especifique uma política de retenção para indicar o período de tempo para manter as métricas e registo de dados.
1. Selecione **Guardar**.

O [portal do Azure](https://portal.azure.com) não atualmente permitem-lhe configurar as métricas da minutos na sua conta de armazenamento; tem de ativar as métricas minutos com o PowerShell ou através de programação.

## <a name="how-to-enable-metrics-using-powershell"></a>Como ativar as métricas através do PowerShell
Pode utilizar o PowerShell no seu computador local para configurar as métricas do Storage na sua conta de armazenamento utilizando o cmdlet do Azure PowerShell Get-AzureStorageServiceMetricsProperty para obter as definições atuais e o cmdlet Set-AzureStorageServiceMetricsProperty para alterar as definições atuais.

Os cmdlets que controlam as métricas do Storage utilizar os seguintes parâmetros:

* MetricsType: os valores possíveis são horas e minutos.
* ServiceType: os valores possíveis são tabela, fila e Blob.
* MetricsLevel: os valores possíveis são None, serviço e ServiceAndApi.

Por exemplo, o comando seguinte muda num minutos métricas para o serviço de BLOBs na sua conta do storage predefinida com retenção período definido como cinco dias:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

O seguinte comando obtém os atuais por hora métricas e retenção de nível de dias para o serviço de BLOBs na sua conta do storage predefinida:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Para obter informações sobre como configurar cmdlets PowerShell do Azure para trabalhar com a sua subscrição do Azure e como selecionar a conta de armazenamento predefinido a utilizar, consulte: [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Como ativar as métricas do Storage através de programação
C# fragmento seguinte mostra como ativar as métricas e registo do serviço Blob a utilizar a biblioteca de clientes de armazenamento para .NET de:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Ver as métricas do Storage
Depois de configurar as métricas da análise de armazenamento para monitorizar a sua conta do storage, análise de armazenamento regista as métricas de um conjunto de tabelas bem conhecidas na sua conta do storage. Pode configurar gráficos para ver as métricas de hora a hora no [portal do Azure](https://portal.azure.com):

1. Navegue até à sua conta do storage no [portal do Azure](https://portal.azure.com).
1. Selecione **métricas** no **Menu** painel para o serviço cujas métricas que pretende visualizar.
1. Selecione **editar** no gráfico de que pretende configurar.
1. No **editar gráfico** painel, selecione o **intervalo de tempo**, **tipo de gráfico**e as métricas que pretende apresentar no gráfico.
1. Selecione **OK**

Se pretender transferir as métricas de armazenamento de longa duração ou analisá-los localmente, terá de:

* Utilize uma ferramenta que está ciente destas tabelas e permite-lhe ver e transferi-los.
* Escreva uma aplicação personalizada ou um script para ler e armazenar as tabelas.

Muitas ferramentas de navegação de armazenamento de terceiros estão cientes estas tabelas e permitem-lhe vê-los diretamente.
Consulte [ferramentas de cliente de armazenamento do Azure](storage-explorers.md) para obter uma lista das ferramentas disponíveis.

> [!NOTE]
> Começando com a versão 0.8.0 do [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/), pode ver e transferir as tabelas de métricas da análise.
> 
> 

Para poder aceder programaticamente as tabelas de análise, tenha em atenção que as tabelas de análise não são apresentados se lista todas as tabelas na sua conta de armazenamento. Pode aceder às mesmas diretamente pelo nome, ou utilizar o [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) na biblioteca de cliente do .NET para consultar os nomes de tabela.

### <a name="hourly-metrics"></a>Métricas por hora
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Métricas de minutos
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacidade
* $MetricsCapacityBlob

Pode encontrar detalhes completos dos esquemas para estas tabelas em [armazenamento esquema da tabela de métricas de análise](https://msdn.microsoft.com/library/azure/hh343264.aspx). As linhas de exemplo abaixo mostram apenas um subconjunto das colunas disponíveis, mas ilustram algumas funcionalidades importantes da forma como métricas do Storage guarda estas métricas:

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilidade | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |utilizador; Todos os |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |utilizador; QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |utilizador; QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |utilizador; UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

Dados de métricas minuto neste exemplo, a chave de partição utiliza o tempo em minuto resolução. A chave de linha identifica o tipo de informações que são armazenadas na linha. A chave de linha é composta por duas partes de informações, o tipo de acesso e o tipo de pedido:

* O tipo de acesso é um utilizador ou sistema, onde o utilizador refere-se a todos os pedidos de utilizador para o serviço de armazenamento e sistema refere-se a pedidos efetuados através da análise de armazenamento.
* O tipo de pedido é tudo situação em que esta é uma linha de resumo, ou identifica a API específica, tais como QueryEntity ou UpdateEntity.

Os dados de exemplo acima mostram todos os registos para um único minuto (começando às 11:00), pelo que o número de pedidos de QueryEntities mais o número de QueryEntity pedidos mais o número de UpdateEntity pedidos adicionar até sete, que é o total apresentado na linha de utilizador: All. Da mesma forma, podem derivar a latência média de ponto a ponto 104.4286 na linha de utilizador: All calculando ((143.8 * 5) + 3 + 9) / 7.

## <a name="metrics-alerts"></a>Alertas de métricas
Deve considerar como configurar alertas no [portal do Azure](https://portal.azure.com) para as métricas do Storage pode automaticamente notificá-lo de alterações importantes no comportamento dos seus serviços de armazenamento. Se utilizar uma ferramenta do Explorador de armazenamento para transferir estes dados de métricas num formato delimitado, pode utilizar o Microsoft Excel para analisar os dados. Consulte [ferramentas de cliente de armazenamento do Azure](storage-explorers.md) para obter uma lista das ferramentas do Explorador de armazenamento disponível. Pode configurar alertas no **regras de alerta** painel, acessível com **monitorização** no painel de menu de conta de armazenamento.

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados de métricas de hora a hora ou minuto correspondente são registados. Quando o registo de métricas minutos, vários minutos de dados podem ser escritos em simultâneo. Transações de minutos anteriores, em seguida, podem ser agregadas numa transação para o minuto atual. Quando isto acontecer, o serviço de alerta poderão não ter todos os dados de métricas disponíveis para o intervalo de alerta configurado, que pode levar a alertas acionando inesperadamente.
>

## <a name="accessing-metrics-data-programmatically"></a>Aceder aos dados de métricas programaticamente
A lista seguinte mostra c# código de exemplo que acede às métricas de minutos para um intervalo de minutos e apresenta os resultados numa janela de consola. Utiliza a biblioteca de armazenamento do Azure versão 4, que inclui a classe de CloudAnalyticsClient que simplifica a aceder as tabelas de métricas no armazenamento.

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
        Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
        var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
        var t = analyticsClient.GetMinuteMetricsTable(service);
        var opContext = new OperationContext();
        var query =
          from entity in metricsQuery
          // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
          // because they are calculated fields in the MetricsEntity class.
          // The PartitionKey identifies the DataTime of the metrics.
          where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
        select entity;

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
        var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
        var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
        Console.WriteLine(resultString);
    }
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)
{
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
      string.Format("Time: {0}, ", entity.Time) +
      string.Format("AccessType: {0}, ", entity.AccessType) +
      string.Format("TransactionType: {0}, ", entity.TransactionType) +
      string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
}
```

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Cobra o que pode implicar ao ativar as métricas do storage?
Escreva pedidos para criar as entidades da tabela de métricas-lhe cobrados às tarifas aplicáveis a todas as operações de armazenamento do Azure.

Pedidos de leitura e eliminar por um cliente para dados de métricas também são facturável às taxas padrão. Se tiver configurado uma política de retenção de dados, não lhe serem cobrados ao Storage do Azure elimina dados antigos de métricas. No entanto, se eliminar dados de análise, a sua conta é cobrada para as operações de eliminação.

A utilizada pelas tabelas de métricas de capacidade também está sujeito a faturação: pode utilizar o seguinte para estimar a quantidade de capacidade utilizada para armazenar dados de métricas:

* Se a cada hora um serviço utiliza a cada API em cada serviço, em seguida, aproximadamente 148KB de dados é armazenado nas tabelas de transação métricas a cada hora se tiver ativado o serviço e o nível da API resumo.
* Se a cada hora um serviço utiliza a cada API em cada serviço, em seguida, aproximadamente 12KB de dados é armazenado nas tabelas de transação métricas a cada hora se tiver ativado apenas nível de serviço resumo.
* A tabela de capacidade para os blobs tem duas linhas adicionadas por dia (fornecidos pelo utilizador foi optada ativamente por participar para os registos): Isto implica que todos os dias o tamanho desta tabela aumenta ao até aproximadamente 300 bytes.

## <a name="next-steps"></a>Passos seguintes
[Ativar o armazenamento de registo e aceder aos dados de registo](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)

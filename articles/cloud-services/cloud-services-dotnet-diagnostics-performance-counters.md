---
title: "Utilizar os contadores de desempenho no diagnóstico do Azure | Microsoft Docs"
description: "Utilize os contadores de desempenho na máquina virtual ou cloud services do Azure para localizar congestionamentos e otimizar o desempenho."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: fc4c61e9-d49d-4ed9-a32c-b91cdf851909
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/29/2016
ms.author: robb
ms.openlocfilehash: 2cf765cb034725199127c547a9b8b997a4a6089c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Criar e utilizar os contadores de desempenho na aplicação do Azure
Este artigo descreve as vantagens do e como colocar os contadores de desempenho na sua aplicação do Azure. Pode utilizá-los para recolher dados, localizar congestionamentos e otimizar o desempenho do sistema e de aplicações.

Contadores de desempenho disponíveis para o Windows Server, IIS e ASP.NET também podem ser recolhidos e utilizados para determinar o estado de funcionamento das suas funções da web do Azure, as funções de trabalho e máquinas virtuais. Também pode criar e utilizar os contadores de desempenho personalizados.  

Pode examinar os dados de contador de desempenho

1. Diretamente no anfitrião de aplicação com a ferramenta de Monitor de desempenho acedido através de ambiente de trabalho remoto
2. Com o System Center Operations Manager utilizando o pacote de gestão do Azure
3. Com outras ferramentas de monitorização que acedem os dados de diagnóstico transferidos para o armazenamento do Azure. Consulte [loja e ver os dados de diagnóstico no armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) para obter mais informações.  

Para obter mais informações sobre como monitorizar o desempenho da sua aplicação no [portal do Azure](http://portal.azure.com/), consulte [como os serviços de nuvem de Monitor](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Para obter orientações aprofundadas adicionais sobre como criar um registo e a estratégia de rastreio e a utilização de diagnóstico e de outras técnicas para resolução de problemas e otimizar aplicações do Azure, consulte [resolução de problemas de melhores práticas para desenvolvimento do Azure Aplicações](https://msdn.microsoft.com/library/azure/hh771389.aspx).

## <a name="enable-performance-counter-monitoring"></a>Ativar a monitorização do contador de desempenho
Contadores de desempenho não estão ativados por predefinição. A aplicação ou uma tarefa de arranque tem de modificar a configuração do agente de diagnóstico predefinido para incluir os contadores de desempenho específicos que pretende monitorizar para cada instância de função.

### <a name="performance-counters-available-for-microsoft-azure"></a>Contadores de desempenho disponíveis para o Microsoft Azure
O Azure oferece um subconjunto dos contadores de desempenho disponíveis para o Windows Server, IIS e a pilha do ASP.NET. A tabela seguinte lista alguns dos contadores de desempenho do especial interesse para aplicações do Azure.

| Categoria do contador: Objeto (instância) | Nome do contador | Referência |
| --- | --- | --- |
| Exceções de CLR de .NET (*Global*) |# Exceções iniciadas / seg |Contadores de desempenho de exceção |
| Memória CLR de .NET (*Global*) |% De tempo na GC |Contadores de desempenho de memória |
| ASP.NET |Reinício da aplicação |Contadores de desempenho para o ASP.NET |
| ASP.NET |Tempo de execução do pedido |Contadores de desempenho para o ASP.NET |
| ASP.NET |Pedidos desligados |Contadores de desempenho para o ASP.NET |
| ASP.NET |Reinício do processo de trabalho |Contadores de desempenho para o ASP.NET |
| Aplicações do ASP.NET (**Total**) |Total de pedidos |Contadores de desempenho para o ASP.NET |
| Aplicações do ASP.NET (**Total**) |Pedidos/seg |Contadores de desempenho para o ASP.NET |
| ASP.NET v 4.0.30319 |Tempo de execução do pedido |Contadores de desempenho para o ASP.NET |
| ASP.NET v 4.0.30319 |Tempo de espera do pedido |Contadores de desempenho para o ASP.NET |
| ASP.NET v 4.0.30319 |Pedidos atuais |Contadores de desempenho para o ASP.NET |
| ASP.NET v 4.0.30319 |Pedidos colocados em fila |Contadores de desempenho para o ASP.NET |
| ASP.NET v 4.0.30319 |Pedidos rejeitados |Contadores de desempenho para o ASP.NET |
| Memória |MBytes disponíveis |Contadores de desempenho de memória |
| Memória |Bytes consolidadas |Contadores de desempenho de memória |
| Processor(_Total) |% Tempo do processador |Contadores de desempenho para o ASP.NET |
| TCPv4 |Falhas de ligação |Objeto TCP |
| TCPv4 |Ligações estabelecidas |Objeto TCP |
| TCPv4 |Reposição de ligações |Objeto TCP |
| TCPv4 |Segmentos enviados/seg |Objeto TCP |
| Interface(*) de rede |Bytes recebidos/seg |Objeto de Interface de rede |
| Interface(*) de rede |Bytes enviados/seg |Objeto de Interface de rede |
| Interface de rede (_2 de adaptador de rede de barramento de Máquina Virtual da Microsoft) |Bytes recebidos/seg |Objeto de Interface de rede |
| Interface de rede (_2 de adaptador de rede de barramento de Máquina Virtual da Microsoft) |Bytes enviados/seg |Objeto de Interface de rede |
| Interface de rede (_2 de adaptador de rede de barramento de Máquina Virtual da Microsoft) |Bytes totais/seg |Objeto de Interface de rede |

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Criar e adicionar os contadores de desempenho personalizado à sua aplicação
O Azure tem suporte para criar e modificar os contadores de desempenho personalizado para funções da web e funções de trabalho. Os contadores podem ser utilizados para controlar e monitorizar o comportamento de específicas da aplicação. Pode criar e eliminar categorias de contador de desempenho personalizados e os especificadores de uma tarefa de arranque, a função da web ou a função de trabalho com permissões elevadas.

> [!NOTE]
> Código que faz alterações os contadores de desempenho personalizado tem de ter permissões para executar elevados. Se o código é uma função da web ou função de trabalho, a função tem de incluir a tag <Runtime executionContext="elevated" /> no ficheiro servicedefinition. Csdef para a função foi corretamente inicializado.
>
>

Pode enviar dados de contador de desempenho personalizados para o armazenamento do Azure utilizando o agente de diagnóstico.

Os dados de contador de desempenho padrão são gerados pelos processos do Azure. Dados de contador de desempenho personalizado tem de ser criados pela sua aplicação de função web ou função de trabalho. Consulte [tipos de contador de desempenho](https://msdn.microsoft.com/library/z573042h.aspx) para obter informações sobre os tipos de dados que podem ser armazenados nos contadores de desempenho personalizados. Consulte [PerformanceCounters exemplo](http://code.msdn.microsoft.com/azure/) para obter um exemplo que cria e define os dados do contador de desempenho personalizados numa função da web.

## <a name="store-and-view-performance-counter-data"></a>Arquivo e a vista de dados do contador de desempenho
Azure coloca em cache os dados de contador de desempenho com outras informações de diagnóstico. Estes dados estão disponíveis para monitorização remota, enquanto a instância de função está em execução com acesso de ambiente de trabalho remoto para ver as ferramentas, como o Monitor de desempenho. Para manter os dados fora da instância de função, o agente de diagnóstico tem de transferir os dados para o armazenamento do Azure. O limite de tamanho dos dados de contador de desempenho em cache pode ser configurado no agente de diagnóstico, ou pode ser configurado para fazer parte de um limite de partilhada para todos os dados de diagnóstico. Para obter mais informações sobre como definir o tamanho da memória intermédia, consulte [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) e [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Consulte [loja e ver os dados de diagnóstico no armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) para uma descrição geral de como configurar o agente de diagnóstico para transferir dados para uma conta de armazenamento.

Cada instância do contador de desempenho configurados é registada uma taxa de amostragem especificado e amostras de dados são transferidas para a conta de armazenamento, um pedido de transferência agendada ou um pedido de transferência a pedido. Transferências automáticas podem ser agendadas no máximo, uma vez por minuto. Dados de contador de desempenho transferidos pelo agente de diagnóstico são armazenados numa tabela, WADPerformanceCountersTable, na conta de armazenamento. Esta tabela pode ser acedida e consultar com métodos de armazenamento do Azure standard API. Consulte [exemplo do Microsoft Azure PerformanceCounters](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) para obter um exemplo de consulta e apresentar dados de contador de desempenho da tabela WADPerformanceCountersTable.

> [!NOTE]
> Consoante a frequência de transferência do agente de diagnóstico e a latência de fila, os dados de contador de desempenho mais recentes na conta de armazenamento poderá vários minutos desatualizada.
>
>

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Ativar os contadores de desempenho com o ficheiro de configuração de diagnóstico
Utilize o procedimento seguinte para ativar os contadores de desempenho na sua aplicação do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que ter importado o monitor de diagnóstico para a aplicação e adicionar o ficheiro de configuração de diagnóstico para a sua solução Visual Studio (diagnostics.wadcfg no SDK 2.4 e abaixo ou diagnostics.wadcfgx no SDK 2.5 e superior). Consulte os passos 1 e 2 no [ativar diagnósticos no Cloud Services do Azure e máquinas virtuais](cloud-services-dotnet-diagnostics.md)) para obter mais informações.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Passo 1: Recolher e armazenar dados de contadores de desempenho
Depois de ter adicionado o ficheiro de diagnóstico para a sua solução Visual Studio, pode configurar a recolha e o armazenamento de dados do contador de desempenho na aplicação do Azure. Isto é feito adicionando os contadores de desempenho para o ficheiro de diagnóstico. Dados de diagnóstico, incluindo os contadores de desempenho, primeiro são recolhidos na instância. Os dados, em seguida, são continuados à tabela WADPerformanceCountersTable no serviço de Azure Table, pelo que também terá de especificar a conta de armazenamento na sua aplicação. Se estiver a testar a aplicação localmente no emulador de computação, pode também armazenar dados de diagnóstico localmente no emulador do Storage. Antes de o armazenar dados de diagnóstico, tem de ir primeiro para o [portal do Azure](http://portal.azure.com/) e crie uma conta de armazenamento clássico. É uma melhor prática localizar a conta do storage na mesma georreplicação-localização que a aplicação do Azure. Ao manter que as aplicações do Azure e a conta de armazenamento estão na mesma geolocalização, evitar pagar os custos de largura de banda externa e reduzir a latência.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Adicionar contadores de desempenho para o ficheiro de diagnóstico
Existem muitos contadores, que pode utilizar. O exemplo seguinte mostra vários contadores de desempenho que são recomendados para web e a monitorização da função de trabalho.

Abra o ficheiro de diagnóstico (diagnostics.wadcfg no SDK 2.4 e abaixo ou diagnostics.wadcfgx no SDK 2.5 e acima) e adicione o seguinte para o elemento de DiagnosticMonitorConfiguration:

```xml
<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
    <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
</PerformanceCounters>
```

O atributo bufferQuotaInMB que especifica a quantidade máxima de armazenamento do sistema de ficheiros que está disponível para o tipo de coleção de dados (os registos do Azure, registos de IIS, etc.). A predefinição é 0. Quando for atingida a quota, os dados mais antigos são eliminados como sendo adicionados dados novos. A soma de todas as propriedades de bufferQuotaInMB tem de ser maior que o valor do atributo OverallQuotaInMB. Para ver um debate mais detalhado de determinar a quantidade de armazenamento será necessária para a recolha de dados de diagnóstico, consulte a secção de configuração WAD de [resolução de problemas de melhores práticas para desenvolver aplicações do Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

O atributo scheduledTransferPeriod, que especifica o intervalo entre agendada transferências de dados, arredondados por excesso para o minuto mais próximo. Nos exemplos a seguir, está definido para PT30M (30 minutos). Definir o período de transferência para um valor pequeno, por exemplo, 1 minuto, negativamente irá afetar o desempenho da aplicação na produção, mas pode ser útil para obter um diagnóstico ver a trabalhar rapidamente quando estiver a testar. O período de transferência agendada deve ser suficientemente pequeno para se certificar de que os dados de diagnóstico não são substituídos na instância, mas suficientemente grande para que não irá afetar o desempenho da sua aplicação.

O atributo counterSpecifier Especifica o contador de desempenho para recolher. O atributo sampleRate Especifica a velocidade a que o contador de desempenho deve ser objeto de amostragem, neste caso, 30 segundos.

Depois de adicionar os contadores de desempenho que pretende recolher, guarde as alterações para o ficheiro de diagnóstico. Em seguida, terá de especificar a conta de armazenamento que os dados de diagnóstico serão transferidos para o.

### <a name="specify-the-storage-account"></a>Especifique a conta de armazenamento
Para manter as informações de diagnóstico para a sua conta do Storage do Azure, tem de especificar uma cadeia de ligação no ficheiro de configuração (serviceconfiguration. Cscfg) do serviço.

Para o Azure SDK 2.5, a conta de armazenamento pode ser especificada no ficheiro diagnostics.wadcfgx.

> [!NOTE]
> Estas instruções aplicam-se apenas para o Azure SDK 2.4 e abaixo. Para o Azure SDK 2.5, a conta de armazenamento pode ser especificada no ficheiro diagnostics.wadcfgx.
>
>

Para definir as cadeias de ligação:

1. Abra o ficheiro de Serviceconfiguration utilizando o editor de texto favorito e definir a cadeia de ligação para o armazenamento. O *AccountName* e *AccountKey* valores encontram-se no portal do Azure no dashboard de conta de armazenamento, em chaves de acesso.

    ```xml
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Guarde o ficheiro Serviceconfiguration.
3. Abra o ficheiro ServiceConfiguration.Local.cscfg e certifique-se de que o UseDevelopmentStorage está definido como true.

    ```xml
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
   Agora que as cadeias de ligação estão definidas, a aplicação irá manter os dados de diagnóstico para a sua conta de armazenamento quando a aplicação for implementada.
4. Guardar e criar o projeto e, em seguida, implementar a sua aplicação.

## <a name="step-2-optional-create-custom-performance-counters"></a>Passo 2: Os contadores de desempenho personalizados (opcional) criar
Para além dos contadores de desempenho predefinidos, pode adicionar os seus próprios contadores de desempenho personalizados para monitorizar funções web ou de trabalho. Contadores de desempenho personalizados podem ser utilizados para controlar e monitorizar o comportamento específicas da aplicação e pode ser criada ou eliminada numa tarefa de arranque, a função da web ou a função de trabalho com permissões elevadas.

O agente de diagnóstico do Azure atualiza a contador configuração de desempenho do ficheiro .wadcfg um minuto após a iniciar.  Se criar os contadores de desempenho personalizado no método OnStart e as tarefas de arranque demorar mais do que um minuto para ser executado, os contadores de desempenho personalizados serão não ter sido criados quando o agente de diagnóstico do Azure tenta carregá-los.  Neste cenário, verá que o Azure Diagnostics captura corretamente todos os dados de diagnóstico, exceto os contadores de desempenho personalizados.  Para resolver este problema, crie os contadores de desempenho numa tarefa de arranque ou mover algumas das suas tarefas de arranque de trabalho para o método OnStart depois de criar os contadores de desempenho.

Execute os seguintes passos para criar um desempenho personalizado simple contador com o nome "\MyCustomCounterCategory\MyButton1Counter":

1. Abra o ficheiro de definição de serviço (. CSDEF) para a sua aplicação.
2. Adicione o elemento de tempo de execução para a WebRole ou elemento WorkerRole para permitir a execução com privilégios elevados:

    ```xml
    <runtime executioncontext="elevated"/>
    ```
3. Guarde o ficheiro.
4. Abra o ficheiro de diagnóstico (diagnostics.wadcfg no SDK 2.4 e abaixo ou diagnostics.wadcfgx no SDK 2.5 e acima) e adicione o seguinte para o DiagnosticMonitorConfiguration

    ```xml
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
      <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Guarde o ficheiro.
6. Crie a categoria de contador de desempenho personalizados no método OnStart da sua função, antes de invocar base. OnStart. O exemplo do c# seguinte cria uma categoria personalizada, se já existir:

    ```csharp
    public override bool OnStart()
    {
      if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
      {
         CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

         // add a counter tracking user button1 clicks
         CounterCreationData operationTotal1 = new CounterCreationData();
         operationTotal1.CounterName = "MyButton1Counter";
         operationTotal1.CounterHelp = "My Custom Counter for Button1";
         operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
         counterCollection.Add(operationTotal1);

         PerformanceCounterCategory.Create(
           "MyCustomCounterCategory",
           "My Custom Counter Category",
           PerformanceCounterCategoryType.SingleInstance, counterCollection);

         Trace.WriteLine("Custom counter category created.");
      }
      else {
        Trace.WriteLine("Custom counter category already exists.");
      }

    return base.OnStart();
    }
    ```
7. Atualize os contadores na sua aplicação. O exemplo seguinte atualiza um contador de desempenho personalizados Button1_Click eventos:

    ```csharp
    protected void Button1_Click(object sender, EventArgs e)
    {
      PerformanceCounter button1Counter = new PerformanceCounter(
        "MyCustomCounterCategory",
        "MyButton1Counter",
        string.Empty,
        false);
      button1Counter.Increment();
      this.Button1.Text = "Button 1 count: " +
        button1Counter.RawValue.ToString();
    }
    ```
8. Guarde o ficheiro.  

Agora serão recolhidos dados de contador de desempenho personalizado pelo monitor de diagnóstico do Azure.

## <a name="step-3-query-performance-counter-data"></a>Passo 3: Dados de contador de desempenho de consulta
Depois da aplicação é implementada e em execução, o monitor de diagnóstico começará a recolher contadores de desempenho e a persistência de dados para o armazenamento do Azure. Utilize ferramentas como o Explorador de servidores no Visual Studio, [Explorador de armazenamento do Azure](http://azurestorageexplorer.codeplex.com/), ou [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) por Cerebrata para ver o desempenho contadores dados o WADPerformanceCountersTable tabela. Através de programação também pode consultar o serviço de tabela utilizando [c#](../cosmos-db/table-storage-how-to-use-dotnet.md), [Java](../cosmos-db/table-storage-how-to-use-java.md), [Node.js](../cosmos-db/table-storage-how-to-use-nodejs.md), [Python](../cosmos-db/table-storage-how-to-use-python.md), [Ruby](../cosmos-db/table-storage-how-to-use-ruby.md), ou [PHP](../cosmos-db/table-storage-how-to-use-php.md).

O exemplo do c# seguinte mostra uma consulta básica na tabela WADPerformanceCountersTable e guarda os dados de diagnóstico para um ficheiro CSV. Depois dos contadores de desempenho são guardados para um ficheiro CSV, pode utilizar as capacidades graphing no Microsoft Excel ou alguns outra ferramenta para visualizar os dados. Lembre-se de que adicione uma referência a Microsoft.WindowsAzure.Storage.dll, que é incluído no SDK do Azure para .NET Outubro de 2012 e posterior. A assemblagem está instalada para o diretório de programa Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version num\ref\ %.

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Table;
...

// Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
// you store your connection string using the Microsoft Azure service configuration
// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
// to retrieve your storage connection string.  If you're not using Cloud Services, it's
// recommended that you store the connection string in your web.config or app.config file.
// Use the ConfigurationManager type to retrieve your storage connection string.

string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

// Get a reference to the storage account using the connection string.  You can also use the development
// storage account (Storage Emulator) for local debugging.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
//CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

// Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
  .Where(
    TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
      TableOperators.And,
      TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
      TableOperators.And,
      TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
    )
  )
);

// Execute the table query.
IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

// Process the query results and build a CSV file.
StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

foreach (PerformanceCountersEntity entity in result)
{
  sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
    + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
}

StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
sw.Write(sb.ToString());
sw.Close();
```

As entidades mapeiam para objetos c# utilizando uma classe personalizada derivada de **TableEntity**. O código seguinte define uma classe de entidade que representa um contador de desempenho no **WADPerformanceCountersTable** tabela.

```csharp
public class PerformanceCountersEntity : TableEntity
{
  public long EventTickCount { get; set; }
  public string DeploymentId { get; set; }
  public string Role { get; set; }
  public string RoleInstance { get; set; }
  public string CounterName { get; set; }
  public double CounterValue { get; set; }
}
```

## <a name="next-steps"></a>Passos Seguintes
[Vista de artigos adicionais no diagnóstico do Azure](../azure-diagnostics.md)

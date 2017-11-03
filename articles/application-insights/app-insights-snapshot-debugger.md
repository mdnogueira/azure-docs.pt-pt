---
title: "Depurador de instantâneos de Azure Application Insights para aplicações de .NET | Microsoft Docs"
description: "Depurar instantâneos são recolhidos automaticamente quando as exceções forem emitidas nas aplicações de .NET de produção"
services: application-insights
documentationcenter: 
author: pharring
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: mbullwin
ms.openlocfilehash: 5a0344dcef779a9818be3e320bd5c269a2859f71
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantâneos em exceções em aplicações de .NET

Quando ocorre uma exceção, pode recolher automaticamente um instantâneo de depuração da sua aplicação web em direto. O instantâneo mostra o estado do código de origem e as variáveis de momento, que a exceção foi acionada. O depurador de instantâneo (pré-visualização) no [Azure Application Insights](app-insights-overview.md) monitoriza a telemetria de exceção da aplicação web. Recolhe os instantâneos no seu exceções a gerar a parte superior para que tenha as informações necessárias diagnosticar problemas na produção. Incluir o [pacotes de NuGet do recoletor de instantâneo](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) na sua aplicação e, opcionalmente, configurar os parâmetros da coleção em [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md). Instantâneos são apresentados no [exceções](app-insights-asp-net-exceptions.md) no portal do Application Insights.

Pode ver instantâneos de depuração no portal para ver a chamada da pilha e inspecionar as variáveis em cada frame de pilha de chamadas. Para obter uma experiência de depuração mais poderosa com o código de origem, abra instantâneos com o Visual Studio Enterprise de 2017 por [transferir a extensão de depurador de instantâneo para o Visual Studio](https://aka.ms/snapshotdebugger). No Visual Studio, pode também [definir Snappoints interativamente tirar instantâneos](https://aka.ms/snappoint) sem aguardar por uma exceção.

Coleção de instantâneo está disponível para:
* Aplicações de .NET framework e ASP.NET com o .NET Framework 4.5 ou posterior.
* Aplicações de .NET core 2.0 e o ASP.NET Core 2.0 em execução no Windows.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurar a recolha de instantâneo para aplicações ASP.NET

1. [Ativar o Application Insights na aplicação web](app-insights-asp-net.md), se ainda não o fez, ainda.

2. Incluir o [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet na sua aplicação. 

3. Reveja as opções predefinidas que o pacote adicionado ao [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in one minute. -->
        <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Instantâneos são recolhidos apenas em exceções são comunicadas ao Application Insights. Em alguns casos (por exemplo, versões mais antigas da plataforma .NET), poderá ser necessário [configurar a recolha de exceção](app-insights-asp-net-exceptions.md#exceptions) para ver as exceções com instantâneos no portal.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurar a recolha de instantâneo para aplicações ASP.NET Core 2.0

1. [Ativar o Application Insights na aplicação web ASP.NET Core](app-insights-asp-net-core.md), se ainda não o fez, ainda.

> [!NOTE]
> Ser se de que a aplicação referencia a versão 2.1.1 ou mais recente do pacote Microsoft.ApplicationInsights.AspNetCore.

2. Incluir o [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet na sua aplicação.

3. Modificar a sua aplicação `Startup` classe para adicionar e configurar o processador de telemetria do Recoletor de instantâneo.

   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }

       public Startup(IConfiguration configuration) => Configuration = configuration;

       public IConfiguration Configuration { get; }

       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Configure o Recoletor do instantâneo ao adicionar uma secção SnapshotCollectorConfiguration para appsettings.json. Por exemplo:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": true
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar a recolha de instantâneo para outras aplicações de .NET

1. Se a aplicação já está instrumentada não com o Application Insights, começar por [ativar Application Insights e definir a chave de instrumentação](app-insights-windows-desktop.md).

2. Adicionar o [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pacote NuGet na sua aplicação.

3. Instantâneos são recolhidos apenas em exceções são comunicadas ao Application Insights. Poderá ter de modificar o código para reportá-los. A exceção de código de processamento depende a estrutura da sua aplicação, mas é um exemplo abaixo:
    ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```
    
## <a name="grant-permissions"></a>Conceder permissões

Os proprietários da subscrição do Azure podem inspecionar instantâneos. Outros utilizadores devem ser concedidos permissão por um proprietário.

Para conceder permissão, atribua o `Application Insights Snapshot Debugger` função para utilizadores que irá inspecionar instantâneos. Esta função pode ser atribuída a utilizadores individuais ou grupos por proprietários de subscrição para o recurso do Application Insights de destino ou o grupo de recursos ou subscrição.

1. Abra o painel de controlo de acesso (IAM).
1. Clique o botão + Adicionar.
1. Selecione o depurador de instantâneo do Application Insights na lista pendente de funções.
1. Procure e introduza um nome para o utilizador adicione.
1. Clique no botão Guardar para adicionar o utilizador à função.


[!IMPORTANT]
    Instantâneos potencialmente podem conter pessoais e outras informações confidenciais numa variável e o parâmetro de valores.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Depurar instantâneos no portal do Application Insights

Se um instantâneo está disponível para uma determinada exceção ou um ID do problema, uma **Abrir instantâneo de depuração** botão aparece o [exceção](app-insights-asp-net-exceptions.md) no portal do Application Insights.

![Abra o instantâneo de depuração botão na exceção](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Na vista de instantâneo de depuração, verá uma pilha de chamadas e um painel de variáveis. Quando seleciona fotogramas da pilha de chamadas no painel de pilha de chamadas, pode ver as variáveis locais e parâmetros para essa função chamada no painel de variáveis.

![Vista de depuração instantâneo no portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Instantâneos poderão conter informações confidenciais e, por predefinição não estão visíveis. Para ver os instantâneos, tem de ter o `Application Insights Snapshot Debugger` função atribuída.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Depurar instantâneos com o Visual Studio Enterprise de 2017
1. Clique em de **transferir instantâneo** botão para transferir um `.diagsession` ficheiro, que pode ser aberto por Visual Studio Enterprise de 2017. 

2. Para abrir o `.diagsession` ficheiro, deve primeiro [transferir e instalar a extensão de depurador de instantâneo para o Visual Studio](https://aka.ms/snapshotdebugger).

3. Depois de abrir o ficheiro do instantâneo, é apresentada a página de depuração de mini-cópia de segurança de mensagens em fila no Visual Studio. Clique em **depurar código gerido** para iniciar a depuração do instantâneo. Abre o instantâneo para a linha de código onde a exceção foi acionada para que pode depurar o estado atual do processo.

    ![Instantâneo de depuração de vista no Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo transferido contém os ficheiros de símbolo que foram encontrados no seu servidor de aplicação web. Estes ficheiros de símbolo são necessárias para associar dados de instantâneo com o código de origem. Para aplicações de serviço de aplicações, certifique-se ativar a implementação de símbolo quando publica as suas aplicações web.

## <a name="how-snapshots-work"></a>Como funcionam os instantâneos

Quando inicia a sua aplicação, é criado um processo de /carregador instantâneo separado que monitoriza a sua aplicação para pedidos de instantâneo. Quando é pedido um instantâneo, é efetuada uma cópia sombra do processo em execução em cerca de 10 a 20 milissegundos. O processo de sombra de volumes, em seguida, é analisado e é criado um instantâneo, enquanto o processo principal continua a executar e enviam tráfego para os utilizadores. O instantâneo, em seguida, é enviado para o Application Insights, juntamente com quaisquer ficheiros de símbolo relevantes (.pdb) que são necessárias para ver o instantâneo.

## <a name="current-limitations"></a>Limitações atuais

### <a name="publish-symbols"></a>Publicar símbolos
O depurador de instantâneo necessita que os ficheiros de símbolo no servidor de produção para descodificar variáveis e fornecer uma experiência de depuração no Visual Studio. A versão 15.2 do Visual Studio 2017 publica símbolos para compilações de versão por predefinição quando publica no App Service. Nas versões anteriores, tem de adicionar a seguinte linha para o seu perfil de publicação `.pubxml` para que os símbolos são publicados no modo de versão do ficheiro:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para computação do Azure e outros tipos, certifique-se de que os ficheiros de símbolo estão na mesma pasta do ficheiro. dll de aplicação principal (normalmente, `wwwroot/bin`) ou estão disponíveis no caminho atual.

### <a name="optimized-builds"></a>Compilações otimizadas
Em alguns casos, variáveis locais não podem ser visualizadas na versão compilações devido a otimizações que são aplicadas durante o processo de compilação.

## <a name="troubleshooting"></a>Resolução de problemas

Estas sugestões ajudam a resolver problemas relacionados com o depurador de instantâneo.

### <a name="verify-the-instrumentation-key"></a>Certifique-se a chave de instrumentação

Certifique-se de que está a utilizar a chave de instrumentação correto na sua aplicação publicada. Normalmente, o Application Insights lê a chave de instrumentação do ficheiro Applicationinsights. Certifique-se de que o valor é igual a chave de instrumentação para o recurso do Application Insights que vir no portal.

### <a name="check-the-uploader-logs"></a>Verifique os registos de /carregador

Depois de criar um instantâneo, é criado um ficheiro de mini-cópia de segurança (. dmp) no disco. Um processo separado /carregador assume que ficheiros de mini-cópia de segurança e carrega, juntamente com quaisquer PDBs associados, para o armazenamento de depurador de instantâneo do Application Insights. Depois de mini-cópia de segurança foi carregado com êxito, é eliminado do disco. Os ficheiros de registo para o /carregador de mini-cópia de segurança são mantidos no disco. Num ambiente de serviço de aplicações, pode encontrar estes registos no `D:\Home\LogFiles\Uploader_*.log`. Utilize o site de gestão do Kudu para o App Service para localizar estes ficheiros de registo.

1. Abra a aplicação do serviço de aplicação no portal do Azure.

2. Selecione o **ferramentas avançadas** painel ou procure **Kudu**.
3. Clique em **aceda**.
4. No **consola de depuração** caixa de lista pendente, selecione **CMD**.
5. Clique em **LogFiles**.

Deverá ver pelo menos um ficheiro com um nome que comece com `Uploader_` e um `.log` extensão. Clique no ícone apropriado para transferir os ficheiros de registo ou abrir num browser.
O nome de ficheiro inclui o nome da máquina. Se a instância de serviço de aplicações estiver alojada em mais do que um computador, existem ficheiros de registo separados para cada máquina. Quando o /carregador Deteta um novo ficheiro de mini-cópia de segurança, é registada no ficheiro de registo. Eis um exemplo de um carregamento bem-sucedido:

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

No exemplo anterior, é a chave de instrumentação `c12a605e73c44346a984e00000000000`. Este valor deve corresponder a chave de instrumentação para a sua aplicação.
A mini-cópia de segurança está associada um instantâneo com o ID `139e411a23934dc0b9ea08a626db16c5`. Pode utilizar este ID mais tarde para localizar a telemetria de exceção associada no Application Insights Analytics.

O /carregador verifica a existência de novas PDBs sobre uma vez a cada 15 minutos. Eis um exemplo:

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

Para aplicações que são _não_ alojada no App Service, os registos de /carregador estão na mesma pasta que o mini-cópias de informação: `%TEMP%\Dumps\<ikey>` (onde `<ikey>` é a sua chave de instrumentação).

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Utilize Application Insights procura para localizar exceções com instantâneos

Quando é criado um instantâneo, a exceção throwing é marcada com um ID de instantâneo. Quando é reportada a telemetria de exceção para o Application Insights, que o ID de instantâneo é incluído como uma propriedade personalizada. Utilizando o painel de pesquisa do Application Insights, pode encontrar toda a telemetria com o `ai.snapshot.id` propriedade personalizada.

1. Navegue para o recurso do Application Insights no portal do Azure.
2. Clique em **pesquisa**.
3. Tipo `ai.snapshot.id` na caixa de texto de pesquisa e prima Enter.

![Procure telemetria com um ID de instantâneo no portal](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Se esta pesquisa não devolve nenhum resultado, não existem instantâneos foram comunicados ao Application Insights para a sua aplicação no intervalo de tempo selecionado.

Para procurar um ID de instantâneo específico dos registos de /carregador, escreva esse ID na caixa de pesquisa. Se não é possível localizar a telemetria de um instantâneo de que sabe que foi carregado, siga estes passos:

1. Verifique que tenciona o recurso do Application Insights correto, verificando a chave de instrumentação.

2. Utilizar timestamp do registo de /carregador, ajuste o filtro de intervalo de tempo da pesquisa para cobrir esse intervalo de tempo.

Se ainda não vir uma exceção com esse ID de instantâneo, não foi reportada a telemetria de exceção para o Application Insights. Esta situação pode acontecer se a sua aplicação falhou após demorou o instantâneo, mas antes da telemetria de exceção foi declarada. Neste caso, verifique os registos do serviço de aplicações em `Diagnose and solve problems` para ver se ocorreram reinicialização inesperada ou não processada exceções.

## <a name="next-steps"></a>Passos seguintes

* [Definir snappoints no seu código](https://docs.microsoft.com/en-us/visualstudio/debugger/debug-live-azure-applications) obter instantâneos sem aguardar por uma exceção.
* [Diagnosticar exceções nas suas aplicações web](app-insights-asp-net-exceptions.md) explica como tornar mais exceções visíveis para o Application Insights. 
* [Deteção de smart](app-insights-proactive-diagnostics.md) Deteta automaticamente anomalias de desempenho.

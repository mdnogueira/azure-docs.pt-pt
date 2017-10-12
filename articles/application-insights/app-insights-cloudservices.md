---
title: "Application Insights para os Serviços Cloud do Azure | Microsoft Docs"
description: "Monitorizar as funções Web e de trabalho eficazmente com o Application Insights"
services: application-insights
documentationcenter: 
keywords: "WAD2AI, Diagnóstico do Azure"
author: CFreemanwa
manager: carmonm
editor: alancameronwills
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.devlang: na
ms.tgt_pltfrm: ibiza
ms.topic: get-started-article
ms.workload: tbd
ms.date: 05/05/2017
ms.author: bwren
ms.openlocfilehash: c12b225aa351d0c272243469550791a6840091e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights para os Serviços Cloud do Azure
As [aplicações de Serviço cloud do Microsoft Azure](https://azure.microsoft.com/services/cloud-services/) podem ser monitorizadas pelo [Application Insights][start] quanto à disponibilidade, ao desempenho, às falhas e à utilização, ao combinar dados de SDKs do Application Insights com dados do [Diagnóstico do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) dos seus Serviços Cloud. Com o feedback que recebe relativamente ao desempenho e à eficácia da sua aplicação no terreno, pode fazer escolhas informadas sobre o rumo do design em cada ciclo de vida do desenvolvimento.

![Exemplo](./media/app-insights-cloudservices/sample.png)

## <a name="before-you-start"></a>Antes de começar
Precisa de:

* Uma subscrição do [Microsoft Azure](http://azure.com). Inicie sessão com uma conta Microsoft, que poderá ter para o Windows, o XBox Live ou outros serviços cloud do Microsoft. 
* Ferramentas do Microsoft Azure 2.9 ou posterior
* Developer Analytics Tools 7.10 ou posterior

## <a name="quick-start"></a>Início rápido
A forma mais rápida e fácil de monitorizar o seu serviço cloud com o Application Insights é escolher essa opção quando publica o seu serviço no Azure.

![Exemplo](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

Esta opção instrumenta a sua aplicação no tempo de execução, dando-lhe toda a telemetria de que precisa para monitorizar pedidos, exceções e dependências na sua função da Web, bem como contadores de desempenho das suas funções de trabalho. Também são enviados para o Application Insights os rastreios de diagnóstico gerados pela sua aplicação.

Se só precisa disto, está pronto! Os próximos passos são [ver métricas da aplicação](app-insights-metrics-explorer.md), [consultar dados com o Analytics](app-insights-analytics.md) e, talvez, configurar um [dashboard](app-insights-dashboards.md). Poderá ser útil configurar [testes de disponibilidade](app-insights-monitor-web-app-availability.md) e [adicionar código às suas páginas Web](app-insights-javascript.md) para monitorizar o desempenho no browser.

Mas também pode obter mais opções:

* Enviar dados de diferentes componentes e configurações de compilação para recursos separados.
* Adicionar telemetria personalizada a partir da sua aplicação.

Se tiver interesse em alguma destas opções, continue a ler.

## <a name="sample-application-instrumented-with-application-insights"></a>Aplicação de exemplo instrumentada com o Application Insights
Veja esta [aplicação de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), em que o Application Insights é adicionado a um serviço cloud com duas funções de trabalho alojadas no Azure. 

O que se segue explica-lhe como adaptar o seu próprio projeto de serviço cloud da mesma forma.

## <a name="plan-resources-and-resource-groups"></a>Planear recursos e grupos de recursos
A telemetria da sua aplicação é armazenada, analisada e apresentada num recurso do Azure do tipo Application Insights. 

Cada recurso pertence a um grupo de recursos. Os grupos de recursos são utilizados para gerir custos, para conceder acesso a membros de equipas e para implementar atualizações numa transação coordenada individual. Por exemplo, pode [escrever um script para implementar](../azure-resource-manager/resource-group-template-deploy.md) um Serviço Cloud do Azure e os respetivos recursos de monitorização do Application Insights numa única operação.

### <a name="resources-for-components"></a>Recursos para componentes
O esquema recomendado é criar um recurso separado para cada componente da aplicação, ou seja, cada função da Web e função de trabalho. Pode analisar cada componente em separado, mas pode criar um [dashboard](app-insights-dashboards.md) que junta os gráficos-chave de todos os componentes, para que possa compará-los e monitorizá-los em conjunto. 

Um esquema alternativo consiste em enviar a telemetria a partir de mais de uma função para o mesmo recurso, mas [adicionar uma propriedade de dimensão a cada item de telemetria](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer) que identifica a respetiva função de origem. Neste esquema, os gráficos das métricas, como as exceções, mostram, geralmente, um agregado das contagens das diferentes funções, mas pode segmentá-los pelo identificador da função, quando necessário. As pesquisas também podem ser filtradas pela mesma dimensão. Esta alternativa faz com que seja um pouco mais fácil ver tudo ao mesmo tempo, mas pode levar a alguma confusão entre as funções.

Normalmente, a telemetria do browser está incluída no mesmo recurso que a função da Web do lado do servidor.

Ponha os recursos do Application Insights dos diferentes componentes num grupo de recursos. Desta forma, será mais fácil geri-los em conjunto. 

### <a name="separating-development-test-and-production"></a>Separar o desenvolvimento, os testes e a produção
Se estiver a desenvolver eventos personalizados para a sua próxima funcionalidade enquanto a versão anterior está publicada, vai querer enviar a telemetria de desenvolvimento para um recurso do Application Insights separado. Caso contrário, será difícil encontrar a telemetria de teste de entre todo o tráfego do site publicado.

Para evitar esta situação, crie recursos separados para cada configuração de compilação ou “carimbo” (desenvolvimento, testes, produção...) do seu sistema. Ponha os recursos de cada configuração de compilação num grupo de recursos separado. 

Para enviar a telemetria para os recursos adequados, pode configurar o SDK do Application Insights para que recolha uma chave de instrumentação diferente, consoante a configuração de compilação. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Criar um recurso do Application Insights para cada função
Se tiver decidido criar um recurso separado para cada função, e talvez um conjunto à parte para cada configuração de compilação, o mais fácil é criá-los todos no portal do Application Insights. (Se criar recursos muitas vezes, pode [automatizar o processo](app-insights-powershell.md)).

1. No [Portal do Azure][portal], crie um recurso do Application Insights novo. Para o tipo de aplicação, escolha a aplicação ASP.NET. 

    ![Clicar em Novo, Application Insights](./media/app-insights-cloudservices/01-new.png)
2. Repare que cada recurso é identificado por uma Chave de Instrumentação. Poderá precisar desta chave mais tarde, se quiser configurar ou verificar a configuração do SDK manualmente.

    ![Clicar em Propriedades, selecionar a chave e premir Ctrl+C](./media/app-insights-cloudservices/02-props.png) 

## <a name="set-up-azure-diagnostics-for-each-role"></a>Configurar os Diagnósticos do Azure para cada função
Defina esta opção para monitorizar a sua aplicação com o Application Insights. Para funções da Web, proporciona monitorização de desempenho, alertas e diagnósticos, bem como análises de utilização. Para outras funções, pode pesquisar e monitorizar diagnósticos do Azure, como reinícios, contadores de desempenho e chamadas para System.Diagnostics.Trace. 

1. No Explorador de Soluções do Visual Studio, em &lt;YourCloudService&gt;, Funções, abra as propriedades de cada função.
2. Em **Configuração**, defina **Enviar dados de diagnóstico para o Application Insights** e selecione o recurso do Application Insights adequado que criou anteriormente.

Se tiver optado por utilizar um recurso do Application Insights separado para cada configuração de compilação, selecione primeiro a configuração.

![Nas propriedades de cada função do Azure, configure o Application Insights](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

Isto tem o efeito de inserir as chaves de instrumentação do Application Insights nos ficheiros com o nome `ServiceConfiguration.*.cscfg`. ([Código de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).

Se quiser variar o nível de informações de diagnóstico enviadas para o Application Insights, pode fazê-lo [ao editar os ficheiros `.cscfg` diretamente](app-insights-azure-diagnostics.md).

## <a name="sdk"></a>Instalar o SDK em cada projeto
Esta opção acrescenta a capacidade de adicionar telemetria empresarial personalizada a qualquer função, para uma análise mais cuidada relativamente à utilização e ao desempenho da sua aplicação.

No Visual Studio, configure o SDK do Application Insights para cada projeto de aplicação na cloud.

1. **Funções da Web**: clique com o botão direito do rato e escolha **Configurar o Application Insights** ou **Adicionar > Telemetria do Application Insights**.

2. **Funções de trabalho**: 
 * Clique com o botão direito do rato no projeto e selecione **Gerir Pacotes Nuget**.
 * Adicione [Application Insights para Servidores Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Procurar “Application Insights”](./media/app-insights-cloudservices/04-ai-nuget.png)

3. Configure o SDK para enviar dados para o recurso do Application Insights.

    Numa função de arranque adequada, defina a chave de instrumentação da definição de configuração no ficheiro .cscfg:
 
    ```C#
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    Faça-o para cada função da aplicação. Veja os exemplos:
   
   * [Função da Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [Função de trabalho](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [Para páginas Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. Defina o ficheiro ApplicationInsights.config para ser sempre copiado para o diretório de saída. 
   
    (No ficheiro .config, verá mensagens que lhe pedem para pôr a chave de instrumentação aqui. Contudo, para as aplicações na cloud, é melhor defini-la a partir do ficheiro .cscfg. Isto garante que a função é identificada corretamente no portal.)

#### <a name="run-and-publish-the-app"></a>Executar e publicar a aplicação
Execute a aplicação e inicie sessão no Azure. Abra os recursos do Application Insights que criou e verá pontos de dados individuais aparecer em [Pesquisa](app-insights-diagnostic-search.md) e dados agregados em [Explorador de Métricas](app-insights-metrics-explorer.md). 

Adicione mais telemetria - veja as secções abaixo - e publique a sua aplicação para receber diagnósticos e feedback de utilização em direto. 

#### <a name="no-data"></a>Não existem dados?
* Abra o mosaico [Pesquisa][diagnostic] para ver eventos individuais.
* Utilize a aplicação, abrindo páginas diferentes, de modo a gerar alguma telemetria.
* Aguarde alguns segundos e clique em Atualizar.
* Veja a [Resolução de Problemas][qna].

## <a name="view-azure-diagnostic-events"></a>Ver eventos de Diagnóstico do Azure
Onde encontrar informações sobre o [Diagnóstico do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) no Application Insights:

* Os contadores de desempenho são apresentados como métricas personalizadas. 
* Os registos de eventos do Windows são mostrados como rastreios e eventos personalizados.
* Os registos de aplicações, os registos ETW e eventuais registos de infraestrutura de diagnósticos aparecem como rastreios.

Para ver contadores de desempenho e contagens de eventos, abra o [Explorador de Métricas](app-insights-metrics-explorer.md) e adicione um gráfico novo:

![Dados de diagnósticos do Azure](./media/app-insights-cloudservices/23-wad.png)

Utilize a [Pesquisa](app-insights-diagnostic-search.md) ou uma [consulta do Analytics](app-insights-analytics-tour.md) para pesquisar pelos vários registos de rastreio que o Diagnóstico do Azure envia. Por exemplo, imagine que tem uma exceção não processada que fez com que uma Função falhasse e se reciclasse. Esta informação apareceria no canal Aplicação do Registo de Eventos do Windows. Pode utilizar a Pesquisa para ver o erro do Registo de Eventos do Windows e obter o rastreio de pilha completo da exceção. Isto ajuda-o a encontrar a origem do problema.

![Pesquisa nos diagnósticos do Azure](./media/app-insights-cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mais telemetria
As secções abaixo mostram como obter telemetria adicional de diferentes aspetos da sua aplicação.

## <a name="track-requests-from-worker-roles"></a>Acompanhar pedidos de funções de trabalho
Nas funções de trabalho, o módulo de pedidos recolhe automaticamente dados sobre os pedidos HTTP. Veja o [exemplo MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) para obter exemplos de como pode substituir o comportamento de recolha predefinido. 

Pode acompanhar as chamadas para as funções de trabalho da mesma forma que os pedidos HTTP para capturar o desempenho das mesmas. No Application Insights, o tipo de telemetria Pedido mede uma unidade de trabalho com nome do lado do servidor que pode ser temporizada e ter êxito ou falhar independentemente. Embora os pedidos HTTP possam ser capturados automaticamente pelo SDK, pode inserir o seu próprio código para acompanhar os pedidos para as funções de trabalho.

Veja as duas funções de trabalho de exemplo instrumentadas para reportar pedidos, [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) e [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB).

## <a name="exceptions"></a>Exceções
Veja [Monitoring Exceptions in Application Insights](app-insights-asp-net-exceptions.md) (Monitorizar Exceções no Application Insights) para obter informações sobre como recolher exceções não processadas a partir de diferentes tipos de aplicações Web.

A função da Web de exemplo tem controladores MVC5 e Web API 2. As exceções não processadas dos dois são capturadas com os seguintes processadores:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) configurado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12), para controladores MVC5
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) configurado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25), para controladores Web API 2

Relativamente às funções de trabalho, existem duas formas de acompanhar as exceções:

* TrackException(ex)
* Se tiver adicionado o pacote NuGet do serviço de escuta de rastreios do Application Insights, pode utilizar **System.Diagnostics.Trace** para registar as exceções. [Exemplo de código.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## <a name="performance-counters"></a>Contadores de Desempenho
São recolhidos por predefinição os contadores seguintes:

    * \Process(??APP_WIN32_PROC??)\% Hora do Processador
    * \Memory\Available Bytes
    * \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
    * \Process(??APP_WIN32_PROC??)\Private Bytes
    * \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
    * \Processor(_Total)\% Processor Time

Para funções da Web, também são recolhidos estes contadores:

    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Pode editar ApplicationInsights.config [, como neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14), para especificar contadores de desempenho personalizados ou do Windows adicionais.

  ![Contadores de desempenho](./media/app-insights-cloudservices/OLfMo2f.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Telemetria correlacionada para Funções de Trabalho
Quando pode ver o que originou um pedido falhado ou com latência elevada, está perante uma experiência de diagnóstico avançada. Com as funções da Web, o SDK configura automaticamente a correlação entre a telemetria relacionada. Relativamente às funções de trabalho, pode utilizar um inicializador de telemetria personalizado para definir um atributo de contexto Operation.Id para toda a telemetria para obter esta correlação. Isto permite-lhe ver rapidamente se o problema de latência/falha foi provocado por uma dependência no seu código. 

Eis como:

* Defina o Id de correlação numa CallContext, conforme mostrado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Neste caso, estamos a utilizar o ID de Pedido como o Id de correlação
* Adicione uma implementação de TelemetryInitializer personalizada para definir o Operation.Id como o correlationId definido acima. Está disponível um exemplo em [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Adicione o inicializador de telemetria personalizado. Pode fazê-lo no ficheiro ApplicationInsights.config ou no código, conforme mostrado [aqui](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

Já está! A experiência do portal já está configurada para o ajudar a ver toda a telemetria associada rapidamente:

![Telemetria correlacionada](./media/app-insights-cloudservices/bHxuUhd.png)

## <a name="client-telemetry"></a>Telemetria de cliente
[Adicione o SDK JavaScript às suas páginas Web][client] para obter telemetria baseada no browser, como contagens de visualizações de páginas, tempos de carregamento de páginas, exceções de script, e para poder escrever telemetria personalizada nos scripts da sua página.

## <a name="availability-tests"></a>Testes de disponibilidade
[Configure testes Web][availability] para certificar-se de que a aplicação permanece em direto e reativa.

## <a name="display-everything-together"></a>Apresentar tudo em conjunto
Para obter uma visão geral do seu sistema, pode reunir os gráficos-chave de monitorização num único [dashboard](app-insights-dashboards.md). Por exemplo, pode afixar as contagens de pedidos e de falhas de cada função. 

Se o seu sistema utilizar outros serviços do Azure, como o Stream Analytics, inclua também os gráficos de monitorização dos mesmos. 

Se tiver uma aplicação móvel cliente, insira algum código para enviar eventos personalizados relativos a operações-chave do utilizador e crie uma aplicação [HockeyApp Bridge](app-insights-hockeyapp-bridge-app.md). Crie consultas no [Analytics](app-insights-analytics.md) para apresentar as contagens de eventos e afixá-las ao dashboard.

## <a name="example"></a>Exemplo
[O exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitoriza um serviço que tem uma função da Web e duas funções de trabalho.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção “método não encontrado” ao executar nos Serviços Cloud do Azure
Compilou para .NET 4.6? O 4.6 não é suportado automaticamente nas funções dos Serviços Cloud do Azure. [Instale o 4.6 em cada função](../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a aplicação.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes
* [Configure sending Azure Diagnostics to Application Insights](app-insights-azure-diagnostics.md) (Configurar o envio de Diagnósticos do Azure para o Application Insights)
* [Automate creation of Application Insights resources](app-insights-powershell.md) (Automatizar a criação de recursos do Application Insights)
* [Automatizar o Diagnóstico do Azure](app-insights-powershell-azure-diagnostics.md)
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 

---
title: "Diagnosticar falhas e exceções em aplicações web com o Azure Application Insights | Microsoft Docs"
description: "Capture as exceções de aplicações do ASP.NET, juntamente com a telemetria de pedido."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: cb87b166a32c47395f99c9cd59442a7ccd65b7ed
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnosticar exceções nas suas aplicações web com o Application Insights
Exceções na sua aplicação web em direto são reportadas pelo [Application Insights](app-insights-overview.md). Pode correlacionar pedidos falhados com exceções e outros eventos no cliente e no servidor, para que pode Diagnostique rapidamente as causas.

## <a name="set-up-exception-reporting"></a>Configurar relatórios de exceção
* Ter exceções reportados da sua aplicação de servidor:
  * Instalar [Application Insights SDK](app-insights-asp-net.md) no código da aplicação, ou
  * Servidores web do IIS: executar [Application Insights agente](app-insights-monitor-performance-live-website-now.md); ou
  * Aplicações web do Azure: adicionar o [extensão do Application Insights](app-insights-azure-web-apps.md)
  * As web apps Java: instalar o [agente Java](app-insights-java-agent.md)
* Instalar o [fragmento do JavaScript](app-insights-javascript.md) nas suas páginas web para detetar exceções de browser.
* Em algumas estruturas de aplicações ou com algumas definições, terá de efetuar alguns passos adicionais para detetar exceções mais:
  * [Formulários Web](#web-forms)
  * [MVC](#mvc)
  * [1.* de API Web](#web-api-1x)
  * [2.* de API Web](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnosticar exceções com o Visual Studio
Abra a solução de aplicação no Visual Studio para o ajudar a depurar.

Execute a aplicação no seu servidor ou no computador de desenvolvimento utilizando F5.

Abra a janela de pesquisa do Application Insights no Visual Studio e defini-lo para apresentar eventos da sua aplicação. Enquanto estiver a depuração, pode fazê-lo clicando no botão do Application Insights.

![Clique com o botão direito no projeto e escolha Application Insights, aberta.](./media/app-insights-asp-net-exceptions/34.png)

Tenha em atenção que pode filtrar o relatório para mostrar apenas as exceções.

*Não existem exceções Mostrar? Consulte [capturar exceções](#exceptions).*

Clique num relatório de exceção para mostrar o respetivo rastreio da pilha.
Clique uma referência de linha no rastreio da pilha, para abrir o ficheiro de código relevante.  

No código, tenha em atenção que CodeLens mostra os dados sobre as exceções:

![Notificação de CodeLens de exceções.](./media/app-insights-asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnosticar falhas no portal do Azure
Application Insights é fornecido com uma experiência APM organizada para o ajudar a diagnosticar falhas nas aplicações monitorizadas. Para iniciar, clique na opção de falhas no menu de recurso do Application Insights localizada na secção investigar. Deverá ver uma vista de total de ecrã que mostra as tendências de taxa de falhas para os seus pedidos, como muitos dos mesmos estão a falhar e quantos utilizadores são afetados. No lado direito, verá alguns das distribuições mais úteis específicas selecionado para efetuar a operação, incluindo os códigos de resposta de 3 principais, tipos de exceção da parte superior 3 e superior 3 tipos de depedency a falhar. 

![Falhas de triagem de vista (separador operações)](./media/app-insights-asp-net-exceptions/FailuresTriageView.png)

Um único clique, em seguida, pode rever amostras representativos para cada um destes subconjuntos de operações. Em particular, para diagnosticar exceções, pode clicar na contagem de uma exceção específica para ser apresentado um painel de detalhes de exceções, tal como esta:

![Painel de detalhes de exceção](./media/app-insights-asp-net-exceptions/ExceptionDetailsBlade.png)

**Em alternativa,** em vez de observar as exceções de uma operação específica falhar, pode iniciar a partir da vista geral de exceções, por mudar para o separador de exceções:

![Falhas de triagem de vista (separador exceções)](./media/app-insights-asp-net-exceptions/FailuresTriageView_Exceptions.png)

Aqui pode ver todas as exceções recolhidas para a sua aplicação monitorizada.

*Não existem exceções Mostrar? Consulte [capturar exceções](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Dados de registo e rastreio personalizado
Para obter dados de diagnóstico específico para a sua aplicação, pode inserir código para enviar os seus próprios dados de telemetria. Isto apresentado na pesquisa de diagnóstico juntamente com o pedido, a vista de página e outros dados recolhidos automaticamente.

Tem várias opções:

* [Trackevent ()](app-insights-api-custom-events-metrics.md#trackevent) é normalmente utilizado para monitorizar os padrões de utilização, mas também envia são apresentados os dados em eventos personalizados na pesquisa de diagnóstico. Os eventos são denominados e podem conter propriedades de cadeia e métricas numérico no qual pode [filtrar as pesquisas de diagnóstico](app-insights-diagnostic-search.md).
* [Tracktrace ()](app-insights-api-custom-events-metrics.md#tracktrace) permite-lhe enviar mais dados, tais como informações de POST.
* [Trackexception ()](#exceptions) envia os rastreios de pilha. [Mais informações sobre as exceções](#exceptions).
* Se já utilizar uma arquitetura de registo como o Log4Net ou o NLog, pode [capturar esses registos](app-insights-asp-net-trace-logs.md) e vê-los na pesquisa de diagnóstico juntamente com dados de pedido e de exceção.

Para ver estes eventos, abra [pesquisa](app-insights-diagnostic-search.md), abra o filtro e, em seguida, escolha exceção, rastreio ou eventos personalizados.

![Explorar](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Se a sua aplicação gerar uma grande quantidade de telemetria, o módulo de amostragem adaptável irá reduzir automaticamente o volume que é enviado para o portal, enviando apenas uma fração representativa dos eventos. Os eventos que fazem parte da mesma operação serão selecionados ou desmarcados como um grupo, para que possa navegar entre os eventos relacionados. [Saiba mais sobre amostragem.](app-insights-sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Como ver dados do pedido POST
Detalhes do pedido não incluem os dados enviados para a sua aplicação numa chamada de POST. Para que estes dados reportados:

* [Instalar o SDK](app-insights-asp-net.md) no seu projeto de aplicação.
* Inserir o código na sua aplicação para chamar [Microsoft.ApplicationInsights.TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace). Envie os dados POST no parâmetro mensagem. Não há um limite para o tamanho permitido, pelo que deve tentar enviar dados essenciais.
* Quando estiver a investigar um pedido falhado, localize os rastreios associados.  

![Explorar](./media/app-insights-asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a>Capturar exceções e dados de diagnóstico relacionados
Em primeiro lugar, não verá no portal de todas as exceções que causam falhas na sua aplicação. Irá ver quaisquer exceções de browser (se estiver a utilizar o [JavaScript SDK](app-insights-javascript.md) nas suas páginas web). Mas a maioria das exceções de servidor são detectadas pelo IIS e tem de escrever um bit de código para vê-los.

Pode:

* **Inicie sessão exceções explicitamente** através da inserção de código no processadores de exceções para comunicar as exceções.
* **Capturar exceções automaticamente** ao configurar a sua arquitetura do ASP.NET. As adições necessárias são diferentes para diferentes tipos de estrutura.

## <a name="reporting-exceptions-explicitly"></a>Relatórios exceções explicitamente
É a forma mais simples para inserir uma chamada para trackexception () no processador de excepções.

JavaScript

    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Os parâmetros e valores de propriedades são opcionais, mas são úteis para [filtragem e adicionar](app-insights-diagnostic-search.md) informações adicionais. Por exemplo, se tiver uma aplicação que pode executar vários jogos, foi possível localizar todos os relatórios de exceção relacionados com um jogo específico. Pode adicionar tantos itens como pretender para cada dicionário.

## <a name="browser-exceptions"></a>Exceções de browser
A maioria das exceções de browser são reportadas.

Se a sua página web inclui os ficheiros de script de redes de entrega de conteúdos ou de outros domínios, certifique-se da tag de script tem o atributo ```crossorigin="anonymous"```, e que o servidor envia [cabeçalhos CORS](http://enable-cors.org/). Isto permitirá obter um rastreio da pilha e os detalhes de exceções de JavaScript não processadas destes recursos.

## <a name="web-forms"></a>Formulários Web
Para formulários web, o módulo de HTTP poderão ser recolher as exceções quando não existe nenhum redirecionamentos configurados com CustomErrors.

Mas, se tiver de redirecionamentos de Active Directory, adicione as seguintes linhas para a função de Application_Error no asax. (Adicionar um ficheiro global. asax se ainda não tiver um.)

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## <a name="mvc"></a>MVC
Se o [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) configuração é `Off`, em seguida, as exceções estarão disponíveis para o [módulo de HTTP](https://msdn.microsoft.com/library/ms178468.aspx) para recolher. No entanto, se for `RemoteOnly` (predefinição), ou `On`, em seguida, a exceção será limpa e não está disponível para o Application Insights recolher automaticamente. Pode corrigir através da substituição de [System.Web.Mvc.HandleErrorAttribute classe](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)e aplicar a classe substituída, conforme mostrado para as diferentes versões MVC abaixo ([origem do github](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }

#### <a name="mvc-2"></a>MVC 2
Substitua o atributo HandleError o novo atributo nos controladores.

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[Exemplo](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Registar `AiHandleErrorAttribute` como um filtro global em Global.asax.cs:

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[Exemplo](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Register AiHandleErrorAttribute como um filtro global em FilterConfig.cs:

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[Exemplo](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api-1x"></a>Web API 1. x
Substitua System.Web.Http.Filters.ExceptionFilterAttribute:

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

Foi possível adicionar este atributo substituído aos controladores específicos, ou adicione-o para a configuração do filtro global na classe WebApiConfig:

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[Exemplo](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

Existem um número de cenários que não é possível processar os filtros de excepção. Por exemplo:

* Exceções acionadas a partir de construtores de controlador.
* Exceções acionadas a partir de processadores de mensagem.
* Exceções emitidas durante o encaminhamento.
* Exceções emitidas durante a serialização de conteúdo de resposta.

## <a name="web-api-2x"></a>Web API 2. x
Adicione uma implementação do IExceptionLogger:

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

Adicione este para os serviços no WebApiConfig:

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
  }

[Exemplo](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Como alternativas, foi:

1. Substitua o ExceptionHandler apenas uma implementação personalizada de IExceptionHandler. Isto só é chamado quando o framework for ainda é possível escolher qual mensagem de resposta a enviar (não quando a ligação foi abortada para a instância)
2. Filtros de excepção (conforme descrito na secção em controladores de 1. x de Web API acima) - não chamados em todos os casos.

## <a name="wcf"></a>WCF
Adicione uma classe que expande o atributo e implementa o IErrorHandler e o IServiceBehavior.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Adicione o atributo para as implementações de serviço:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...

[Exemplo](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Contadores de desempenho de exceção
Se tiver [instalado o agente do Application Insights](app-insights-monitor-performance-live-website-now.md) no seu servidor, pode obter um gráfico da taxa de exceções, medido pela .NET. Isto inclui as exceções de .NET processadas e não processadas.

Abrir um painel do Explorador de métrica, adicione um novo gráfico e selecione **taxa de exceção**, listado em contadores de desempenho.

O .NET framework calcula a taxa do número de exceções de contagem de um intervalo e dividir pelo comprimento do intervalo.

Isto é diferente da contagem de 'Exceções' calculada pelo portal do Application Insights contando TrackException relatórios. Os intervalos de amostra são diferentes e o SDK não enviar relatórios de TrackException para todos os processadas e não processadas exceções.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar REST, SQL e outras chamadas para dependências](app-insights-asp-net-dependencies.md)
* [Monitorizar os tempos de carregamento de página, exceções de browser e chamadas AJAX](app-insights-javascript.md)
* [Contadores de monitor de desempenho](app-insights-performance-counters.md)

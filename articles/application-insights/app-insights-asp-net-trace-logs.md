---
title: Explorar os registos de rastreio do .NET no Application Insights
description: Pesquisar registos gerados com Trace, NLog ou Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 6da0bf009fa71885d7d8e3bd5376c5a7c9d4a344
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Explorar os registos de rastreio do .NET no Application Insights
Se utilizar o NLog, o log4Net ou Trace para o rastreio de diagnóstico na sua aplicação ASP.NET, pode ter os registos enviados para [Azure Application Insights][start], onde pode explorar e procure-os. Os registos serão Unidos com outra telemetria provenientes da sua aplicação, para que possa identificar os rastreios associados a cada pedido de utilizador de manutenção e correlacioná-los com outros eventos e os relatórios de exceções.

> [!NOTE]
> Precisa que o módulo de registo de captura? É um adaptador útil para 3rd terceiros registadores, mas se já não estiverem a utilizar o NLog, o log4Net ou Trace, considere chamar apenas [tracktrace () do Application Insights](app-insights-api-custom-events-metrics.md#tracktrace) diretamente.
>
>

## <a name="install-logging-on-your-app"></a>Instalar o registo na sua aplicação
Instale a arquitetura de registo que escolheu no seu projeto. Isto deve resultar numa entrada no App. config ou Web. config.

Se estiver a utilizar Trace, terá de adicionar uma entrada para o Web. config:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Configurar o Application Insights para recolher registos
**[Adicionar o Application Insights ao seu projeto](app-insights-asp-net.md)**  se ainda não o fez que ainda. Verá uma opção para incluir o recoletor de registos.

Ou **configurar o Application Insights** clicando com o seu projeto no Explorador de soluções. Selecione a opção para **configurar a recolha de rastreio**.

*Nenhum registo ou menu recoletor opção do Application Insights?* Tente [resolução de problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Utilize este método se o tipo de projeto não é suportado pelo programa de instalação do Application Insights (por exemplo um ambiente de trabalho projeto Windows).

1. Se planeia utilizar o log4Net ou o NLog, instalá-lo no seu projeto.
2. No Explorador de soluções, clique no seu projeto e escolha **gerir pacotes NuGet**.
3. Procurar “Application Insights”
4. Selecione o pacote adequado - um dos:

   * Microsoft.ApplicationInsights.TraceListener (para capturar Trace chamadas)
   * Microsoft.ApplicationInsights.EventSourceListener (para recolher eventos de EventSource)
   * Microsoft.ApplicationInsights.EtwListener (para capturar eventos ETW)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

O pacote NuGet instala as assemblagens necessárias e também modifica o Web. config ou o App. config.

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de registo de diagnóstico
Se utilizar Trace, uma chamada típica seria:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se preferir log4net ou NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>A utilização de eventos de EventSource
Pode configurar [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos enviados para o Application Insights como rastreios. Em primeiro lugar, instalar o `Microsoft.ApplicationInsights.EventSourceListener` pacote NuGet. Em seguida, edite `TelemetryModules` secção o [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md) ficheiro.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada origem, pode definir os seguintes parâmetros:
 * `Name`Especifica o nome de EventSource para recolher.
 * `Level`Especifica o nível de registo a recolher. Pode ser um dos `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords`(Opcional) Especifica o valor de número inteiro de combinações de palavras-chave a utilizar.

## <a name="using-diagnosticsource-events"></a>A utilização de eventos de DiagnosticSource
Pode configurar [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) eventos enviados para o Application Insights como rastreios. Em primeiro lugar, instalar o [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pacote NuGet. Em seguida, edite o `TelemetryModules` secção o [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md) ficheiro.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnsoticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que pretende rastrear, adicione uma entrada com o `Name` atributo definido como o nome do seu DiagnosticSource.

## <a name="using-etw-events"></a>A utilização de eventos do ETW
Pode configurar eventos ETW sejam enviados para o Application Insights como rastreios. Em primeiro lugar, instalar o `Microsoft.ApplicationInsights.EtwCollector` pacote NuGet. Em seguida, edite `TelemetryModules` secção o [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md) ficheiro.

> [!NOTE] 
> Só podem ser recolhidos Eventos ETW se o processo que aloja o SDK está a ser executada uma identidade que é um membro de "Utilizadores de registo de desempenho" ou os administradores.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada origem, pode definir os seguintes parâmetros:
 * `ProviderName`é o nome do fornecedor ETW para recolher.
 * `ProviderGuid`Especifica o GUID do fornecedor ETW para recolher, pode ser utilizado em vez de `ProviderName`.
 * `Level`Define o nível de registo a recolher. Pode ser um dos `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords`(Opcional) define o valor de número inteiro de combinações de palavra-chave a utilizar.

## <a name="using-the-trace-api-directly"></a>Utilizar diretamente o rastreio de API
Pode chamar o API de rastreio do Application Insights diretamente. Os adaptadores de registo utilizam esta API.

Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Uma vantagem TrackTrace é que pode colocar dados relativamente longos na mensagem. Por exemplo, foi codificar POST data do não existe.

Além disso, pode adicionar um nível de gravidade para a mensagem. E, como outra telemetria, pode adicionar valores de propriedade que pode utilizar para ajudar o filtro ou a pesquisa para diferentes conjuntos de rastreios. Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isto seria permitem-lhe, na [pesquisa][diagnostic], facilmente filtrar todas as mensagens de um nível de gravidade específico relacionado com a base de dados específica.

## <a name="explore-your-logs"></a>Explore os seus registos
Executar a aplicação no modo de depuração ou implementá-la em direto.

No painel de descrição geral da sua aplicação no [portal do Application Insights][portal], escolha [pesquisa][diagnostic].

![No Application Insights, escolha pesquisa](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Pesquisa](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Pode, por exemplo:

* Filtrar rastreios de registo ou itens com propriedades específicas
* Inspecione um item específico em detalhe.
* Encontrar outra telemetria relacionadas com o mesmo pedido de utilizador (ou seja, com o mesmo OperationId)
* Guardar a configuração desta página como um favorito

> [!NOTE]
> **Amostragem.** Se a aplicação enviar uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para o ASP.NET versão 2.0.0-beta3 ou posterior, a funcionalidade de amostragem adaptável pode operar e enviar apenas uma percentagem da sua telemetria. [Saiba mais sobre a amostragem.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Passos seguintes
[Diagnosticar falhas e exceções no ASP.NET][exceptions]

[Saiba mais sobre pesquisa][diagnostic].

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como posso fazer isto para Java?
Utilize o [adaptadores de registo de Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há nenhuma opção do Application Insights no menu de contexto do projeto
* Verificar as ferramentas do Application Insights está instalado neste computador de desenvolvimento. No Visual Studio menu Ferramentas, extensões e atualizações, procure as ferramentas do Application Insights. Se não se encontra no separador de instalado, abra o separador Online e instalá-lo.
* Isto pode ser um tipo de projeto não suportado pelas ferramentas do Application Insights. Utilize [instalação manual](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Nenhuma opção do adaptador de registo na ferramenta de configuração
* Tem de instalar primeiro a arquitetura de registo.
* Se estiver a utilizar Trace, certifique-se de [configurado no `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Tem a versão mais recente do Application Insights? No Visual Studio **ferramentas** menu, escolha **extensões e atualizações**e abra o **atualizações** separador. Se as ferramentas de análise de programador não existe, clique em Atualizar.

### <a name="emptykey"></a>Posso obter um erro "chave de instrumentação não pode estar vazio"
Parece que instalou o pacote do registo adaptador Nuget sem instalar o Application Insights.

No Explorador de soluções, faça duplo clique `ApplicationInsights.config` e escolha **atualização Application Insights**. Obterá uma caixa de diálogo invites que inicie sessão no Azure e cria um recurso do Application Insights, ou reutilize um existente. Que deverá corrigir.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Posso ver os rastreios na pesquisa de diagnóstico, mas não os outros eventos
Por vezes, pode demorar algum tempo para todos os eventos e pedidos para obter através do pipeline.

### <a name="limits"></a>Quantidade de dados é mantida?
Vários fatores afetam a quantidade de dados retidos. Consulte o [limites](app-insights-api-custom-events-metrics.md#limits) secção da página de métricas de eventos de cliente para obter mais informações. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Não estou a ver algumas das entradas de registo que posso esperar
Se a aplicação enviar uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para o ASP.NET versão 2.0.0-beta3 ou posterior, a funcionalidade de amostragem adaptável pode operar e enviar apenas uma percentagem da sua telemetria. [Saiba mais sobre a amostragem.](app-insights-sampling.md)

## <a name="add"></a>Passos seguintes
* [Configurar a disponibilidade e testes de capacidade de resposta][availability]
* [Resolução de problemas][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

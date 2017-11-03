---
title: "Aplicação Azure Insights FAQ | Microsoft Docs"
description: Perguntas mais frequentes sobre o Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: 7dcfea240a8dbb416226e76f8941cfe8147e2b39
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Perguntas mais frequentes

## <a name="configuration-problems"></a>Problemas de configuração
*Estou a ter a definição de problema ao os meus:*

* [Aplicação .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorizar uma aplicação já em execução](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Diagnóstico do Azure](app-insights-azure-diagnostics.md)
* [Aplicação Web Java](app-insights-java-troubleshoot.md)

*Posso obter não existem dados a partir do meu servidor*

* [Exceções de firewall de conjunto](app-insights-ip-addresses.md)
* [Configurar um servidor ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurar um servidor de Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Posso utilizar o Application Insights com …?

* [Aplicações Web num servidor IIS - no local ou numa VM](app-insights-asp-net.md)
* [Web apps Java](app-insights-java-get-started.md)
* [Aplicações Node.js](app-insights-nodejs.md)
* [Web apps do Azure](app-insights-azure-web-apps.md)
* [Serviços em nuvem no Azure](app-insights-cloudservices.md)
* [Servidores de aplicações em execução no Docker](app-insights-docker.md)
* [Aplicações web de página única](app-insights-javascript.md)
* [SharePoint](app-insights-sharepoint.md)
* [Aplicação de ambiente de trabalho do Windows](app-insights-windows-desktop.md)
* [Outras plataformas](app-insights-platforms.md)

## <a name="is-it-free"></a>É gratuito?

Sim, para utilização experimental. O plano de preços básico, a aplicação pode enviar um determinado allowance de dados de cada mês gratuitas. O allowance gratuita é suficiente grande para desenvolvimento abrange e publicar uma aplicação de um pequeno número de utilizadores. Pode definir um limite para impedir que mais do que uma quantidade especificada de dados a ser processado.

Maiores volumes de telemetria são cobrados pelos Gb. Fornecemos algumas sugestões sobre como [limitar os encargos](app-insights-pricing.md).

O plano de Enterprise implica um custo de cada dia em que cada nó do servidor web envia a telemetria. É adequado se quiser utilizar a exportação contínua de mensagens em fila em grande escala.

[Leia o plano de preços](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Quanto está-custos?

* Abra o **funcionalidades + preços** página num recurso do Application Insights. Não há um gráfico de utilização recente. Pode definir um limite de volume de dados, se pretender.
* Abra o [painel de faturação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) para ver o seu faturas em todos os recursos.

## <a name="q14"></a>O Application Insights modificar no meu projeto?
Os detalhes dependem do tipo de projeto. Para uma aplicação web:

* Adiciona estes ficheiros ao seu projeto:

  * Applicationinsights.
  * ai.js
* Instala estes pacotes de NuGet:

  * *Application Insights API* -as principais API
  * *API do Application Insights para aplicações Web* - utilizada para enviar telemetria a partir do servidor
  * *API do Application Insights para aplicações JavaScript* - utilizada para enviar telemetria a partir do cliente

    Os pacotes incluem estas assemblagens:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insere itens para:

  * Web.config
  * Packages
* (Novos projetos só - se que [adicionar o Application Insights para um projeto existente][start], terá de fazê-lo manualmente.) Insere fragmentos no código de cliente e servidor ao inicializá-los com o ID de recurso do Application Insights. Por exemplo, numa aplicação MVC, é inserido código à página principal Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como atualizar a partir de versões anteriores do SDK?
Consulte o [notas de versão](app-insights-release-notes.md) para o SDK adequado ao seu tipo de aplicação.

## <a name="update"></a>Como posso alterar a qual os meus projeto envia dados para o recurso do Azure?
No Explorador de soluções, faça duplo clique `ApplicationInsights.config` e escolha **atualização Application Insights**. Pode enviar os dados a um recurso novo ou existente no Azure. O Assistente de atualização altera a chave de instrumentação no Applicationinsights, que determina onde o servidor SDK envia os dados. A menos que anular a seleção "Atualizar tudo", a chave de onde aparece nas suas páginas web também serão alteradas.

## <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Uma aplicação de ambiente de trabalho que pode utilizar no servidor web do IIS para o ajudar a configurar o Application Insights nas web apps. Não recolher telemetria: pode parar quando não estiver a configurar uma aplicação. 

[Saiba mais](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>A telemetria que é recolhida pelo Application Insights?

A partir de aplicações do servidor web:

* Pedidos HTTP
* [Dependências](app-insights-asp-net-dependencies.md). Chamadas para: bases de dados do SQL Server; Chamadas HTTP para serviços externos; Azure Cosmos DB, tabela, armazenamento de BLOBs e filas. 
* [Exceções](app-insights-asp-net-exceptions.md) e rastreios de pilha.
* [Contadores de desempenho](app-insights-performance-counters.md) - se de que utiliza [Monitor de estado](app-insights-monitor-performance-live-website-now.md), [monitorização do Azure](app-insights-azure-web-apps.md) ou [escritor do Application Insights collectd](app-insights-java-collectd.md).
* [Métricas e eventos personalizados](app-insights-api-custom-events-metrics.md) que code.
* [Registos de rastreio](app-insights-asp-net-trace-logs.md) se configurar o recoletor adequado.

De [páginas web de cliente](app-insights-javascript.md):

* [Contagens de vista de página](app-insights-web-track-usage.md)
* [Chamadas AJAX](app-insights-asp-net-dependencies.md) pedidos efetuados a partir de um script em execução.
* Dados de carregamento da vista de página
* Contagens de sessões e utilizador
* [Os IDs de utilizador autenticado](app-insights-api-custom-events-metrics.md#authenticated-users)

A partir de outras origens, se configurar:

* [Diagnóstico do Azure](app-insights-azure-diagnostics.md)
* [Contentores de docker](app-insights-docker.md)
* [Importar as tabelas para análise](app-insights-analytics-import.md)
* [(Análise de registos) do OMS](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Pode filtrar ou modificar algumas telemetria?

Sim, no servidor pode escrever:

* Processador de telemetria filtrar ou adicionar propriedades de itens de telemetria selecionada antes de serem enviados a partir da sua aplicação.
* Inicializador de telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais para [ASP.NET](app-insights-api-filtering-sampling.md) ou [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Como são cidade, país e outros dados de localização georreplicação calculados?

Iremos procurar o endereço IP (IPv4 ou IPv6) do cliente web utilizando [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria de browser: recolhemos o endereço do remetente IP.
* Telemetria do servidor: módulo o Application Insights recolhe o endereço IP do cliente. Não é recolhido se `X-Forwarded-For` está definido.

Pode configurar o `ClientIpHeaderTelemetryInitializer` para assumir o endereço IP de um cabeçalho diferente. Em alguns sistemas, por exemplo, esta ser movida por um proxy, carregar balanceador ou CDN `X-Originating-IP`. [Saiba mais](http://apmtips.com/blog/2016/07/05/client-ip-address/).

Pode [utilizar o Power BI](app-insights-export-power-bi.md) para apresentar a telemetria de pedido num mapa.


## <a name="data"></a>Quanto os dados são mantidos no portal? É seguro?
Observe [retenção de dados e privacidade][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>Poderá pessoal informação identificativa (PII) enviada na telemetria?

Isto é possível se o seu código envia esses dados. Também pode acontecer se as variáveis de rastreios de pilha incluem PII. A equipa de desenvolvimento deve realizar avaliações de risco para se certificar de que é corretamente processada PII. [Saiba mais sobre retenção de dados e privacidade](app-insights-data-retention-privacy.md).

O octeto última do endereço de web de cliente está sempre definido como 0 após ingestão pelo portal.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>A minha iKey é visível na minha origem da página web. 

* Este é prática comum em soluções de monitorização.
* Não pode ser utilizado para roubar os seus dados.
* Pode ser utilizado para prejudicar os alertas de dados ou acionador.
* Não tem teve a que qualquer cliente apresentou esses problemas.

Foi:

* Utilize dois iKeys separados (separe os recursos do Application Insights), para os dados de cliente e servidor. Ou
* Escrever um proxy que é executado no seu servidor e têm o cliente web enviar através do proxy de que os dados.

## <a name="post"></a>Como posso ver dados de publicação na pesquisa de diagnóstico?
Iremos não iniciar sessão automaticamente dados reg, mas pode utilizar uma chamada de TrackTrace: colocar os dados no parâmetro mensagem. Isto tem um tempo limite de tamanho que ultrapasse os limites nas propriedades de cadeia, apesar de não é possível filtrar.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Deve utilizar único ou vários recursos do Application Insights?

Utilize um único recurso de todos os componentes ou funções num sistema de negócio único. Utilize recursos separados para o desenvolvimento, teste e versões de lançamento e para aplicações independentes.

* [Consulte a discussão aqui](app-insights-separate-resources.md)
* [Exemplo - serviço em nuvem com funções web e de trabalho](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Como posso alterar dinamicamente a chave de instrumentação?

* [Debate aqui](app-insights-separate-resources.md)
* [Exemplo - serviço em nuvem com funções web e de trabalho](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Quais são os utilizadores e a sessão de conta?

* O SDK de JavaScript define um cookie de utilizador no cliente web, para identificar utilizadores devolvidos e um cookie de sessão para agrupar atividades.
* Se não houver nenhum script do lado do cliente, pode [definir cookies no servidor](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se um utilizador real utiliza o seu site no browsers diferentes ou utilizando a navegar no-privada/incognito ou vários computadores, em seguida, estes serão contados tendo mais do que uma vez.
* Para identificar um utilizador com sessão iniciada em máquinas e browsers, adicione uma chamada para [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a>Posso ativou tudo no Application Insights?
| Deverá ver | Como a obter | Motivo pelo qual pretende |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes Web](app-insights-monitor-web-app-availability.md) |Conhecer a que sua aplicação web está ativo |
| Desempenho de aplicações de servidor: tempos de resposta,... |[Adicionar o Application Insights ao seu projeto](app-insights-asp-net.md) ou [instalar Monitor de estado de AI no servidor](app-insights-monitor-performance-live-website-now.md) (ou escrever o seu próprio código para [controlar dependências](app-insights-api-custom-events-metrics.md#trackdependency)) |Detetar problemas de desempenho |
| Telemetria de dependência |[Instalar o Monitor de estado de AI no servidor](app-insights-monitor-performance-live-website-now.md) |Diagnosticar problemas com outros componentes externos ou de bases de dados |
| Obter rastreios de pilha de exceções |[Inserir chamadas TrackException no seu código](app-insights-asp-net-exceptions.md) (mas algumas são reportadas automaticamente) |Detetar e diagnosticar exceções |
| Rastreios de registo de pesquisa |[Adicionar uma placa de registo](app-insights-asp-net-trace-logs.md) |Diagnosticar exceções, problemas de desempenho |
| Noções básicas de utilização do cliente: vistas de página, sessões,... |[Inicializador de JavaScript em páginas web](app-insights-javascript.md) |Análise de utilização |
| Métricas personalizadas de cliente |[Controlo de chamadas em páginas web](app-insights-api-custom-events-metrics.md) |Melhorar a experiência de utilizador |
| Métricas personalizadas do servidor |[Chamadas de registo no servidor](app-insights-api-custom-events-metrics.md) |Business intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Por que razão são unequal contagens nos gráficos de pesquisa e métricas?

[Amostragem](app-insights-sampling.md) reduz o número de itens de telemetria (pedidos, eventos personalizados e assim sucessivamente) que são enviados, na verdade, da sua aplicação no portal. Na pesquisa, verá o número de itens realmente recebido. Gráficos de métrica mostra uma contagem de eventos, consulte o número de eventos originais que ocorreram. 

Cada item que é transmitida ativada uma `itemCount` representa de propriedade que mostra o número de eventos original que o item. Para observar amostragem numa operação, pode executar esta consulta na análise:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatização

### <a name="configuring-application-insights"></a>Configurar o Application Insights

Pode [escrever scripts do PowerShell](app-insights-powershell.md) utilizando o Monitor de recursos do Azure para:

* Criar e atualizar recursos do Application Insights.
* Defina o plano de preços.
* Obter a chave de instrumentação.
* Adicione um alerta de métrico.
* Adicione um teste de disponibilidade.

Não é possível configurar um relatório de métrica Explorador ou configurar a exportação contínua.

### <a name="querying-the-telemetry"></a>Consultar a telemetria

Utilize o [REST API](https://dev.applicationinsights.io/) executar [análise](app-insights-analytics.md) consultas.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Como pode definir um alerta num evento?

Alertas do Azure são apenas nas métricas. Crie uma métrica personalizada que atravesse um limiar de valor sempre que o evento ocorre. Em seguida, defina um alerta a métrica. Tenha em atenção que: receberá uma notificação sempre que a métrica atravesse o limiar em qualquer direção; não irá receber uma notificação até o cruzamento primeiro, independentemente se o valor inicial elevado ou baixa; é sempre uma latência de alguns minutos.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Existem taxas aplicáveis às transferências de dados entre uma aplicação web do Azure e o Application Insights?

* Se a sua aplicação web do Azure está alojada num centro de dados em que exista um ponto final de recolha de Application Insights, há sem qualquer encargo. 
* Se não houver nenhum ponto final de recolha no seu centro de dados do anfitrião, em seguida, a telemetria da sua aplicação será cobrado [Azure encargos de saída](https://azure.microsoft.com/pricing/details/bandwidth/).

Isto não depende do local onde o recurso do Application Insights está alojado. Depende apenas a distribuição dos nossos pontos finais.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Pode enviar telemetria ao portal do Application Insights?

Recomendamos que utilize os nossos SDKs e utiliza a API do SDK (app-insights-api-custom-events-metrics.md). Existem variantes do SDK para várias [plataformas](app-insights-platforms.md). Estes SDKs lidar com colocação em memória intermédia, compressão, limitação, tentativas e assim sucessivamente. No entanto, o [esquema de ingestão](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e [protocolo de ponto final](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) são públicas.

## <a name="can-i-monitor-an-intranet-web-server"></a>Pode monitorizar um servidor de web da intranet?

Seguem-se dois métodos:

### <a name="firewall-door"></a>Porta de firewall

Permitir que o servidor web enviar telemetria ao nosso https://dc.services.visualstudio.com:443 pontos finais e https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Proxy

Encaminhar o tráfego do seu servidor para um gateway na sua intranet, definindo isto no Applicationinsights:

```XML
<TelemetryChannel>
    <EndpointAddress>your gateway endpoint</EndpointAddress>
</TelemetryChannel>
```

O gateway deve encaminhar o tráfego para v2/https://dc.services.visualstudio.com:443/controlar

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Pode executar testes web de disponibilidade num servidor da intranet?

A nossa [testes web](app-insights-monitor-web-app-availability.md) executar nos pontos de presença que são distribuídos por volta de todo o mundo. Existem duas soluções:

* Porta de firewall - permitir que os pedidos para o servidor [a lista de comprimento e das partições de agentes de teste web](app-insights-ip-addresses.md).
* Escreva o seu próprio código para enviar pedidos periódicos para o servidor dentro da sua intranet. É possível executar testes web do Visual Studio para esta finalidade. O recurso de teste foi possível enviar os resultados para o Application Insights utilizando a API de TrackAvailability().

## <a name="more-answers"></a>Mais respostas
* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

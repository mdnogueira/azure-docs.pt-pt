---
title: "Monitorizar serviços Node.js com o Azure Application Insights | Microsoft Docs"
description: "Monitorize o desempenho e diagnostique problemas em serviços Node.js com o Application Insights."
services: application-insights
documentationcenter: nodejs
author: joshgav
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 76a8025cd2a67533beb321c88e924517c1977dfc
ms.contentlocale: pt-pt
ms.lasthandoff: 05/02/2017


---

# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorizar os seus serviços e aplicações Node.js com o Application Insights

O [Azure Application Insights](app-insights-overview.md) monitoriza os seus serviços e componentes de back-end depois de os implementar para ajudar a [detetar e diagnosticar rapidamente problemas de desempenho, entre outros](app-insights-detect-triage-diagnose.md). Utilize-o em serviços Node.js alojados em qualquer sítio, no seu datacenter, em VMs do Azure, em Aplicações Web e, inclusivamente, noutras clouds públicas.

Para receber, armazenar e explorar os seus dados de monitorização, siga as instruções abaixo para incluir um agente no seu código e configurar um recurso do Application Insights correspondente no Azure. O agente envia dados para esse recurso para análise e exploração adicionais.

O agente de Node.js pode monitorizar automaticamente pedidos HTTP de entrada e saída, várias métricas do sistema e exceções. A partir da versão v0.20, também pode monitorizar alguns pacotes de terceiros comuns, como `mongodb`, `mysql` e `redis`. Todos os eventos relacionados com pedidos HTTP de entrada são correlacionados para resolução de problemas mais rápida.

Pode monitorizar mais aspetos da sua aplicação e do seu sistema ao instrumentá-los manualmente com a API de agente descrita adiante.

![Gráficos de exemplo da monitorização do desempenho](./media/app-insights-nodejs/10-perf.png)

## <a name="getting-started"></a>Introdução

Vamos avançar para a configuração da monitorização de um serviço ou aplicação.

### <a name="resource"></a>Configurar um recurso do App Insights

**Antes de começar**, certifique-se de que tem uma subscrição do Azure ou [obtenha uma nova gratuitamente][azure-free-offer]. Se a sua organização já tiver uma subscrição do Azure, um administrador pode seguir [estas instruções][add-aad-user] para o adicionar à mesma.

[azure-free-offer]: https://azure.microsoft.com/en-us/free/
[add-aad-user]: https://docs.microsoft.com/en-us/azure/active-directory/active-directory-users-create-azure-portal

Agora, inicie sessão no [portal do Azure][portal] e crie um recurso do Application Insights, conforme ilustrado na imagem seguinte: clique em "Novo" > "Ferramentas de programador" > "Application Insights". O recurso inclui um ponto final para receber dados de telemetria, armazenamento para esses dados, relatórios guardados e configuração de dashboards, regras e alertas, entre outras coisas.

![Criar um recurso do App Insights](./media/app-insights-nodejs/03-new_appinsights_resource.png)

Na página de criação do recurso, escolha “Aplicação Node.js”, na lista pendente de tipo de aplicação. O tipo de aplicação determina o conjunto predefinido de dashboards e relatórios criados por si. Mas não se preocupe. Todos os recursos do App Insights podem, efetivamente, recolher dados de qualquer linguagem e plataforma.

![Formulário de recurso novo do App Insights](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="agent"></a> Configurar o agente de Node.js

Agora, é altura de incluir o agente na aplicação, para que esta possa recolher dados.
Comece por copiar a Chave de Instrumentação do recurso (doravante denominada como `ikey`) no portal, conforme mostrado abaixo. O sistema do App Insights utiliza esta chave para mapear dados para o seu recurso do Azure, pelo que tem de a especificar numa variável de ambiente ou no código, para que o agente a possa utilizar.  

![Copiar a chave de instrumentação](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

Em seguida, adicione a biblioteca do agente de Node.js às dependências da aplicação, através de package.json. Na pasta raiz da aplicação, execute:

```bash
npm install applicationinsights --save
```

Agora, tem de carregar explicitamente a biblioteca para o seu código. Uma vez que o agente injeta instrumentação em muitas outras bibliotecas, deve carregá-lo o mais cedo possível, até antes de outras declarações `require`. Para começar, na parte superior do seu primeiro ficheiro .js, adicione:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.start();
```

O método `setup` configura a chave de instrumentação (e, por conseguinte, o recurso do Azure) que vai ser utilizada por predefinição para todos os itens monitorizados. Chame `start` quando a configuração estiver concluída para começar a recolher e enviar dados de telemetria.

Também pode indicar uma ikey através da variável de ambiente APPINSIGHTS\_INSTRUMENTATIONKEY, em vez de a transmitir manualmente para `setup()` ou `getClient()`. Esta prática permite-lhe manter as ikeys fora do código de origem consolidado e especificar outras ikeys para diferentes ambientes.

Estão documentadas abaixo opções de configuração adicionais.

Pode experimentar o agente sem enviar telemetria. Para tal, defina a chave de instrumentação para uma cadeia não vazia.

### <a name="monitor"></a> Monitorizar a sua aplicação

O agente recolhe automaticamente telemetria sobre o runtime de Node.js e de alguns módulos de terceiros comuns. Utilize a sua aplicação agora para gerar alguns destes dados.

Depois, no [portal do Azure][portal], navegue para o recurso do Application Insights que criou anteriormente e procure os seus primeiros pontos de dados na linha cronológica Descrição geral, conforme a imagem seguinte. Clique nos gráficos para ver mais detalhes.

![Primeiros pontos de dados](./media/app-insights-nodejs/12-first-perf.png)

Clique no botão Mapa da aplicação para ver a topologia detetada pela aplicação, conforme a imagem seguinte. Clique nos componentes no mapa para ver mais detalhes.

![Mapa de aplicação individual](./media/app-insights-nodejs/06-appinsights_appmap.png)

Saiba mais sobre a sua aplicação e resolva problemas com as outras vistas disponíveis na secção “Investigar”.

![Secção Investigar](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>Não existem dados?

Visto que o agente coloca os dados em lotes para a submissão, poderá haver um atraso antes de os itens aparecerem no portal. Se não vir dados no seu recurso, experimente algumas das correções seguintes:

* Utilize mais um pouco a aplicação; faça mais ações para gerar mais telemetria.
* Clique em **Atualizar** na vista de recursos do portal. Os gráficos atualizam-se de forma automática periodicamente, mas atualizar desta forma leva a uma atualização imediata.
* Confirme que as [portas de saída necessárias](app-insights-ip-addresses.md) estão abertas.
* Abra o mosaico [Pesquisa](app-insights-diagnostic-search.md) e procure eventos individuais.
* Veja as [FAQ][].


## <a name="agent-configuration"></a>Configuração do Agente

Seguem-se os métodos de configuração do agente e os respetivos valores predefinidos.

Para correlacionar totalmente os eventos num serviço, confirme que define `.setAutoDependencyCorrelation(true)`. Desta forma, o agente pode monitorizar o contexto em chamadas de retorno assíncronas em Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(false)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .start();
```

## <a name="agent-api"></a>API de Agente

<!-- TODO: Fully document agent API. -->

A API de Agente de .NET está descrita integralmente [aqui](app-insights-api-custom-events-metrics.md).

Pode controlar qualquer pedido, evento, métrica ou exceção com o cliente Node.js do Application Insights. O exemplo seguinte demonstra algumas das APIs disponíveis.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var
let client = appInsights.getClient();

client.trackEvent("my custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");

let http = require("http");
http.createServer( (req, res) => {
  client.trackRequest(req, res); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Monitorizar as dependências

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.getClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency("dependency name", "command name", duration, success);
```

### <a name="add-a-custom-property-to-all-events"></a>Adicionar propriedades personalizadas a todos os eventos

```javascript
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Monitorizar pedidos HTTP GET

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.client.trackRequest(req, res);
    }
    // other work here....
    res.end();
});
```

### <a name="track-server-startup-time"></a>Monitorizar o tempo de arranque do servidor

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.client.trackMetric("server startup time", duration);
});
```

## <a name="more-resources"></a>Mais recursos

* [Monitorizar a telemetria no portal](app-insights-dashboards.md)
* [Escrever consultas de análise sobre a telemetria](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: app-insights-troubleshoot-faq.md


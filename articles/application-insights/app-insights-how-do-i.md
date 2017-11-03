---
title: Como posso... no Azure Application Insights | Microsoft Docs
description: FAQ no Application Insights.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: a32127f14c93012b5ace11ff982824f9ecba7d94
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="how-do-i--in-application-insights"></a>Como... no Application Insights?
## <a name="get-an-email-when-"></a>Obter um e-mail quando...
### <a name="email-if-my-site-goes-down"></a>Se o meu site ficar inativo de e-mail
Definir um [teste web de disponibilidade](app-insights-monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Se o meu site está sobrecarregado de e-mail
Definir um [alerta](app-insights-alerts.md) no **tempo de resposta do servidor**. Um limiar entre 1 e 2 segundos deverão funcionar.

![](./media/app-insights-how-do-i/030-server.png)

A aplicação também poderá mostrar sinais de strain devolvendo códigos de falha. Definir um alerta em **pedidos falhados**.

Se pretender definir um alerta em **exceções de servidor**, poderá ter de fazer [alguma configuração adicional](app-insights-asp-net-exceptions.md) para ver os dados.

### <a name="email-on-exceptions"></a>Exceções de e-mail
1. [Configurar a monitorização de exceção](app-insights-asp-net-exceptions.md)
2. [Definir um alerta](app-insights-alerts.md) na exceção de contagem de métrica

### <a name="email-on-an-event-in-my-app"></a>Um evento na minha aplicação de e-mail
Vamos imaginar que gostaria de receber um e-mail quando ocorre um evento específico. Application Insights não fornece este instalações diretamente, mas pode [enviar um alerta quando uma métrica atravesse um limiar](app-insights-alerts.md).

Alertas podem ser definidos no [métricas personalizadas](app-insights-api-custom-events-metrics.md#trackmetric), embora os eventos não personalizados. Escreva alguns códigos para aumentar uma métrica quando da ocorrência do evento:

    telemetry.TrackMetric("Alarm", 10);

Ou:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Porque os alertas têm dois Estados, terá de enviar um valor baixo quando considerar o alerta tem de terminar:

    telemetry.TrackMetric("Alarm", 0.5);

Criar um gráfico no [explorer métrica](app-insights-metrics-explorer.md) para ver o alarme:

![](./media/app-insights-how-do-i/010-alarm.png)

Agora, defina um alerta para accionar quando a métrica fica acima um valor entre durante um curto período de tempo:

![](./media/app-insights-how-do-i/020-threshold.png)

Defina o período da média para o mínimo.

Irá receber e-mails quando a métrica for superior e inferior ao limiar.

Alguns pontos a considerar:

* Um alerta tem dois Estados ("alerta" e "bom estado de funcionamento"). O estado é avaliado apenas quando uma métrica é recebida.
* Mensagens de correio eletrónico é enviada apenas quando o estado é alterado. Este é o motivo pelo qual tem de enviar elevada e métricas de valor baixo.
* Para avaliar o alerta, a média é colocada dos valores recebidos durante o período anterior. Isto ocorre sempre que uma métrica é recebida, pelo que as mensagens de correio eletrónico podem ser enviadas mais frequentemente do que o período de definir.
* Uma vez que os e-mails são enviados no "alerta" e "bom estado de funcionamento", deverá considerar a pensar novamente o evento da como uma condição de dois Estados. Por exemplo, em vez de um evento de "tarefa concluída", ter uma condição de "tarefa em curso", onde pode obter mensagens de correio eletrónico no início e no fim de uma tarefa.

### <a name="set-up-alerts-automatically"></a>Configure alertas automaticamente
[Utilize o PowerShell para criar novos alertas](app-insights-alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Utilize o PowerShell para gerir o Application Insights
* [Criar novos recursos](app-insights-powershell-script-create-resource.md)
* [Criar novos alertas](app-insights-alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Telemetria separada de diferentes versões

* Várias funções numa aplicação: utilizar um único recurso do Application Insights e filtre cloud_Rolename. [Saiba mais](app-insights-monitor-multi-role-apps.md)
* Separação de desenvolvimento, teste e versões: utilizar diferentes recursos do Application Insights. Escolha das chaves de instrumentação do Web. config. [Saiba mais](app-insights-separate-resources.md)
* Relatórios criar versões: adicionar uma propriedade com um inicializador de telemetria. [Saiba mais](app-insights-separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorizar aplicações de ambiente de trabalho e servidores de back-end
[Utilizar o módulo do Windows Server SDK](app-insights-windows-desktop.md).

## <a name="visualize-data"></a>Visualizar dados
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dashboard com a métrica de várias aplicações
* No [métrica Explorer](app-insights-metrics-explorer.md), personalizar o gráfico e guardá-lo como um favorito. Afixá-lo ao dashboard do Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dashboard com dados de outras origens e o Application Insights
* [Exportar a telemetria para o Power BI](app-insights-export-power-bi.md).

Ou

* Utilize o SharePoint como seu dashboard, apresentar os dados nas peças web do SharePoint. [Utilizar a exportação contínua e Stream Analytics para exportar para o SQL](app-insights-code-sample-export-sql-stream-analytics.md).  Utilize PowerView para examinar a base de dados e criar uma peça web do SharePoint para PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrar utilizadores autenticados ou anónimos
Se os seus utilizadores iniciarem sessão, pode definir o [autenticado id de utilizador](app-insights-api-custom-events-metrics.md#authenticated-users). (Se não ocorre automaticamente.)

Em seguida, pode:

* Procurar em ids de utilizador específico

![](./media/app-insights-how-do-i/110-search.png)

* Métricas de filtro para utilizadores anónimos ou autenticados

![](./media/app-insights-how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modificar os nomes de propriedades ou valores
Criar um [filtro](app-insights-api-filtering-sampling.md#filtering). Isto permite-lhe modificar ou filtrar telemetria antes do envio da sua aplicação para o Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Lista de utilizadores específicos e a respetiva utilização
Se apenas pretender [procurar utilizadores específicos](#search-specific-users), pode definir o [autenticado id de utilizador](app-insights-api-custom-events-metrics.md#authenticated-users).

Se pretender que uma lista de utilizadores com dados, tais como as páginas observe ou frequência iniciar sessão, tem duas opções:

* [Id do conjunto de utilizador autenticado](app-insights-api-custom-events-metrics.md#authenticated-users), [exportar para uma base de dados](app-insights-code-sample-export-sql-stream-analytics.md) e utilizar ferramentas adequadas para analisar os dados de utilizador não existe.
* Se tiver apenas um pequeno número de utilizadores, envie eventos personalizados ou métricas, utilizando os dados de interesse como o nome de valor ou evento de métrico e definir o id de utilizador como uma propriedade. Para analisar as vistas de página, substitua a chamada de trackPageView JavaScript padrão. Para analisar a telemetria do lado do servidor, utilize um inicializador de telemetria para adicionar o id de utilizador a telemetria do servidor. Pode, em seguida, as métricas de filtro e do segmento e pesquisas o ID de utilizador.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Reduzir o tráfego da minha aplicação para o Application Insights
* No [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md), desative quaisquer módulos não precisa de, essa o recoletor de contador de desempenho.
* Utilize [amostragem e filtragem](app-insights-api-filtering-sampling.md) no SDK.
* Nas suas páginas web, limite o número de chamadas Ajax comunicado para cada vista de página. No fragmento de script após `instrumentationKey:...` , inserir: `,maxAjaxCallsPerView:3` (ou um número adequado).
* Se estiver a utilizar [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric), a agregação de lotes de valores de métricos de computação antes de enviar o resultado. Há uma sobrecarga de trackmetric () que fornece para esse.

Saiba mais sobre [preços e quotas](app-insights-pricing.md).

## <a name="disable-telemetry"></a>Desativar a telemetria
Para **dinamicamente parar e iniciar** a recolha e a transmissão de telemetria do servidor:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



Para **desativar os recoletores padrão selecionados** - por exemplo, os contadores de desempenho, os pedidos de HTTP ou dependências - eliminar ou comente as linhas relevantes [Applicationinsights](app-insights-api-custom-events-metrics.md). Pode fazer isto, por exemplo, se pretender enviar os seus próprios dados TrackRequest.

## <a name="view-system-performance-counters"></a>Contadores de desempenho do sistema de vista
Entre as métricas que pode mostrar no Explorador de métricas é um conjunto de sistema contadores de desempenho. Há um painel predefinido intitulado **servidores** que apresenta muitos dos mesmos.

![Abra o recurso do Application Insights e clique em servidores](./media/app-insights-how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Se vir que não existem dados de contador de desempenho
* **Servidor IIS** no seu próprio computador ou numa VM. [Instalar o Monitor de estado](app-insights-monitor-performance-live-website-now.md).
* **Web site do Azure** -Microsoft não suporta os contadores de desempenho. Existem várias métricas, que pode obter como parte padrão de painel de controlo do web site do Azure.
* **Servidor UNIX** - [instale collectd](app-insights-java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Para apresentar mais contadores de desempenho
* Primeiro, [adicionar um novo gráfico](app-insights-metrics-explorer.md) e verificar se o contador no conjunto de básico que oferecemos.
* Caso contrário, [adicionar o contador para o conjunto de recolhidos pelo módulo de contador de desempenho](app-insights-performance-counters.md).

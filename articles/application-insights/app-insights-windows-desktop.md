---
title: "Monitorização de utilização e desempenho de aplicações de ambiente de trabalho do Windows"
description: "Análise da utilização e desempenho da sua aplicação de ambiente de trabalho do Windows com o HockeyApp e o Application Insights."
services: application-insights
documentationcenter: windows
author: CFreemanwa
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: bwren
ms.openlocfilehash: 9d7e2a390adf10cbf5d88dd0084ce09136987309
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Monitorização de utilização e desempenho de aplicações de Ambiente de Trabalho do Windows


O [Azure Application Insights](app-insights-overview.md) e o [HockeyApp](https://hockeyapp.net) permitem-lhe monitorizar a aplicação implementada para utilização e desempenho.

> [!IMPORTANT]
> Recomendamos o [HockeyApp](https://hockeyapp.net) para distribuir e monitorizar aplicações de ambiente de trabalho e do dispositivo. Com o HockeyApp, pode gerir a distribuição, os testes em direto e os comentários do utilizador, bem como monitorizar relatórios de utilização e falhas. Também pode [exportar e consultar a telemetria com a Análise](app-insights-hockeyapp-bridge-app.md).
> 
> Apesar de a telemetria poder ser enviada para o Application Insights a partir de uma aplicação de ambiente de trabalho, isto é principalmente útil para fins de depuração e experimentais.
> 
> 

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Para enviar a telemetria para o Application Insights a partir de uma aplicação do Windows
1. No [portal do Azure](https://portal.azure.com), [crie um recurso do Application Insights](app-insights-create-new-resource.md). Para o tipo de aplicação, escolha a aplicação ASP.NET.
2. Faça uma cópia da Chave de Instrumentação. Encontre a chave na lista pendente Essentials do novo recurso que acabou de criar. 
3. No Visual Studio, edite os pacotes NuGet do seu projeto da aplicação e adicione Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft.ApplicationInsights se apenas pretender a API bare, sem os módulos de coleção de telemetria standard.)
4. Defina a chave de instrumentação no seu código:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *a sua chave* `";` 
   
    ou no Applicationinsights (se tiver instalado um dos pacotes de telemetria standard):
   
    `<InstrumentationKey>`*a sua chave*`</InstrumentationKey>` 
   
    Se utilizar o ApplicationInsights.config, certifique-se de que as propriedades no Explorador de Soluções estão definidas para **Ação de Compilação = Conteúdo, Copiar para o Diretório de Saída = Copiar**.
5. [Utilize a API](app-insights-api-custom-events-metrics.md) para enviar telemetria.
6. Execute a aplicação e veja a telemetria no recurso que criou no Portal do Azure.

## <a name="telemetry"></a>Código de exemplo
```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Passos seguintes
* [Criar um dashboard](app-insights-dashboards.md)
* [Pesquisa de Diagnóstico](app-insights-diagnostic-search.md)
* [Explorar métricas](app-insights-metrics-explorer.md)
* [Escrever consultas da Análise](app-insights-analytics.md)


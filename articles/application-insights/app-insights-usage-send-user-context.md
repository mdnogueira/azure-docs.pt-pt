---
title: "Enviar IDs para ativar a utilização de contexto de utilizador experiências no Azure Application Insights | Microsoft Docs"
description: "Controle a forma como os utilizadores passam através do seu serviço atribuindo uma cadeia de ID exclusiva, persistente no Application Insights para cada um deles."
services: application-insights
documentationcenter: 
author: abgreg
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: mbullwin
ms.openlocfilehash: 9d9360d77c6a0db82d3708baf4af15a0fcdc188e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Enviar o contexto de utilizador IDs proporcionar experiências de utilização no Azure Application Insights

## <a name="tracking-users"></a>Os utilizadores de controlo

Application Insights permite-lhe monitorizar e controlar os seus utilizadores através de um conjunto de ferramentas de utilização do produto: 
* [Utilizadores, Sessões, Eventos](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Funis](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Retenção](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Cohorts
* [Livros](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Para poder controlar que um utilizador faz ao longo do tempo, o Application Insights tem um ID para cada utilizador ou a sessão. Inclua os seguintes IDs em cada vista de página ou eventos personalizada.
- Os utilizadores, Funnels, retenção e Cohorts: incluem o ID de utilizador.
- Sessões: Incluem o ID de sessão.

Se a aplicação está integrada com o [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), utilizador ID é registado automaticamente.

## <a name="choosing-user-ids"></a>Escolher os IDs de utilizador

Os IDs de utilizador devem são mantidas entre sessões de utilizador para controlar a forma como os utilizadores comportar-se ao longo do tempo. Existem várias abordagens para a persistência do ID.
- Uma definição de um utilizador que já tem no seu serviço.
- Se o serviço tem acesso a um browser, este pode passar o browser um cookie com um ID na mesma. O ID será mantido para, desde que o cookie permanece no browser do utilizador.
- Se necessário, pode utilizar um novo ID de cada sessão, mas os resultados sobre os utilizadores serão limitados. Por exemplo, não será capaz de ver como alterações de comportamento do utilizador ao longo do tempo.

O ID deve ser um Guid ou outra cadeia suficientemente complexa para identificar exclusivamente cada utilizador. Por exemplo, poderia ser um número de tempo aleatório.

Se o ID do contém informações de identificação pessoal sobre o utilizador, não é um valor adequado para enviar para o Application Insights como um ID de utilizador. Pode enviar esse um ID como um [autenticado ID de utilizador](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), mas não cumpre o requisito de ID de utilizador para cenários de utilização.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplicações do ASP.NET: definir o contexto de utilizador num ITelemetryInitializer

Crie um inicializador de telemetria, conforme descrito detalhadamente [aqui](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer)e defina o Context.User.Id e o Context.Session.Id.

Neste exemplo define o ID de utilizador para um identificador que expira após a sessão. Se possível, utilize um ID de utilizador que persiste entre sessões.

```C#

    using System;
    using System.Web;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that sets the user ID.
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>Passos seguintes
- Para ativar as experiências de utilização, começar a enviar [eventos personalizados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [vistas de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se já enviar eventos personalizados ou vistas de página, explore as ferramentas de utilização para saber como os utilizadores utilizam o serviço.
    * [Descrição geral da utilização](app-insights-usage-overview.md)
    * [Os utilizadores, sessões e os eventos](app-insights-usage-segmentation.md)
    * [Funis](usage-funnels.md)
    * [Retenção](app-insights-usage-retention.md)
    * [Livros](app-insights-usage-workbooks.md)

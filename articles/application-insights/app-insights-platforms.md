---
title: "Application Insights: linguagens, plataformas e integrações | Microsoft Docs"
description: "Linguagens, plataformas e integrações disponíveis para o Application Insights"
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
manager: douge
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 42507475b5d15c4704e6bcb3d56dc00c91006655
ms.lasthandoff: 03/31/2017


---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Análise do programador: idiomas, plataformas e integrações
Estes itens são implementações do [Application Insights](app-insights-overview.md) que já conhecemos, incluindo alguns de terceiros.

## <a name="languages"></a>Linguagens
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Páginas Web JavaScript](app-insights-web-track-usage.md)
* [Objective-C](https://github.com/Microsoft/ApplicationInsights-iOS)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Tudo o resto](#projects)

## <a name="platforms-and-frameworks"></a>Plataformas e estruturas
* [Angular](https://www.npmjs.com/package/angular-applicationinsights)
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET – para aplicações que já estão em direto](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET 5](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (HockeyApp)
* [Aplicações Web do Azure](app-insights-azure-web-apps.md)
* [Cloud Services do Azure](app-insights-cloudservices.md): incluindo funções Web e funções de trabalho
* [CRM Online da Microsoft Dynamics](app-insights-sample-mscrm.md)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE – para aplicações que já estão em direto](app-insights-java-live.md)
* [Aplicação do Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Aplicação universal do Windows](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Aplicação do Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Aplicação do Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Aplicações de ambiente de trabalho, serviços e funções de trabalho do Windows](app-insights-windows-desktop.md)
* [Tudo o resto](#projects)

## <a name="logging-frameworks"></a>Arquiteturas de registo
* [Log4Net, NLog ou System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J ou Logback](app-insights-java-trace-logs.md)
* [Semantic Logging (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) -integra-se no [Semantic Logging Application Block](https://msdn.microsoft.com/library/dn440729.aspx)
* [Teste de carga baseado na nuvem](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Plug-in do LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [OMS Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>Sistemas de Gestão de Conteúdo
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://orchardazureappinsights.codeplex.com)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Análise de Dados e Exportação
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Criar o seu próprio SDK
Se ainda não tem um SDK para o seu idioma ou plataforma, gostaria de criar um? Observe o código dos SDKs existentes listados no [Projeto Application Insights SDK no GitHub](https://github.com/Microsoft/AppInsights-Home).


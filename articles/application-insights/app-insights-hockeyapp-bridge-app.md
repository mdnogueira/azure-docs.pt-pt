---
title: Explorar os dados do HockeyApp no Azure Application Insights | Microsoft Docs
description: "Analise a utilização e desempenho da sua aplicação do Azure com o Application Insights."
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: bec2b2dc8a29e5c94e3d3c7f2f0333e07bfe3a4f
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Explorar os dados do HockeyApp no Application Insights

> [!NOTE]
> Centro de aplicação do Visual Studio é agora o serviço da Microsoft recomendado para monitorização de aplicações móveis novo. [Saiba como configurar as suas aplicações com o Centro de aplicação e o Application Insights](app-insights-mobile-center-quickstart.md).
> 
> 

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) é um serviço para monitorização de aplicações de ambiente de trabalho e dispositivos móveis em direto. Do HockeyApp, pode enviar personalizado e rastrear a telemetria para monitorizar a utilização e ajudar no diagnóstico (para além de obtenção de dados de falhas). Este fluxo de telemetria pode ser consultado utilizando poderoso [análise](app-insights-analytics.md) funcionalidade do [Azure Application Insights](app-insights-overview.md). Além disso, pode [exportar personalizado e telemetria de rastreio](app-insights-export-telemetry.md). Para ativar estas funcionalidades, configure uma ponte que transmite dados personalizados do HockeyApp para o Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>A aplicação do HockeyApp Bridge
A aplicação de Bridge HockeyApp é a funcionalidade de núcleos que lhe permite aceder aos seus HockeyApp personalizado e a telemetria de rastreio no Application Insights através das funcionalidades de exportação contínua e de análise. Eventos personalizados e rastreio recolhidos pelo HockeyApp após a criação da aplicação de Bridge HockeyApp estará acessíveis a partir destas funcionalidades. Vamos ver como configurar uma destas aplicações Bridge.

No HockeyApp, abra as definições da conta, [API Tokens](https://rink.hockeyapp.net/manage/auth_tokens). Crie um novo token ou reutilizar um existente. Os direitos mínimos necessários são "só de leitura". Efetuar uma cópia da API de token.

![Obtenha o token de uma API do HockeyApp](./media/app-insights-hockeyapp-bridge-app/01.png)

Abra o portal do Microsoft Azure e [crie um recurso do Application Insights](app-insights-create-new-resource.md). Definir o tipo de aplicação a "Aplicação de bridge HockeyApp":

![Novo recurso do Application Insights](./media/app-insights-hockeyapp-bridge-app/02.png)

Não precisa de definir um nome - isto automaticamente irá definir o nome do HockeyApp do.

Os campos de bridge HockeyApp aparecem. 

![Introduza os campos de bridge](./media/app-insights-hockeyapp-bridge-app/03.png)

Introduza o token do HockeyApp que anotou anteriormente. Esta ação preenche o menu de lista pendente "Aplicação HockeyApp" com todas as suas aplicações do HockeyApp. Selecione a que pretende utilizar e concluir o resto dos campos. 

Abra o novo recurso. 

Tenha em atenção que os dados demora algum tempo para iniciar o fluxo.

![Recurso de informações de aplicação a aguardar a dados](./media/app-insights-hockeyapp-bridge-app/04.png)

Já está! Dados personalizados e rastreio recolhidos na sua aplicação instrumentados o HockeyApp a partir deste ponto também estão agora disponíveis nas funcionalidades de exportação contínua e de análise do Application Insights.

Vamos brevemente rever cada uma destas funcionalidades agora disponíveis para si.

## <a name="analytics"></a>Análise
A análise é uma ferramenta poderosa para consultas ad-hoc dos seus dados, permitindo-lhe diagnosticar e analisar a telemetria e rapidamente detetar as causas raiz e padrões.

![Análise](./media/app-insights-hockeyapp-bridge-app/05.png)

* [Saiba mais sobre a análise](app-insights-analytics-tour.md)

## <a name="continuous-export"></a>Exportação contínua
A exportação contínua permite-lhe exportar os dados para um contentor do Blob Storage do Azure. Isto é muito útil se pretender manter os seus dados durante mais tempo do que o período de retenção atualmente oferecido pelo Application Insights. Pode manter os dados no blob storage, processá-la para uma base de dados do SQL Server ou a sua solução de armazém de dados preferenciais.

[Saiba mais sobre a exportação contínua](app-insights-export-telemetry.md)

## <a name="next-steps"></a>Passos seguintes
* [Aplicar análise aos seus dados](app-insights-analytics-tour.md)


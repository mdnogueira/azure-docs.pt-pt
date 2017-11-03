---
title: "Início rápido com o Azure Application Insights | Microsoft Docs"
description: "Fornece instruções para configurar rapidamente a uma aplicação móvel para monitorização com o Application Insights e o Centro de Mobile"
services: application-insights
keywords: 
author: numberbycolors
ms.author: daviste
ms.date: 10/05/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: a8af65a9a9f31200f833a47f811ae50a0a9d2f9b
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="start-analyzing-your-mobile-app-with-mobile-center-and-application-insights"></a>Iniciar a análise da aplicação móvel com Mobile Center e o Application Insights

Este guia de introdução orienta-o ligar a instância de Center móveis da sua aplicação para o Application Insights. Com o Application Insights, pode consultar, segmentar, filtrar e analisar a telemetria com ferramentas mais poderosas que estão disponíveis a partir de [análise](https://docs.microsoft.com/mobile-center/analytics/) serviço do Centro de Mobile.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

- Uma subscrição do Azure.
- Um iOS, Android, Xamarin, Universal do Windows ou aplicação reagir nativa.
 
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="onboard-to-mobile-center"></a>Carregar para o Centro de Mobile

Antes de poder utilizar o Application Insights com a sua aplicação móvel, terá de carregar a aplicação [Mobile Center](https://docs.microsoft.com/mobile-center/). Application Insights não recebe a telemetria da sua aplicação móvel diretamente. Em vez disso, a sua aplicação enviar telemetria de evento personalizado para o Centro de Mobile. Em seguida, Mobile Center continuamente exporta cópias destes eventos personalizados no Application Insights como os eventos são recebidos.

Integrar a aplicação, siga o guia de introdução do Mobile Center para cada plataforma suporta a sua aplicação. Crie instâncias Mobile Center separadas para cada plataforma:

* [iOS](https://docs.microsoft.com/mobile-center/sdk/getting-started/ios).
* [Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/android).
* [Xamarin](https://docs.microsoft.com/mobile-center/sdk/getting-started/xamarin).
* [Universal Windows](https://docs.microsoft.com/mobile-center/sdk/getting-started/uwp).
* [Reagir nativo](https://docs.microsoft.com/mobile-center/sdk/getting-started/react-native).

## <a name="track-events-in-your-app"></a>Controlar eventos na sua aplicação

Após a sua aplicação integradas ao centro de móveis, tem de ser modificados para enviar telemetria relacionada com eventos personalizados utilizando o SDK do System Center Mobile. Eventos personalizados são o único tipo de telemetria de Center móveis que é exportado para o Application Insights.

Para enviar eventos personalizados a partir de aplicações iOS, utilize o `trackEvent` ou `trackEvent:withProperties` métodos no Mobile Center SDK. [Saiba mais sobre o controlo de eventos de aplicações iOS.](https://docs.microsoft.com/mobile-center/sdk/analytics/ios)

```Swift
MSAnalytics.trackEvent("Video clicked")
```

Para enviar eventos personalizados a partir de aplicações Android, utilize o `trackEvent` método no Mobile Center SDK. [Saiba mais sobre o controlo de eventos de aplicações Android.](https://docs.microsoft.com/mobile-center/sdk/analytics/android)

```Java
Analytics.trackEvent("Video clicked")
```

Para enviar eventos personalizados a partir de outras plataformas de aplicação, utilize o `trackEvent` métodos no respetivo SDKs do System Center Mobile.

Para certificar-se de que estão a ser recebidos os eventos personalizados, aceda ao **eventos** separador sob o **análise** secção Mobile Center. Pode demorar alguns minutos para eventos apareçam de quando estes está a ser enviados da sua aplicação.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Depois da aplicação está a enviar eventos personalizados e estes eventos estão a ser recebidos pelo centro de móveis, terá de criar um recurso de tipo Center Mobile Application Insights no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Novo** > **Monitorização + Gestão** > **Application Insights**.

    ![Adicionar recurso do Application Insights](./media/app-insights-mobile-center-quickstart/add.png)

    Será apresentada uma caixa de configuração. Utilize a tabela abaixo para preencher os campos de entrada.

    | Definições        |  Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Um valor exclusivo global, como "myApp iOS" | Nome que identifica a aplicação que está a monitorizar |
   | **Tipo de Aplicação** | Aplicação de Mobile Center | Tipo de aplicação que está a monitorizar |
   | **Grupo de Recursos**     | Um novo grupo de recursos ou um existente a partir do menu | O grupo de recursos no qual pretende criar o novo recurso do Application Insights |
   | **Localização** | Uma localização a partir do menu | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

3. Clique em **Criar**.

Se a aplicação suportar várias plataformas (iOS, Android, etc.), é melhor criar recursos do Application Insights separados, um para cada plataforma.

## <a name="export-to-application-insights"></a>Exportar para o Application Insights

No seu novo recurso do Application Insights no **descrição geral** página no **Essentials** secção na parte superior, copie a chave de instrumentação deste recurso.

Na instância Mobile Center para a sua aplicação:

1. No **definições** página, clique em **exportar**.
2. Escolha **exportar novo**, escolha **Application Insights**, em seguida, clique em **personalizar**.
3. Cole a chave de instrumentação do Application Insights na caixa.
4. Consentimento para aumentar a utilização da subscrição do Azure que contém o recurso do Application Insights. Cada recurso do Application Insights é gratuito para o primeiro 1 GB de dados recebidos por mês. [Saiba mais sobre os preços do Application Insights.](https://azure.microsoft.com/pricing/details/application-insights/)

Lembre-se repetir este processo para cada plataforma suporta a sua aplicação.

Uma vez [exportar](https://docs.microsoft.com/mobile-center/analytics/export) está definido até, cada evento personalizado recebido pelo centro de Mobile é copiado para o Application Insights. Pode demorar alguns minutos para os eventos alcançar o Application Insights, pelo que o se estes não apareçam imediatamente, aguardar um pouco antes de diagnosticar mais.

Para lhe dar mais dados quando liga pela primeira vez, as mais recentes 48 horas dos eventos personalizados no Centro de Mobile automaticamente são exportadas para o Application Insights.

## <a name="start-monitoring-your-app"></a>Iniciar a sua aplicação de monitorização

Application Insights podem consultar, segmentar, filtrar e analisar a telemetria de evento personalizado das suas aplicações, para além das ferramentas de análise que Mobile Center fornece.

1. **Consulta de telemetria de evento personalizado.** Do Application Insights **descrição geral** página, escolha **análise**. 

   ![Botão de análise no Application Insights](./media/app-insights-mobile-center-quickstart/analytics.png)

   Portal do Application Insights Analytics associado recurso do Application Insights irá abrir. O portal da análise permite-lhe diretamente consultar os dados utilizando a linguagem de consulta de análise de registos, pelo que pode colocar perguntas arbitrariamente complexas sobre a aplicação e os seus utilizadores.
   
   Abra um novo separador no portal da análise, em seguida, cole a seguinte consulta. Devolve uma contagem do número de utilizadores distinto que enviaram cada evento personalizado da sua aplicação nas últimas 24 horas, ordenados por estas contagens distintas.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Portal da análise](./media/app-insights-mobile-center-quickstart/analytics-portal.png)

   1. Selecione a consulta, clicando em qualquer lugar na consulta no editor de texto.
   2. Em seguida, clique em **aceda** para executar a consulta. 

   Saiba mais sobre [Application Insights Analytics](app-insights-analytics.md) e [idioma de consulta de análise de registos](https://docs.loganalytics.io/docs/Language-Reference).


2. **Segmentar e filtrar a telemetria de evento personalizado.** Do Application Insights **descrição geral** página, escolha **utilizadores** no índice.

   ![Ícone de ferramenta de utilizadores](./media/app-insights-mobile-center-quickstart/users-icon.png)

   A ferramenta utilizadores mostra quantos utilizadores da sua aplicação clicado determinados botões, visitada determinadas ecrãs ou efetuar qualquer ação que está a controlar como um evento com o SDK do System Center Mobile. Se tiver sido à procura de uma forma segmentar e filtrar os eventos de Mobile Center, a ferramenta de utilizadores é uma escolha ideal.

   ![Ferramenta de utilizadores](./media/app-insights-mobile-center-quickstart/users.png) 

   Por exemplo, segmentar a sua utilização por geografia escolhendo **país ou região** no **dividir por** menu pendente.

3. **Analise padrões de conversão, retenção e navegação na sua aplicação.** Do Application Insights **descrição geral** página, escolha **utilizador flui** no índice.

   ![Ferramenta de fluxos de utilizador](./media/app-insights-mobile-center-quickstart/user-flows.png)

   A ferramenta de utilizador fluxos visualiza os eventos que os utilizadores enviar após alguns evento inicial. É útil para obter uma visão geral do Estado de como os utilizadores navegam através da sua aplicação. Também pode revelar dos locais onde muitos utilizadores churning da sua aplicação ou repetindo as mesmas ações mais e superior.

   Para além de fluxos de utilizador, o Application Insights tem várias ferramentas de análise outros utilização para responder a perguntas específicas:

   * **Funnels** para analisar e taxas de conversão de monitorização.
   * **Retenção** para analisar o quão bem a aplicação mantém os utilizadores ao longo do tempo.
   * **Os livros** para combinar o texto e visualizações para um relatório partilhável.
   * **Cohorts** para atribuição de nomes e guardar grupos específicos de utilizadores ou de eventos, pelo que podem ser facilmente referenciados a partir de outras ferramentas de análise.

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar o Application Insights com o Centro de Mobile, desativar a exportação no Centro de Mobile e eliminar o recurso do Application Insights. Isto irá impedir a que está a ser cobrados mais pelo Application Insights para este recurso.

Para desativar a exportação no Centro de Mobile:

1. No Centro de Mobile, aceda a **definições** e escolha **exportar**.
2. Clique em exportar o Application Insights que pretende eliminar, em seguida, clique em **eliminar exportação** na parte inferior e confirme.

Para eliminar o recurso do Application Insights:

1. No menu da esquerda do portal do Azure, clique em **grupos de recursos** e, em seguida, escolha o grupo de recursos no qual o recurso do Application Insights foi criado.
2. Abra o recurso do Application Insights que pretende eliminar. Em seguida, clique em **eliminar** no menu superior do recurso e confirme. Esta ação eliminará permanentemente a cópia dos dados que foi exportados para o Application Insights.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Compreender como os clientes estão a utilizar a aplicação](app-insights-usage-overview.md)
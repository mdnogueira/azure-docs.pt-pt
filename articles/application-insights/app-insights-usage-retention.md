---
title: "Análise de retenção de utilizador para aplicações web com o Azure Application Insights | Documentos da Microsoft"
description: "Quantos utilizadores regressar à sua aplicação?"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: f19e5022d366bc686beed42bacf8a4d6d108887e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análise de retenção de utilizador para aplicações web com o Application Insights

A funcionalidade de retenção no [Azure Application Insights](app-insights-overview.md) ajuda a analisar quantos utilizadores regressar à sua aplicação e frequência efetuar tarefas específicas ou atingir objetivos. Por exemplo, se executar um site de jogo, pode comparar o número de utilizadores que voltar para o site após a perda de um jogo com o número de retorno após prevalecente. Estes conhecimentos podem ajudar a melhorar a experiência de utilizador e a estratégia de negócio.

## <a name="get-started"></a>Introdução

Se não vir ainda dados na ferramenta de retenção no portal do Application Insights, [aprender a começar a utilizar as ferramentas de utilização](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>A ferramenta de retenção

![Ferramenta de retenção](./media/app-insights-usage-retention/retention.png)

1. Barra de ferramentas permite aos utilizadores criar novos relatórios de retenção, abrir relatórios existentes de retenção, guardar o relatório de retenção atual ou guardar como, reverter as alterações feitas a relatórios guardados, atualizar dados no relatório, partilhar o relatório por e-mail ou de ligação direta e a documentação de acesso página. 
2. Por predefinição, o retenção mostra todos os utilizadores que tiver tudo, em seguida, volta veio e foram há mais alguma coisa durante um período. Pode selecionar diferentes combinação de eventos para restringir o enfoque em atividades de utilizador específico.
3. Adicione um ou mais filtros nas propriedades. Por exemplo, pode concentrar nos utilizadores de um determinado país ou região. Clique em **atualização** depois de definir os filtros. 
4. O gráfico de retenção global mostra um resumo de retenção de utilizador entre o período de tempo selecionado. 
5. Grelha mostra o número de utilizadores retidos, de acordo com o construtor de consultas no #2. Cada linha representa um coorte de utilizadores que executou qualquer evento dentro do tempo período mostrado. Cada célula na linha mostra quantos dos que coorte devolvido pelo menos uma vez num período posterior. Alguns utilizadores podem devolver mais do que um ponto. 
6. Os cartões de insights mostram eventos de início de cinco superiores e superiores eventos devolvidos cinco conceder aos utilizadores uma melhor compreensão sobre os relatórios de retenção. 

![Passagem do rato de retenção](./media/app-insights-usage-retention/hover.png)

Os utilizadores podem paira o rato sobre células na ferramenta de retenção para as sugestões de botão e a ferramenta de análise explicar o que significa a célula de acesso. O botão de análise direciona os utilizadores para a ferramenta de análise com uma consulta pré-preenchidos para gerar os utilizadores da célula. 

## <a name="use-business-events-to-track-retention"></a>Utilizar eventos de negócio para controlar a retenção

Para obter a análise de retenção mais úteis, medir os eventos que representam as atividades de negócio significativas. 

Por exemplo, vários utilizadores podem abrir uma página na sua aplicação sem jogar no jogo que é apresentado. Apenas as vistas de página de controlo, por conseguinte, iria fornecer uma estimativa de como muitas pessoas devolvem para reproduzir no jogo após desfrutar dos-la anteriormente incorreta. Para obter uma fotografia limpar devolver jogadores, a aplicação deve enviar um evento personalizado quando um utilizador, na verdade, desempenha.  

É recomendável code eventos personalizados que representam ações empresarial fundamentais e utilizá-los para a análise de retenção. Para capturar o resultado de jogo, terá de escrever uma linha de código para enviar um evento personalizado para o Application Insights. Se escrever-a no código da página web ou no Node.JS, este aspeto:

```JavaScript
    appinsights.trackEvent("won game");
```

Ou no código de servidor ASP.NET:

```C#
   telemetry.TrackEvent("won game");
```

[Saiba mais sobre como escrever eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Passos seguintes
- Para ativar as experiências de utilização, começar a enviar [eventos personalizados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [vistas de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se já enviar eventos personalizados ou vistas de página, explore as ferramentas de utilização para saber como os utilizadores utilizam o serviço.
    - [Utilizadores, Sessões, Eventos](app-insights-usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Fluxos do Utilizador](app-insights-usage-flows.md)
    - [Livros](app-insights-usage-workbooks.md)
    - [Adicionar o contexto de utilizador](app-insights-usage-send-user-context.md)



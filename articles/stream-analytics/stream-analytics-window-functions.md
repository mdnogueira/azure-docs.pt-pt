---
title: "Introdução às funções de janela do Stream Analytics | Microsoft Docs"
description: "Saiba mais sobre as funções de janela três do Stream Analytics (em cascata, hopping, a deslizante)."
keywords: em cascata janela, numa janela, hopping janela deslizante
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 0d8d8717-5d23-43f0-b475-af078ab4627d
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 2a6559551f608cf435e89997392a6a0ba995c583
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Introdução às funções de janela do Stream Analytics
Em tempo real muitos cenários de transmissão em fluxo, é necessário para efetuarem operações apenas nos dados contidos no windows temporais. Suporte nativo para funções de modos de janela é uma funcionalidade chave do Azure Stream Analytics que são transmitidos a agulha na produtividade do programador na criação as tarefas de processamento de fluxo complexas. Do Stream Analytics permite aos programadores utilizar [ **em cascata**](https://msdn.microsoft.com/library/dn835055.aspx), [ **Hopping** ](https://msdn.microsoft.com/library/dn835041.aspx) e [ **deslizantes** ](https://msdn.microsoft.com/library/dn835051.aspx) windows para executar operações temporais nos dados de transmissão em fluxo. É importante salientar que, todos os [janela](https://msdn.microsoft.com/library/dn835019.aspx) operações saída resultados no **final** da janela. A saída da janela serão evento único com base na função de agregação utilizada. O evento tem o carimbo de hora de fim da janela e todas as funções de janela estão definidas com um comprimento fixo. Por último é importante ter em atenção que todas as funções de janela devem ser utilizadas num [ **GROUP BY** ](https://msdn.microsoft.com/library/dn835023.aspx) cláusula.

![Conceitos de funções de janela de análise de fluxo](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Janela em cascata
Em cascata janela funções são utilizadas para segmentar um fluxo de dados em segmentos de tempo distintos e efetuar uma função contra eles, como o exemplo abaixo. As chaves differentiators de uma janela em cascata são que eles repetir, não se sobreponha e um evento não pode pertencer a mais do que uma janela em cascata.

![Funções de janela de análise de fluxo em cascata introdução](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Janela de salto
Salto salto de funções de janela reencaminhar na hora por um período fixo. Poderá ser mais fácil considerá-los como janelas em cascata podem sobrepor-se, pelo que os eventos podem pertencer a mais do que um conjunto de resultados de janela Hopping. Para tornar uma janela de Hopping o mesmo que um em cascata janela um simplesmente especifique o tamanho de salto deve ser o mesmo que o tamanho da janela. 

![Janela do Stream Analytics funciona salto de introdução](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Numa janela deslizante
Funções de janela deslizante, ao contrário do salto windows, ou em cascata produzem uma saída **apenas** quando ocorre um evento. Cada janela vai ter, pelo menos, um evento e a janela continuamente move reencaminhar por um € (epsilon). Como salto Windows, os eventos podem pertencer a mais do que uma janela a deslizante.

![Funções de Stream Analytics janela deslizante de introdução](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obter ajuda com as funções de janela
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


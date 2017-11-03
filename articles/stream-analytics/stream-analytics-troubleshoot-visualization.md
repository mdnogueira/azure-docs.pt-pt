---
title: Visualizar e resolver problemas relacionados com tarefas do Stream Analytics | Microsoft Docs
description: "Saiba como visualizar um pipeline de tarefa do Stream Analytics para o self-service de resolução de problemas com a funcionalidade de diagrama de diagnóstico."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 820b73a5dbf9bb108e189313cf6ee2b924ab04c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualizar e resolver problemas relacionados com tarefas do Stream Analytics
No Stream Analytics, tal como acontece com outras tecnologias baseado na nuvem, resolução de problemas é por vezes, necessário a razão pela qual uma tarefa não produzir o resultado esperado (ou qualquer saída para esse fim). Com este conceito em mente, o Stream Analytics fornece a capacidade para visualizar uma tarefa de transmissão em fluxo. Isto também é útil como uma ferramenta de modelação e tem o benefício do lado desses necessitando de documentação do seu trabalho.

No painel de visualização, as entradas estão visíveis, bem como a consulta a ser executado e, em seguida, todas as saídas configuradas. Problemas de conetividade ou a configuração podem tornar-se mais aparente e também pode ser útil ver uma representação visual da configuração.

## <a name="using-the-diagnosis-diagram-tool"></a>Utilizar a ferramenta de diagrama de diagnóstico
Para aceder a este visualizador, basta clicar no botão "Diagrama de diagnóstico" na área da tarefa de Stream Analytics "Definições".

![Stream-Analytics-Troubleshoot-visualization-Diagnosis-Diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Cada entrada e saída são o estado de cor codificada para indicar atual desse componente, conforme mostrado da seguinte forma.

![Stream-Analytics-Troubleshoot-visualization-Input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Quando o utilizador pretende ver passos da consulta intermédio para compreender os padrões de fluxo de dados dentro de uma tarefa, a ferramenta de visualização fornece uma vista de divisão da consulta para os passos de componente e a sequência de fluxo. Clicar em cada passo de consulta mostra a secção correspondente numa consulta painel conforme ilustrado a editar. 

![Stream-Analytics-Troubleshoot-visualization-Intermediate-Steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


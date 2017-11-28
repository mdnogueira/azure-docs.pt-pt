---
title: Consulta de teste no Azure Stream Analytics | Microsoft Docs
description: Identificar problemas quando resolver problemas relacionados com tarefas do Stream Analytics.
keywords: resolver problemas de amostragem de entrada, a entrada
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e2636b8b89b86bbb2a2991972386462535d5a10f
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="azure-stream-analytics-query-testing-and-input-stream-sampling"></a>A amostragem de fluxo de entrada e de teste de consulta do Stream Analytics do Azure

Ao utilizar o Azure Stream Analytics, pode apresentar exemplos eventos de entrada que vêm de um ficheiro e testar consultas no portal sem ser necessário iniciar ou parar uma tarefa.

## <a name="testing-your-query"></a>Testar a sua consulta

No painel de detalhes de tarefa de Stream Analytics, abra o **editor de consultas** painel ao clicar no nome de consulta em **consulta**. (No nosso cenário de exemplo, porque ainda foi criada sem consulta, clique em de **< >** marcador de posição.)

![O editor de consultas do Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

O painel de editor avançado para criar a sua consulta é apresentado tal como estava na versão anterior. O painel foi atualizado com um novo painel esquerdo, que mostra as entradas e saídas que são utilizadas pela consulta e definidas para esta tarefa.

![O editor de consultas do Stream Analytics entradas e saídas listas](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Também mostradas são uma entrada adicional e de saída, que não estão definidas. O novo modelo de consulta que comece com estes provenientes. Alterar ou mesmo desaparecer completamente, à medida que edita a consulta. Pode ignorá-los por agora.

Para testar com dados de entrada de exemplo, faça duplo clique de qualquer uma das entradas e, em seguida, selecione **carregar dados de exemplo do ficheiro**.

![Os dados de exemplo de carregamento de editor de consulta do Stream Analytics do comando de ficheiro](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

Depois do carregamento estiver concluído, clique em **testar** para testar esta consulta contra os dados de exemplo que acabou de fornecer.

![O botão de teste do editor de consulta do Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Se pretender guardar o teste de saída para utilização posterior, o resultado da sua consulta é apresentado no browser com uma ligação para os resultados da transferência. Pode facilmente e iteratively modifique a consulta e testá-lo repetidamente para ver como o resultado é alterado.

![Saída de exemplo do editor de consulta do Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

Na imagem anterior, foi adicionada uma saída segundo, denominado **HighAvgTempOutput**.

Quando utilizar várias saídas numa consulta, pode ver os resultados para cada saída separadamente e facilmente alternar entre elas.

Depois de se satisfeito com os resultados, pode guardar a consulta, iniciar a sua tarefa, manter-se novamente e veja a magia do Stream Analytics.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

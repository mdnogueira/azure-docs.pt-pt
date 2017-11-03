---
title: "Como iniciar a transmissão em fluxo de tarefas no Stream Analytics | Microsoft Docs"
description: "Como executar uma tarefa de transmissão em fluxo no Azure Stream Analytics | Learning segmento de caminho."
keywords: "tarefas de transmissão em fluxo"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9d46950f-2b69-49ce-a567-df558c5dd820
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: fb084f1c6b53e2582849e71271e8114a22dcf05c
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Como executar uma tarefa de transmissão em fluxo no Azure Stream Analytics
Quando uma tarefa de entrada, a consulta e a saída todas foram especificadas que pode iniciar a tarefa de Stream Analytics.

Para iniciar a tarefa:

1. No portal clássico do Azure, a partir do dashboard tarefa, clique em **iniciar** na parte inferior da página.
   
   ![Iniciar a tarefa de botão](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  
   
   No portal do Azure, clique em **iniciar** na parte superior da sua página de tarefa.
   
   ![Tarefa de início botão portal do Azure](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  
2. Especifique um **iniciar saída** valor para determinar quando esta tarefa irá começar a produzir o resultado. A predefinição para as tarefas que anteriormente não foram iniciados é **hora de início da tarefa**, o que significa que a tarefa começará imediatamente processar os dados. Também pode especificar um **personalizada** hora no passado (para consumir dados históricos) ou o futuro (para atrasar o processamento até uma hora no futuro). Em casos quando uma tarefa foi anteriormente iniciada e parada, a opção de **hora da última paragem** está disponível para retomar a tarefa da última hora de saída e evitar a perda de dados.  

> [!NOTE]
> Quando utilizar partições, a hora da última paragem representa o mínimo de última hora de saída em todas as partições.
   
   ![Iniciar a tarefa de tempo de transmissão em fluxo](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  
   
   ![Azure tarefa de transmissão em fluxo de início tempo de portal](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  
3. Confirme a sua seleção. O estado da tarefa será alterado para *inicial* e irá mover em breve para *executar* assim que a tarefa foi iniciada. Pode monitorizar o progresso do **iniciar** operação no **Notification Hub**:
   
   ![progresso da tarefa de transmissão em fluxo](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  
   
   ![Progresso da tarefa de transmissão em fluxo de portal do Azure](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


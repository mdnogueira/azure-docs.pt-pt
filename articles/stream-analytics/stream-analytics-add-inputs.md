---
title: Adicionar uma entrada de dados para as tarefas do Stream Analytics | Microsoft Docs
description: "Saiba como ligar uma origem de dados para a tarefa de Stream Analytics como entrada de dados a partir do Event Hubs ou referência de dados do armazenamento do blogue de transmissão em fluxo."
keywords: "dados de entrada, transmissão em fluxo de dados"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: 
ms.assetid: 9e59bd24-2a80-4ecb-b6b2-309a07c70bcd
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 8bdbcf78f2892cbd1e1cc09cef220dff08dd9490
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Adicionar um transmissão em fluxo dados de entrada ou de referência de dados para uma tarefa de Stream Analytics
Saiba como ligar uma origem de dados para a tarefa de Stream Analytics como entrada de dados a partir do Event Hubs ou referência de dados do armazenamento de BLOBs de transmissão em fluxo.

Tarefas do Stream Analytics do Azure podem ser ligadas à entrada de dados de um ou mais, cada um dos quais definir uma ligação a uma origem de dados existente. Como são enviados dados para essa origem de dados, é consumido pela tarefa de Stream Analytics e processado em tempo real como dados de transmissão em fluxo. Do Stream Analytics tem integração de primeira classe com [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/) e [Blob storage do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md) dentro e fora da subscrição da tarefa.

Este artigo é um passo de [percurso de aprendizagem do Stream Analytics](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Entrada de dados: transmissão em fluxo de dados e os dados de referência
Existem dois tipos diferentes de entradas no Stream Analytics: fluxos de dados e os dados de referência.

* **Os fluxos de dados**: tarefas do Stream Analytics tem de incluir a entrada de fluxo de dados, pelo menos, um para ser consumidos e transformados pela tarefa. Armazenamento de Blobs do Azure e de Event Hubs do Azure são suportados como origens de entrada de fluxo de dados. Os Hubs de eventos do Azure são utilizados para recolher fluxos de eventos de dispositivos, serviços e aplicações ligados. Armazenamento de Blobs do Azure pode ser utilizado como uma origem de entrada para ingestão de dados em massa como uma transmissão em fluxo.  
* **Referência a dados**: Stream Analytics suporta um segundo tipo de dados de referência de chamada de entrada auxiliar.  Por oposição a dados em movimento, estes dados são estáticos ou abrandamento alterar.  Normalmente, é utilizado para efetuar look-ups e correlações com fluxos de dados para criar um conjunto mais rico de dados.  Armazenamento de Blobs do Azure está atualmente a única origem de entrada suportada para dados de referência.  

Para adicionar uma entrada para a tarefa de Stream Analytics:

1. No, clique em portal do Azure **entradas** e, em seguida, clique em **adicionar uma entrada** na sua tarefa do Stream Analytics.
   
    ![Portal clássico do Azure - adicionar uma entrada.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    No, clique em portal do Azure a **entradas** mosaico na sua tarefa do Stream Analytics.  
   
    ![Portal do Azure – adicionar a entrada de dados.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Especifique o tipo de entrada: o **fluxo de dados** ou **referência a dados**.
   
    ![Adicionar os dados corretos de entrada, transmissão em fluxo ou de referência](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Adicionar os dados corretos de entrada, transmissão em fluxo ou de referência](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. Se criar uma entrada de fluxo de dados, especifique o tipo de origem para a entrada.  Este passo pode ser ignorado durante a criação de dados de referência como apenas o Blob storage é suportado neste momento.
   
    ![Adicionar a entrada de dados de fluxo de dados](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Adicionar a entrada de dados de fluxo de dados](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Forneça um nome amigável para esta introdução na caixa de entrada Alias.  Este nome será utilizado na consulta da tarefa mais tarde para fazer referência para a entrada.
   
    Preencha o resto das propriedades de ligação necessárias para ligar a sua origem de dados. Estes campos variam consoante o tipo de entrada e de origem e estão definidos em detalhe [aqui](stream-analytics-create-a-job.md).  
   
    ![Adicionar a entrada de dados do hub de eventos](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Especifique as definições de serialização para os dados de entrada:
   
   * Para certificar-se de que as suas consultas funcionam do modo esperado, especifique o **formato de serialização de eventos** de dados recebidos.  Formatos de serialização suportados são JSON, CSV e Avro.
   * Certifique-se a **codificação** para os dados.  UTF-8 é o único formato de codificação suportado neste momento.
     
     ![Definições de serialização de dados para os dados de entrada](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Definições de serialização de dados para os dados de entrada](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. Depois de concluir a criação de entrada, o Stream Analytics irá verificar se pode ligar à origem de entrada.  Pode ver o estado da operação de ligação de teste no Notification hub.
   
    ![Teste a ligação de dados de transmissão de entrada](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Teste a ligação de dados de transmissão de entrada](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Obter ajuda com entradas de dados de transmissão em fluxo
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


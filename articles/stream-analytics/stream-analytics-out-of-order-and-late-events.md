---
title: Tratamento de ordem de eventos e lateness Azure Stream Analytics | Microsoft Docs
description: Saiba mais sobre como funciona o Stream Analytics com eventos fora de ordem ou enlace tardio em fluxos de dados.
keywords: fora de ordem, eventos
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: cf57bd12a62b3de8ac49b26ce7cdc40aec0b6738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-event-order-handling"></a>Processamento de ordem de eventos do Azure Stream Analytics

Um fluxo de dados temporal de eventos, cada evento é atribuído a um carimbo. O Azure Stream Analytics atribui timestamp para cada evento utilizando a hora da chegada ou hora da aplicação. A coluna "Timestamp" tem o timestamp atribuído ao evento. Hora da chegada é atribuída na origem de entrada quando o evento chega a origem. Hora da chegada é EventEnqueuedTime para a entrada do Hub de eventos e [blob hora da última modificação](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) para a entrada de blob. Hora da aplicação é atribuída quando o evento é gerado e faz parte do payload. Processar os eventos por hora da aplicação, utilize a cláusula "Timestamp por" a consulta select. Se a cláusula "Timestamp por" estiver ausente, são processados por hora da chegada de eventos. Hora da chegada pode ser acedida utilizando a propriedade de EventEnqueuedTime para o hub de eventos e utilizando a propriedade de BlobLastModified para a entrada de blob. O Azure Stream Analytics produz saída de ordem timestamp e fornece algumas definições para lidar com dados fora de ordem.

![Processamento de eventos do Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

Fluxos de entrada que não estão na ordem são:
* Ordenada (e, por isso **atrasada**).
* Ajustada pelo sistema, de acordo com as políticas de utilizador especificado.

Do Stream Analytics tolerates eventos de enlace tardio e fora de ordem durante o processamento por hora da aplicação.

**Tolerância de chegada dinâmico**

* Esta definição é aplicável apenas durante o processamento do tempo de aplicação, caso contrário será ignorada.
* Esta é a diferença máxima entre a hora da chegada e hora da aplicação. Se a hora da aplicação é antes (hora da chegada - tarde chegada janela), é definido como (hora da chegada - tarde chegada janela)
* Quando várias partições do mesmo fluxo de entrada ou de vários fluxos de entrada são combinados em conjunto, o enlace tardio tolerância de chegada é a quantidade máxima de tempo que cada partição espera novos dados. 

Resumidamente, a janela de chegada de enlace tardio é o máximo de atraso entre a geração de eventos e de receção do evento na origem de entrada.
Ajuste com base na tolerância de chegada de enlace tardio é efetuada pela primeira vez e fora de ordem é feito seguinte. O **Timestamp** coluna terão o final timestamp atribuído para o evento.

**Tolerância fora de ordem**

* Os eventos que chegam desordenados mas dentro do conjunto de "período de tolerância fora de ordem" são **reordenados por timestamp**. 
* Os eventos que chegam posterior tolerância são **removida ou ajustado**.
    * **Tendo em conta**: tendo em conta parece ter chegou o momento mais recente aceitável. 
    * **Removido**: rejeitados.

Para reordenar os eventos recebidos "período de tolerância fora de ordem", o resultado da consulta é **atrasada pelo período de tolerância fora de ordem**.

**Exemplo**

A tolerância de chegada enlace tardio = 10 minutos<br/>
Fora de tolerância a ordem = 3 minutos<br/>
Processamento do tempo de aplicação<br/>

Eventos:

Evento 1 _hora da aplicação_ = 00:00:00, _hora da chegada_ = 00:10:01, _Timestamp_ = 00:00:01, tendo em conta porque (_hora da chegada_  -  _Hora da aplicação_) é maior do que a tolerância de chegada enlace tardio.

Evento 2 _hora da aplicação_ = 00:00:01 _hora da chegada_ = 00:10:01, _Timestamp_ = 00:00:01, não tendo em conta porque o tempo de aplicação é entre a chegada de enlace tardio janela.

Evento 3 _hora da aplicação_ = 00:10:00, _hora da chegada_ = 00:10:02, _Timestamp_ = 00:10:00, não tendo em conta porque o tempo de appliation está dentro da janela de chegada de enlace tardio .

Evento 4 _hora da aplicação_ = 00:09:00, _hora da chegada_ = 00:10:03, _Timestamp_ = 00:09:00, aceite com original timestamp como hora da aplicação está dentro do limite de tolerância de ordem.

Evento 5 _hora da aplicação_ = 00:06:00, _hora da chegada_ = 00:10:04, _Timestamp_ = 00:07:00, tendo em conta porque a hora da aplicação é mais antiga do que o fora de ordem tolerância.



## <a name="get-help"></a>Obter ajuda
Para obter assistência adicional, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Stream Analytics](stream-analytics-introduction.md)
* [Introdução ao Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar tarefas do Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da REST API de gestão de análise de fluxo](https://msdn.microsoft.com/library/azure/dn835031.aspx)

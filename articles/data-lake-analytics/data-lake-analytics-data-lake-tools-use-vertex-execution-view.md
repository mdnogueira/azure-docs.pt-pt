---
title: "Utilize a vista de execução de vértice nas ferramentas do Data Lake para Visual Studio | Microsoft Docs"
description: "Saiba como utilizar a vista de execução de vértice para tarefas de Data Lake Analytics exam."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
ms.openlocfilehash: b788e7bc8ded86ebd49cc0be73e5b4e1bcbeaba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Utilize a vista de execução de vértice nas ferramentas do Data Lake para Visual Studio
Saiba como utilizar a vista de execução de vértice para tarefas de Data Lake Analytics exam.

## <a name="prerequisites"></a>Pré-requisitos

É necessário um conhecimento básico da utilização de ferramentas do Data Lake para Visual Studio para desenvolver scripts U-SQL.  Consulte [Tutorial: desenvolver scripts U-SQL com ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Abrir a vista de execução de vértice
Abra uma tarefa de U-SQL no Data Lake Tools para Visual Studio. Clique em **vista de execução de vértice** no canto inferior esquerdo. Poderá ser-lhe pedido para carregar perfis pela primeira vez e pode demorar algum tempo consoante a conectividade de rede.

![Vista de execução de vértice de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Compreender a vista de execução de vértice
A vista de execução de vértice tem três partes:

![Vista de execução de vértice de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

O **Seletor de vértice** no esquerda permite selecionar vértices as funcionalidades (tais como dados de 10 de principais de leitura ou optar por fase). Um dos filtros mais utilizadas normalmente é ver o **vértices no caminho crítico**. O **caminho crítico** é mais longo cadeia de vértices de uma tarefa de U-SQL. Noções sobre o caminho crítico é útil para otimizar as suas tarefas, verificando qual vértice demora o tempo mais longo.
  
![Vista de execução de vértice de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Mostra o painel de parte superior ao centro de **com o estado de todos os vértices**.
  
![Vista de execução de vértice de ferramentas do Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Painel na parte inferior central mostra informações sobre cada vertex:
* Nome do processo: O nome da instância do vértice. -Lo é composto por diferentes partes no StageName | VertexName | VertexRunInstance. Por exemplo, o vertex de .v1 [62] SV7_Split representa a segunda instância em execução (.v1, índice a partir de 0) número vértice 62 na fase SV7_Split.
* Leitura de dados total/Written: Os dados estavam a leitura/escrita por esta vértice.
* Estado de estado/saída: O estado final quando o vértice é terminado.
* Tipo de falha do código de saída: O erro quando o vértice falhou.
* Razão de criação: Por que motivo o vértice foi criado.
* Latência de fila de latência/PN de latência/processe de recursos: o tempo do vértice aguardar recursos, para processar os dados e para se manter na fila.
* GUID de processo/criador: GUID para o vértice atual em execução ou o criador.
* Versão: a N-ésimo instância do vértice em execução (o sistema pode agendar a novas instâncias de um vértice para muitos motivos, por exemplo, ativação pós-falha, computação redundância, etc.)
* Versão criada tempo.
* Processar criar início tempo/processe em fila tempo/processe início tempo/processe concluída tempo: quando o processo de vértice começa criação; Quando o processo de vértice começa a fila; Quando inicia o processo de vértice determinadas; Quando a determinados vértice está concluída.

## <a name="next-steps"></a>Passos seguintes
* Para obter informações de diagnóstico de registo, veja [Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para ver os detalhes da tarefa, consulte [Browser de tarefa de utilização e a vista de tarefas para tarefas do Azure Data lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)

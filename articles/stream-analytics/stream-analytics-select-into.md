---
title: "Depurar consultas do Azure Stream Analytics através da utilização de SELECT INTO | Microsoft Docs"
description: "Consulta de intermédio de dados de exemplo, utilizando instruções SELECT INTO no Stream Analytics"
keywords: 
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: 6ffa756eef0cfa44d7dd397e43afbf054ac2df7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="debug-queries-by-using-select-into-statements"></a>Consultas de depuração, utilizando instruções SELECT INTO

Processamento de dados em tempo real, saber aspeto os dados durante a consulta pode ser útil. Porque os passos de uma tarefa do Azure Stream Analytics ou entradas podem ser lido várias vezes, pode escrever adicionais em instruções SELECT INTO. Se o fizer, produz dados intermédios para o armazenamento e permite-lhe inspecionar a correção de dados, tal como *veja variáveis* fazer quando a depuração um programa.

## <a name="use-select-into-to-check-the-data-stream"></a>Utilize SELECT INTO para verificar o fluxo de dados

A seguinte consulta de exemplo, uma tarefa do Azure Stream Analytics tem um fluxo de entrada, duas entradas de dados de referência e um resultado ao Table Storage do Azure. A consulta associa a dados do hub de eventos e blobs de referência dois para obter as informações de nome e categoria:

![Exemplo de consulta SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Tenha em atenção que a tarefa está em execução, mas não há eventos estão a ser produzidos na saída. No **monitorização** mosaico, apresentado aqui, pode ver que a entrada é produzir dados, mas não sabe qual passo do **associar** causado todos os eventos ser removido.

![O mosaico de monitorização](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
Nesta situação, pode adicionar alguns adicionais em instruções SELECT INTO "Iniciar" os resultados de associação intermédios e os dados que é lida a entrada.

Neste exemplo, adicionámos dois novas "temporárias saídas." Podem ser qualquer sink que pretender. Aqui, utilizamos Storage do Azure como um exemplo:

![Adicionar adicionais em instruções SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Em seguida, possam reescrever a consulta seguinte:

![Consulta de conversão SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Agora iniciar novamente a tarefa e permitir que o mesmo executar alguns minutos. Em seguida, consultar temp1 e temp2 com o Visual Studio Cloud Explorer para produzir as tabelas seguintes:

**tabela de temp1**
![SELECT INTO temp1 tabela](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabela de temp2**
![SELECT INTO temp2 tabela](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Como pode ver, temp1 e temp2 têm dados e a coluna de nome é preenchida corretamente no temp2. No entanto, porque não existe ainda não foram encontrados dados no resultado, algo está errado:

![SELECT INTO output1 tabela sem dados](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Através da amostragem os dados, pode ter praticamente determinadas que o problema está na associação a um segundo. Pode transferir os dados de referência de blob e veja:

![SELECT INTO tabela de referência](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Como pode ver, o formato do GUID nestes dados de referência é diferente do formato de [coluna no temp2 de]. É por esse motivo os dados não chegam a output1 conforme esperado.

Pode corrigir o formato dos dados, carregá-lo a referência de blob e tente novamente:

![SELECT INTO tabela temporária](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Neste momento, os dados de saída são formatados e preenchidos conforme esperado.

![SELECT INTO final tabela](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


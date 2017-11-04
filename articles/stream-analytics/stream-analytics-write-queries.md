---
title: Como escrever consultas no Stream Analytics | Microsoft Docs
description: Consultas de escrita no Stream Analytics e consultar dados | Learning segmento de caminho.
keywords: como escrever consultas, dados de consulta, escrever uma consulta, escrever consultas
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 0e9cdadd-0ee0-4bee-b65b-4a06fb863c95
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 215b774c20d80a67b1cefa2634131bd44860c692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-write-queries-in-stream-analytics"></a>Como escrever consultas no Stream Analytics
Escrever consultas para fluxo processar lógica no Azure Stream Analytics é implementado como uma "consulta colocado" que está definida antes de uma tarefa é iniciado e executados nos dados como chegar a tarefa. A transformação de dados é expresso num idioma de consulta como o SQL Server, que é amplamente um subconjunto de T-SQL com algumas adicionado extensões de idioma, como [modos de janela](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilizado para expressar semântica temporal.

## <a name="writing-queries"></a>Escrever consultas:
1. Na sua tarefa do Stream Analytics no portal do Azure, clique em **consulta**.
   
    ![Selecione a consulta](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  
   
    No portal do Azure, clique em **consulta**.
   
    ![Selecione a pré-visualização da consulta](./media/stream-analytics-write-queries/query-preview-portal.png)  
2. As novas tarefas têm um modelo de consulta para ajudar a começar. O modelo de consulta executa uma consulta de "pass-through" projetos todos os campos de eventos de entrada para a saída.  
   
   * Se tiver definido, pelo menos, uma entrada e saída para a sua tarefa, pode substituir o marcador de posição "[YourOutputAlias]" e "[YourInputAlias]" campos com os aliases de entrada e de saída que pretende utilizam pela primeira vez. Além disso, pode ainda criar e testar a consulta no Portal clássico do Azure sem definir entradas e saídas na tarefa.
   * Se pretender efetuar o processamento de mais de um pass-through simple, pode editar a definição de consulta. Para começar a utilizar com a criação de consulta, observe alguns são capturados padrões de consulta comuns [aqui](stream-analytics-stream-analytics-query-patterns.md).  
   
   ![Consultar dados janela](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Para validar dados de consulta está a funcionar:
Pode testar a sua consulta se comporta conforme esperado executando-a no browser ao longo de um ou mais locais ficheiros JSON que contém dados de teste. Isto não irá iniciar a tarefa ou apto a faturação.

> [!NOTE]
> Atualmente o teste de consulta no browser não é suportado no portal do Azure.  
> 
> 

1. Certifique-se de que existem não existem erros na consulta (caso contrário, o botão de teste será desativado) e, em seguida, clique no botão de teste.  
   
   ![Consultar dados de teste](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  
2. Lhe for pedido para especificar os ficheiros para cada uma das entradas referenciadas na consulta. Neste exemplo, a consulta de modelo for deixada como-é, pelo que a caixa de diálogo está a pedir para uma entrada com o nome "yourinputalias".
   
   ![Consulta de dados de teste](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  
3. Procure um ficheiro de teste. Vários ficheiros de exemplo está disponível no [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) e também pode obter dados de exemplo de entradas de fluxo os seus dados através da função de dados de exemplo no separador de entradas.
   
   ![Entrada de consulta](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  
4. Depois de fechar a caixa de diálogo, a consulta será executada sobre os dados de teste e verá os resultados na parte inferior da página de consulta.
   
   ![Resumo de consulta](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


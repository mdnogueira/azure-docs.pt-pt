---
title: "Guia de resolução de problemas para o Azure Stream Analytics | Microsoft Docs"
description: Como resolver problemas com a tarefa de Stream Analytics
keywords: "Guia de resolução de problemas"
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
ms.openlocfilehash: dcff312e4a282b15e76ea32aadb1981a496a2446
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Guia de resolução de problemas para o Azure Stream Analytics

Resolução de problemas de Stream Analytics do Azure pode parece estar um esforço complexo à primeira vista. Depois de trabalharem com muitos utilizadores, foi criado neste guia para ajudar a simplificar o processo e remover guesswork sobre as entradas, saídas, consultas e funções.

## <a name="troubleshoot-your-stream-analytics-job"></a>Resolver problemas relacionados com a tarefa de Stream Analytics

Para obter os melhores resultados na sua tarefa do Stream Analytics de resolução de problemas, utilize as seguintes diretrizes:

1.  Teste a conectividade:
    - Verifique a conectividade às entradas e saídas, utilizando o **Testar ligação** botão para cada entrada e saída.

2.  Examine os dados de entrada:
    - Para verificar que os dados de entrada é fluir para o Hub de eventos, utilize [Explorador do Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) para ligar ao Hub de eventos do Azure (se a entrada do Hub de eventos é utilizada).  
    - Utilize o [ **dados de exemplo** ](stream-analytics-sample-data-input.md) botão para cada entrada e transferir os dados de entrada de exemplo.
    - Inspecione os dados de exemplo para compreender a forma dos dados: o esquema e o [tipos de dados](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  A consulta de teste:
    - No **consulta** separador, utilize o **testar** botão para testar a consulta e utilize os dados de exemplo transferido para [testar a consulta](stream-analytics-test-query.md). Examine os erros e tentar corrigir as entradas.
    - Se utilizar [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), certifique-se de que os eventos tenham carimbos maiores do que o [hora de início da tarefa](stream-analytics-out-of-order-and-late-events.md).

4.  A consulta de depuração:
    - Reconstrua a consulta progressivamente de instrução select simple para agregados mais complexas, utilizando os passos. Para criar cópias de segurança a lógica de consulta passo a passo, utilize [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) cláusulas.
    - Utilize [SELECT INTO](stream-analytics-select-into.md) passos da consulta de depuração.

5.  Eliminar pitfalls comuns, tais como:
    - A [ **onde** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) cláusula FROM na consulta filtrados por todos os eventos, impedir que quaisquer dados que está a ser gerado.
    - Quando utiliza as funções de janela, aguarde que a duração de janela completo ver um resultado da consulta.
    - Timestamp eventos precede a hora de início da tarefa e, por conseguinte, os eventos estão a ser ignorados.

6.  Utilize a ordenação de evento:
    - Se todos os passos anteriores trabalhou ajustar, vá para o **definições** painel e selecione [ **eventos ordenação**](stream-analytics-out-of-order-and-late-events.md). Certifique-se de que esta política está configurada para o que faz sentido no seu cenário. A política é *não* aplicada ao utilizar o **testar** botão para testar a consulta. Este resultado é uma diferença entre no browser versus executar a tarefa de produção de teste.

7.  Depurar utilizando métricas:
    - Se nenhuma saída é obter após a duração esperada (com base na consulta), experimente o seguinte:
        - Observe [ **monitorização métricas** ](stream-analytics-monitoring.md) no **Monitor** separador. Porque os valores são agregados, as métricas atrasadas por alguns minutos.
            - Se os eventos de entrada > 0, a tarefa é possível ler dados de entrada. Se os eventos de entrada não é > 0, em seguida:
                - Para ver se a origem de dados tem dados válidos, consulte-lo utilizando [Explorador do Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Esta verificação aplica-se da tarefa está a utilizar o Hub de eventos como entrada.
                - Verifique se o formato de serialização de dados e, em seguida, a codificação de dados estão como esperado.
                - Se a tarefa está a utilizar um Hub de eventos, verifique se o corpo da mensagem está *nulo*.
            - Se os erros de conversão de dados > 0 e climbing, o seguinte poderá ser verdadeiro:
                - A tarefa não poderá anular a serialização de eventos.
                - O esquema de eventos poderá não corresponder o esquema definido ou é esperado dos eventos na consulta.
                - Os tipos de dados de alguns dos campos de eventos poderão não corresponder às expectativas do.
            - Se os erros de Runtime > 0, significa que a tarefa pode receber dados, mas está a gerar erros ao processar a consulta.
                - Para localizar os erros, visite o [registos de auditoria](../azure-resource-manager/resource-group-audit.md) e filtre *falha* estado.
            - Se InputEvents > 0 e OutputEvents = 0, significa que uma das seguintes opções for verdadeira:
                - O processamento da consulta resultou em zero eventos de saída.
                - Os campos ou eventos poderão estar incorreto, resultando na saída do zero após o processamento de consulta.
                - A tarefa não conseguiu para enviar dados para o [sink de saída](stream-analytics-select-into.md) por motivos de autenticação ou de conectividade.
        - Em todos os casos de erro mencionadas anteriormente, as mensagens do registo de operações explicam detalhes adicionais (incluindo o que acontece), exceto nos casos em que a lógica de consulta filtrada todos os eventos. Se o processamento de vários eventos gera erros, o Stream Analytics regista as mensagens de três erro primeiro o mesmo tipo de 10 minutos para os registos de operações. -Lo, em seguida, suprime erros idênticos adicionais com uma mensagem que lê "Erros estão a ocorrer demasiado rapidamente, que estes estão a ser suprimidos."

8. Depurar através da utilização de auditoria e registos de diagnóstico:
    - Utilize [registos de auditoria](../azure-resource-manager/resource-group-audit.md)e filtrar para identificar e erros de depuração.
    - Utilize [os registos de diagnóstico da tarefa](stream-analytics-job-diagnostic-logs.md) para identificar e erros de depuração.

9. Examine as saídas:
    - Quando o estado da tarefa é *executar*, consoante a duração stipulated da consulta, pode ver o resultado da origem de dados de sink.
    - Se não forem apresentadas saídas que vão para um tipo de saída específico, redirecioná-los para um tipo de saída é menos complexo, tais como um Blob do Azure. Ao utilizar o Explorador de armazenamento, verifique se a saída pode ser vista. Além disso, verifique se os limites de limitação na saída estão a impedir dados a ser recebidos.

10. Utilize a análise de fluxo de dados com a métrica de diagrama de tarefa:
    - Para analisar o fluxo de dados e identificar problemas, utilize o [diagrama de tarefa com a métrica](stream-analytics-job-diagram-with-metrics.md).

11. Abra um incidente de suporte:
    - Por fim, se todos os resultarem, abra um incidente de suporte da Microsoft, utilizando o SubscriptionID que contém a tarefa.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

---
title: "Como resolver problemas de uma tarefa periódica anormal | Microsoft Docs"
description: "Saiba como utilizar o Azure Data Lake Tools para Visual Studio para depurar uma tarefa periódica anormal."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>Como resolver problemas de uma tarefa periódica anormal

Neste documento, introduzirmos como utilizar [ferramentas do Azure Data Lake para Visual Studio](http://aka.ms/adltoolsvs) para resolver problemas de tarefa periódica. Saiba mais sobre o pipeline e tarefas periódicas a partir do [aqui](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).
Trabalhos recorrentes, normalmente, partilham a mesma consulta lógica e os dados de entrada semelhantes. Por exemplo, tem uma tarefa periódica em execução em cada segunda-feira de manhã em 8 A.M. a contagem de utilizador do último semana semanal Active Directory, os scripts para estas tarefas partilham um modelo de script que contém a lógica de consulta e as entradas para estas tarefas são os dados de utilização da última semana. Partilha a mesma lógica de consulta e semelhante de entrada normalmente significa desempenho destas tarefas é semelhante e estável, se um dos seus trabalhos recorrentes efetua subitamente anormal, falhas ou lentidão dos muito, poderá querer:

1.  Consulte os relatórios de estatísticas para as execuções de pré-visualizações da tarefa periódica para ver o que aconteceu.
2.  Compare o trabalho anormal com um normal para descobrir o que foi alterado.

**Relacionadas com a vista de tarefas** nas ferramentas do Azure Data Lake para Visual Studio ajuda pode acelerar o progresso de resolução de problemas com ambos os casos.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Passo 1: Localize trabalhos recorrentes e abra a vista de tarefas relacionadas

Para utilizar a tarefa relacionada vista resolver o problema recorrente de tarefa, tem de primeiro localizar a tarefa periódica no Visual Studio e, em seguida, abra a vista de tarefas relacionadas.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Cenário 1: Tem o URL para a tarefa periódica

Através de **ferramentas > Data Lake > Vista de tarefas**, pode colar o URL de tarefa para abrir a vista de tarefa no Visual Studio e através da vista de tarefas relacionadas para abrir relacionadas com a vista de tarefas.

![As ferramentas do Data Lake Analytics ver tarefas relacionadas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Caso 2: Tem o pipeline para a tarefa periódica, mas não o URL

No Visual Studio, pode abrir Pipeline Browser através de **Explorador de servidores > a conta de Data Lake Analytics > Pipelines** (se não é possível localizar este nó no Explorador de servidores, obtenha a ferramenta lasted [aqui](http://aka.ms/adltoolsvs)). Num Browser de Pipeline, todos os pipelines para a conta ADLA estão listados no lado esquerdo, pode expandir pipelines para localizar todos os trabalhos recorrentes, clique em que tem problemas, a vista de tarefas relacionadas abre no lado direito.

![As ferramentas do Data Lake Analytics ver tarefas relacionadas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![As ferramentas do Data Lake Analytics ver tarefas relacionadas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>Passo 2: Analisar o relatório de estatísticas

Um resumo e um relatório de estatísticas são apresentados na parte superior da vista de tarefas relacionadas, através do qual pode obter a potencial causa de raiz do anormal. 

1.  Primeiro tem de localizar a tarefa anormal no relatório. O eixo X mostra o tempo de submissão da tarefa, através do qual possam localizar a tarefa anormal.
2.  Siga abaixo processo para verificar as estatísticas e obter as informações do anormal e possíveis soluções.

![As ferramentas do Data Lake Analytics ver tarefas relacionadas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>Passo 3: Comparar a tarefa periódica anormal para uma tarefa normal

Pode encontrar submetido todos os trabalhos recorrentes através da lista de tarefas na parte inferior da vista de tarefa relacionados. Através do clique direito pode comparar a tarefa anormal com um normal anterior para obter mais informações e possíveis soluções na vista de diferença de tarefa.

![As ferramentas do Data Lake Analytics ver tarefas relacionadas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Normalmente, terá de prestar atenção às diferenças entre estes 2 tarefas grandes à medida que são provavelmente os motivos pelos quais causar problemas de desempenho, também pode seguir passos abaixo para fazer uma verificação adicional.

![As ferramentas do Data Lake Analytics ver tarefas relacionadas](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Passos seguintes

* [Como depurar e resolver problemas de dissimetrias de dados](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Como depurar a falha da tarefa U-SQL para erro de código definido pelo utilizador](data-lake-analytics-debug-u-sql-jobs.md)
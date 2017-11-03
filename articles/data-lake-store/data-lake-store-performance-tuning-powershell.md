---
title: "Desempenho de otimização de orientações para a utilização do Powershell com o Data Lake Store | Microsoft Docs"
description: "Sugestões sobre como melhorar o desempenho quando utilizar o Azure PowerShell com o Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/30/2017
ms.author: nitinme
ms.openlocfilehash: 49404c7df6423a20c71347e4a764d5626110310e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Orientações para a utilização do PowerShell do Azure Data Lake Store de otimização do desempenho

Este artigo apresenta as propriedades que podem ser otimizadas para obter um melhor desempenho ao utilizar o PowerShell para trabalhar com o Data Lake Store:

## <a name="performance-related-properties"></a>Propriedades relacionadas com o desempenho

| Propriedade            | Predefinição | Descrição |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Este parâmetro permite-lhe escolher o número de threads paralelos para carregar ou transferir cada ficheiro. Este número representa o máximo de threads que podem ser atribuído por ficheiro, mas poderá ser menos threads dependendo do seu cenário (por exemplo, se estiver a carregar um ficheiro de 1 KB, receberá um thread, mesmo se a peça para 20 threads).  |
| ConcurrentFileCount | 10      | Este parâmetro destina-se especificamente ao carregamento ou transferência de pastas. Este parâmetro determina o número de ficheiros simultâneos que podem ser carregados ou transferidos. Este número representa o número máximo de ficheiros em simultâneo que podem ser carregado ou transferida em simultâneo, mas poderá ser menos simultaneidade dependendo do seu cenário (por exemplo, se estiver a carregar dois ficheiros, depara-se dois carregamentos de ficheiros em simultâneo, mesmo se a peça para 15). |

**Exemplo**

Este comando transfere ficheiros do Azure Data Lake Store para a unidade local do utilizador, com 20 threads por ficheiro e 100 ficheiros simultâneos.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Como posso determinar o valor para estas propriedades?

A questão seguinte que poderá ser necessário é como determinar o valor para fornecer as propriedades relacionadas com o desempenho. Eis algumas orientações que poderá utilizar.

* **Passo 1: Determine a contagem total de threads** - Deve começar por calcular a contagem total de threads a utilizar. Como orientação geral, deve utilizar seis threads para cada núcleos físicos.

        Total thread count = total physical cores * 6

    **Exemplo**

    Partindo do princípio de que está a executar os comandos do PowerShell a partir de uma VM D14 que tem 16 núcleos

        Total thread count = 16 cores * 6 = 96 threads


* **Passo 2: Calcular a PerFileThreadCount** - Calculamos a nossa PerFileThreadCount com base no tamanho dos ficheiros. Para ficheiros inferior 2,5 GB, não é necessário para alterar este parâmetro, porque a predefinição de 10 é suficiente. Para ficheiros maiores do que 2,5 GB, deverá utilizar 10 threads como base para o primeira 2,5 GB e adicionar 1 thread para cada aumento adicional de 256 MB de tamanho de ficheiro. Se estiver a copiar uma pasta com uma grande variedade de tamanhos de ficheiros, considere agrupá-los em tamanhos de ficheiro semelhantes. Ter tamanhos de ficheiro diferentes pode causar um desempenho não ideal. Se não for possível agrupar tamanhos de ficheiros semelhantes, deve definir PerFileThreadCount com base no tamanho de ficheiro maior.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Exemplo**

    Partindo do princípio que tem 100 ficheiros entre 1 GB e 10 GB, utilizamos o 10 GB dado que o tamanho de ficheiro maior de equação, que deverá ler como o seguinte.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Passo 3: Calcular ConcurrentFilecount** - utiliza a contagem total de threads e PerFileThreadCount para calcular ConcurrentFileCount baseado na equação a seguinte:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Exemplo**

    Com base nos valores de exemplo que temos estado a utilizar

        96 = 40 * ConcurrentFileCount

    Por isso, **ConcurrentFileCount** é **2,4**, que podemos arredondar para **2**.

## <a name="further-tuning"></a>Otimização adicional

Poderá necessitar de otimização adicional porque existe uma grande variedade de tamanhos de ficheiro com os quais trabalhar. O cálculo anterior funciona bem se todas ou mais dos ficheiros são maiores e mais próximo do intervalo de 10 GB. Se, em vez disso, existirem vários tamanhos de ficheiro diferentes com muitos ficheiros mais pequenos, poderia reduzir a PerFileThreadCount. Ao reduzir a PerFileThreadCount, podemos aumentar a ConcurrentFileCount. Por isso, se partimos do princípio que a maioria dos nossos ficheiros são mais pequena no intervalo de 5 GB, iremos pode refazer nosso cálculo:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Por isso, **ConcurrentFileCount** fica 96/20, que é 4.8, arredondada para **4**.

Pode continuar a otimizar estas definições, alterando a **PerFileThreadCount** para cima e para baixo consoante a distribuição dos tamanhos de ficheiro.

### <a name="limitation"></a>Limitação

* **O número de ficheiros é menor que ConcurrentFileCount**: se o número de ficheiros que está a carregar for menor que a **ConcurrentFileCount** que calculou, deve reduzir a **ConcurrentFileCount** para ser igual ao número de ficheiros. Pode utilizar quaisquer threads restantes para aumentar a **PerFileThreadCount**.

* **Existem demasiados threads**: se aumentar demasiado a contagem de threads sem aumentar o tamanho do cluster, corre o risco de degradar o desempenho. Podem existir problemas de contenção ao alternar de contexto na CPU.

* **Simultaneidade insuficiente**: se a simultaneidade não for suficiente, significa que o cluster pode ser demasiado pequeno. Pode aumentar o número de nós do cluster, o que lhe dá a simultaneidade mais.

* **Erros de limitação**: poderá ver erros de limitação se a simultaneidade for demasiado elevada. Se vir erros de limitação, deverá reduzir a simultaneidade ou contactar a Microsoft.

## <a name="next-steps"></a>Passos seguintes
* [Utilizar o Azure Data Lake Store para requisitos de macrodados](data-lake-store-data-scenarios.md) 
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)


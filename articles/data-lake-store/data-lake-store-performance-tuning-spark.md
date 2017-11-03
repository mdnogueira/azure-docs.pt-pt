---
title: "Otimização diretrizes de Spark do desempenho do Azure Data Lake Store | Microsoft Docs"
description: "Otimização diretrizes de Spark do desempenho do Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 2109744fb7ffdfafb7a86bbea355e119718af099
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>Orientações para o Spark no HDInsight e o Azure Data Lake Store de otimização do desempenho

Quando a otimização de desempenho no Spark, terá de considerar o número de aplicações que será executado no seu cluster.  Por predefinição, pode executar 4 aplicações em simultâneo no HDI cluster (Nota: a predefinição está sujeita a alterações).  Pode optar por utilizar menos aplicações para que possa substituem as predefinições e utilizar mais do cluster para essas aplicações.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster do HDInsight Azure** com acesso a uma conta de Data Lake Store. Consulte [criar um cluster do HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.
* **Executar cluster do Spark no Azure Data Lake Store**.  Para obter mais informações, consulte [cluster utilizar o Spark do HDInsight para analisar os dados no Data Lake Store](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Diretrizes no ADLS de otimização do desempenho**.  Para os conceitos gerais de desempenho, consulte [guia de otimização de desempenho do Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Parâmetros

Quando as tarefas em execução Spark, seguem-se as definições mais importantes que podem ser otimizadas para melhorar o desempenho ADLS:

* **Núm executores** -o número de tarefas simultâneas que podem ser executadas.

* **Memória de executor** -a quantidade de memória atribuída a cada executor.

* **Núcleos de executor** -o número de núcleos alocados a cada executor.                     

**Núm executores** núm executores irão definir o número máximo de tarefas que podem ser executadas em paralelo.  O número real de tarefas que podem ser executadas em paralelo é tem um vínculo pela memória e recursos de CPU disponíveis no seu cluster.

**Memória de executor** esta é a quantidade de memória que está a ser atribuída a cada executor.  A memória necessária para cada executor está dependente da tarefa.  Para operações complexas, a memória tem de ser superior.  Para operações simples, como leitura e escrita, os requisitos de memória será inferiores.  A quantidade de memória para cada executor pode ser visualizada no Ambari.  Ambari, navegue para o Spark e ver o separador de folhas.  

**Núcleos de executor** Isto define a quantidade de núcleos utilizado por executor, que determina o número de threads paralelas que podem ser executadas por executor.  Por exemplo, se executor núcleos = 2, execute cada executor pode 2 tarefas paralelas no executor.  Os núcleos de executor necessitam estará dependentes na tarefa.  Tarefas de intensa de e/s não requerem uma grande quantidade de memória por tarefas, para que cada executor pode processar mais tarefas paralelas.

Por predefinição, os dois núcleos YARN virtuais são definidos para cada núcleos físicos quando executar o Spark no HDInsight.  Este número fornece um boa equilíbrio de concurrecy e a quantidade de contexto mudar de vários threads.  

## <a name="guidance"></a>Orientação

Ao executar o Spark cargas de trabalho analíticas para trabalhar com dados no Data Lake Store, recomendamos que utilize a versão mais recente do HDInsight para obter o melhor desempenho com o Data Lake Store. Quando a tarefa mais intensiva da e/s, determinados parâmetros podem ser configurados para melhorar o desempenho.  O Azure Data Lake Store é uma plataforma de armazenamento altamente dimensionável, que pode processar o débito elevado.  Se a tarefa consiste principalmente de leitura ou escrita, em seguida, aumentar a simultaneidade para e/s de e para o Azure Data Lake Store pode aumentar o desempenho.

Existem algumas formas gerais para aumentar a simultaneidade de tarefas de e/s intensivas.

**Passo 1: Determinar quantas aplicações estão em execução no seu cluster** – que deve saber como muitas aplicações estão em execução no cluster, incluindo o atual.  Os valores predefinidos para cada Spark definição assume que existem 4 aplicações em execução em simultâneo.  Por conseguinte, só tem 25% de cluster disponível para cada aplicação.  Para obter um melhor desempenho, pode substituir as predefinições, alterar o número de executor.  

**Passo 2: Definir a memória de executor** – a primeira coisa que definir é a memória de executor.  A memória será dependente da tarefa que vai executar.  Pode aumentar a simultaneidade atribuindo menos memória por executor.  Se vir fora de exceções de memória quando executar a tarefa, deve aumentar o valor para este parâmetro.  É uma alternativa para obter mais memória ao utilizar um cluster com superiores quantidades de memória ou aumentar o tamanho do cluster.  Mais memória permitirá executores mais ser utilizado, que significa mais concorrência.

**Passo 3: Definir o executor núcleos** – para e/s intensivas cargas de trabalho que não tenham operações complexas, está pronto para começar com um elevado número de núcleos de executor para aumentar o número de tarefas paralelas por executor.  A definição de núcleos de executor para 4 é um bom ponto de partida.   

    executor-cores = 4
Aumento do número de núcleos de executor irá dar-lhe mais paralelismo pelo que pode experimentar diferentes de núcleos de executor.  Para tarefas que têm operações mais complexas, deve reduzir o número de núcleos por executor.  Se executor núcleos for definido como superior a 4, em seguida, recolha de lixo pode tornar-se ineficiente e degradar o desempenho.

**Passo 4: Determinar a quantidade de memória YARN em cluster** – esta informação está disponível no Ambari.  Navegue para YARN e ver o separador de folhas.  A memória YARN é apresentada nesta janela.  
Nota: Apesar de estarem na janela, também pode ver o tamanho de contentor predefinido YARN.  O tamanho do contentor YARN é a mesma memória por executor parâmetro.

    Total YARN memory = nodes * YARN memory per node
**Passo 5: Calcular núm executores**

**Calcular a restrição de memória** -o parâmetro de executor núm é limitado pela memória ou por da CPU.  A restrição de memória é determinada pela quantidade de memória YARN disponível para a sua aplicação.  Deve tirar total da memória YARN e dividir que por executor de memória.  A restrição tem de ser anular expandido para o número de aplicações, pelo que iremos dividir por número de aplicações.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Calcular a restrição de CPU** -restrição a CPU é calculada como os núcleos virtuais totais divididos pelo número de núcleos por executor.  Existem 2 núcleos virtuais para cada núcleos físicos.  Semelhante para a restrição de memória, temos dividir por número de aplicações.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Definir o executor núm** – o parâmetro de executor núm é determinado pelo tirar o mínimo de memória e a restrição de CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
A definição de um número mais elevado de executor núm não necessariamente aumentar o desempenho.  Deve considerar que ao adicionar mais executores adicionar extra gerais para cada executor adicional, que potencialmente pode degradar o desempenho.  Núm executores é tem um vínculo pelos recursos de cluster.    

## <a name="example-calculation"></a>Cálculo de exemplo

Vamos supor que tem atualmente um é composto por 8 D4v2 nós de cluster que executa 2 aplicações, incluindo o vai executar.  

**Passo 1: Determinar quantas aplicações estão em execução no seu cluster** – sabe que tiver 2 aplicações no seu cluster, incluindo o vai executar.  

**Passo 2: Definir a memória de executor** – para este exemplo, podemos determinar que 6 GB de memória de executor serão suficiente para a tarefa intensiva da e/s.  

    executor-memory = 6GB
**Passo 3: Definir o executor núcleos** – uma vez que esta é uma tarefa intensiva da e/s, pode definir o número de núcleos para cada executor para 4.  Definição de núcleos por executor como superior a 4 pode causar problemas de recolha de lixo.  

    executor-cores = 4
**Passo 4: Determinar a quantidade de memória YARN em cluster** –, navegue para Ambari para saber que cada D4v2 tem 25 GB de memória YARN.  Uma vez que existem 8 nós, a memória disponível do YARN é multiplicada por 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Passo 5: Calcular núm executores** – o parâmetro de executor núm é determinado pelo tirar o mínimo de restrição de memória e a restrição de CPU dividido por # de aplicações em execução no Spark.    

**Calcular a restrição de memória** – a restrição de memória é calculada como a memória total do YARN dividida pela memória por executor.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Calcular a restrição de CPU** -restrição a CPU é calculada como os núcleos total yarn divididos pelo número de núcleos por executor.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Conjunto segui-executores**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    


---
title: "Otimização diretrizes de MapReduce do desempenho do Azure Data Lake Store | Microsoft Docs"
description: "Otimização diretrizes de MapReduce do desempenho do Azure Data Lake Store"
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
ms.openlocfilehash: 9528148792f083cb0e48d356e61cf61762ee954f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Orientações para MapReduce no HDInsight e o Azure Data Lake Store de otimização do desempenho


## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster do HDInsight Azure** com acesso a uma conta de Data Lake Store. Consulte [criar um cluster do HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.
* **Utilizar o MapReduce no HDInsight**.  Para obter mais informações, consulte [MapReduce de utilização no Hadoop no HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-mapreduce)  
* **Diretrizes no ADLS de otimização do desempenho**.  Para os conceitos gerais de desempenho, consulte [guia de otimização de desempenho do Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

## <a name="parameters"></a>Parâmetros

Quando executar tarefas de MapReduce, seguem-se os parâmetros mais importantes que pode configurar para melhorar o desempenho ADLS:

* **Mapreduce.Map.Memory.MB** – a quantidade de memória a atribuir a cada mapeador
* **Mapreduce.job.Maps** – o número de tarefas de mapa por tarefa
* **Mapreduce.reduce.Memory.MB** – a quantidade de memória a atribuir a cada reducer
* **Mapreduce.job.reduces** – o número de tarefas reduza por tarefa

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** este número deve ser ajustada com base na quantidade de memória necessária para o mapa de e/ou reduzir a tarefa.  Os valores predefinidos de mapreduce.map.memory e mapreduce.reduce.memory podem ser visualizados no Ambari através da configuração de Yarn.  Ambari, navegue para YARN e ver o separador de folhas.  A memória YARN será apresentada.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** Isto irá determinar o número máximo de mappers ou reducers a ser criado.  O número de divisões irá determinar quantos mappers serão criadas para a tarefa de MapReduce.  Por conseguinte, pode obter menos mappers que pediu se existem menos divisões que o número de mappers pedida.       

## <a name="guidance"></a>Orientação

**Passo 1: Determinar o número de tarefas em execução** -por predefinição, o MapReduce utilizará o cluster completo para a tarefa.  Pode utilizar o menor de cluster utilizando menos mappers que existem contentores disponíveis.  As orientações neste documento parte do princípio de que a aplicação é a aplicação apenas em execução no seu cluster.      

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory** – o tamanho da memória para mapa e reduzir as tarefas serão dependentes na sua tarefa específica.  Pode reduzir o tamanho de memória se pretende aumentar a simultaneidade.  O número de tarefas em execução em simultâneo depende o número de contentores.  Ao diminuir a quantidade de memória por mapeador ou reducer, podem ser criadas mais contentores, que ativar mais mappers ou reducers a ser executados em simultâneo.  Diminua a quantidade de memória muito pode causar alguns processos ficar com memória esgotada.  Se obtiver um erro de área dinâmica para dados ao executar a tarefa, deve aumentar a memória por mapeador ou reducer.  Deve considerar que ao adicionar mais contentores adicionar extra gerais para cada contentor adicional, que potencialmente pode degradar o desempenho.  Outra alternativa é para obter mais memória ao utilizar um cluster com superiores quantidades de memória ou aumentar o número de nós do cluster.  Mais memória irá ativar mais contentores ser utilizado, o que significa mais concorrência.  

**Passo 3: Determinar a memória Total YARN** - para otimizar mapreduce.job.maps/mapreduce.job.reduces, deve considerar a quantidade de memória YARN total disponível para utilização.  Esta informação está disponível no Ambari.  Navegue para YARN e ver o separador de folhas.  A memória YARN é apresentada nesta janela.  Deve multiplicar a memória do YARN com o número de nós do cluster para obter a memória total do YARN.

    Total YARN memory = nodes * YARN memory per node
Se estiver a utilizar um cluster vazio, memória pode ser o total de memória YARN para o cluster.  Se outras aplicações estão a utilizar memória, em seguida, pode optar por utilizar apenas uma parte da memória do seu cluster ao reduzir o número de mappers ou reducers ao número de contentores que pretende utilizar.  

**Passo 4: Calcular o número de contentores YARN** – contentores YARN que estipulam a quantidade de concorrência disponível para a tarefa.  Tirar total da memória YARN e que de divisão por mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces** definido mapreduce.job.maps/mapreduce.job.reduces para, pelo menos, o número de contentores disponíveis.  Pode experimentar mais aumento do número de mappers e reducers para ver se consegue obter um melhor desempenho.  Tenha em atenção que mappers mais terão overhead adicional para que ter demasiadas mappers pode degradar o desempenho.  

CPU de agendamento e o isolamento de CPU estão desativadas por predefinição, para que o número de contentores YARN é limitado pela memória.

## <a name="example-calculation"></a>Cálculo de exemplo

Vamos supor que tem atualmente um é composto por 8 D14 nós de cluster e que pretende executar uma tarefa intensiva da e/s.  Seguem-se a cálculos que deve fazer:

**Passo 1: Determinar o número de tarefas em execução** -para o nosso exemplo, partimos do pressuposto que o nosso tarefa está a executar apenas um.  

**Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory** – para o nosso exemplo, estiver a executar uma tarefa intensiva de i / e decidir que 3 GB de memória para tarefas de mapa serão suficiente.

    mapreduce.map.memory = 3GB
**Passo 3: Determinar a memória de Total YARN**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Passo 4: Calcular n. º de contentores YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Passo 5: Definir mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Limitações

**ADLS limitação**

Como um serviço de multi-inquilino, ADLS define limites de largura de banda ao nível da conta.  Se clicar nestes limites, irá começar a ver falhas de tarefas. Isto pode ser identificado ao observar erros limitação nos registos de tarefas.  Se precisar de mais largura de banda para a sua tarefa, contacte-nos.   

Para verificar se a introdução limitada, tem de ativar a depuração de registo do lado do cliente. Eis como, pode fazê-lo:

1. Colocar a seguinte propriedade nas propriedades de log4j na Ambari > YARN > configuração > Avançadas yarn log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Reinicie todos os nós/serviço de configuração entrem em vigor.

3. Se a introdução limitada, verá o código de erro HTTP 429 no ficheiro de registo YARN. O ficheiro de registo YARN consta /tmp/&lt;utilizador&gt;/yarn.log

## <a name="examples-to-run"></a>Exemplos para ser executado

Para demonstrar como MapReduce é executada no Azure Data Lake Store, segue-se algum código de exemplo que foi executado num cluster com as seguintes definições:

* nó 16 D14v2
* Cluster de Hadoop com HDI 3.6

Para um ponto de partida, eis alguns comandos de exemplo para executar MapReduce Teragen, Terasort e Teravalidate.  Pode ajustar estes comandos com base nos seus recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate

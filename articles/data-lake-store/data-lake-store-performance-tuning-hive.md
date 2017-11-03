---
title: "Otimização diretrizes de ramo de registo do desempenho do Azure Data Lake Store | Microsoft Docs"
description: "Desempenho de ramo de registo do Azure Data Lake Store diretrizes de otimização"
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
ms.openlocfilehash: e10bf8f7cbae2b81d22823ff74fe652c6bcb2da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Orientações para do Hive no HDInsight e o Azure Data Lake Store de otimização do desempenho

As predefinições foram definidas para oferecer um bom desempenho em muitos casos de utilização diferentes.  Para consultas de e/s intensivas, o ramo de registo pode ser otimizado para obter um melhor desempenho com ADLS.  

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster do HDInsight Azure** com acesso a uma conta de Data Lake Store. Consulte [criar um cluster do HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.
* **Em execução do Hive no HDInsight**.  Para saber mais sobre como executar tarefas do Hive no HDInsight, consulte [utilizar o Hive no HDInsight] (https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-hive)
* **Diretrizes no ADLS de otimização do desempenho**.  Para os conceitos gerais de desempenho, consulte [guia de otimização de desempenho do Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parâmetros

Seguem-se as definições mais importantes para otimizar para um melhor desempenho ADLS:

* **Hive.tez.Container.size** – a quantidade de memória utilizada por cada tarefas

* **tamanho tez.grouping.min** – mínimo tamanho de cada mapeador

* **tamanho tez.grouping.Max** – máximo tamanho de cada mapeador

* **Hive.exec.reducer.bytes.Per.reducer** – tamanho de cada reducer

**Hive.tez.Container.size** -o tamanho do contentor determina a quantidade de memória está disponível para cada tarefa.  Este é o principal de entrada para controlar a simultaneidade no ramo de registo.  

**tamanho tez.grouping.min** – este parâmetro permite-lhe definir o tamanho mínimo do mapeador de cada.  Se o número de mappers Tez escolhe é menor do que o valor deste parâmetro, Tez utilizará o valor definido aqui.  

**tamanho tez.grouping.Max** – o parâmetro permite-lhe definir o tamanho máximo de cada mapeador.  Se o número de mappers Tez escolhe é maior do que o valor deste parâmetro, Tez utilizará o valor definido aqui.  

**Hive.exec.reducer.bytes.Per.reducer** – este parâmetro define o tamanho de cada reducer.  Por predefinição, cada reducer é de 256MB.  

## <a name="guidance"></a>Orientação

**Definir hive.exec.reducer.bytes.per.reducer** – o valor predefinido funciona bem quando os dados são descomprimidos.  Para os dados são comprimidos, deve reduzir o tamanho do reducer.  

**Definir hive.tez.container.size** – em cada nó, memória é especificada por yarn.nodemanager.resource.memory mb e deve ser definida corretamente no HDI cluster por predefinição.  Para obter informações adicionais sobre a definição de memória adequada no YARN, consulte este [publique](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Cargas de trabalho de e/s intensivas podem beneficiar do paralelismo mais ao diminuir o tamanho do contentor de Tez. Isto permite que o utilizador mais contentores que aumenta de concorrência.  No entanto, algumas consultas do Hive requerem uma quantidade significativa de memória (por exemplo, MapJoin).  Se a tarefa não tem memória suficiente, receberá um fora de exceção de memória durante o tempo de execução.  Se receber fora de exceções de memória, em seguida, deve aumentar a memória.   

O número de tarefas em execução ou o paralelismo simultâneo tem um vínculo pelo total de memória YARN.  O número de contentores YARN determinarão como muitas tarefas simultâneas podem ser executado.  Para localizar a memória YARN por nó, pode aceder à Ambari.  Navegue para YARN e ver o separador de folhas.  A memória YARN é apresentada nesta janela.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
A chave para melhorar o desempenho utilizando ADLS é aumentar a simultaneidade quanto possível.  Tez calcula automaticamente o número de tarefas que devem ser criados por isso não terá de configurá-lo.   

## <a name="example-calculation"></a>Cálculo de exemplo

Imaginemos que tem um cluster do 8 nó D14.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Limitações
**ADLS limitação** 

UIf, atingiu os limites de largura de banda fornecida pelo ADLS, seria começar a ver falhas de tarefas. Isto pode ser identificado ao observar erros limitação nos registos de tarefas.  Pode diminuir o paralelismo através do aumento do tamanho do contentor de Tez.  Se precisar de concorrência mais a tarefa, contacte-nos.   

Para verificar se a introdução limitada, tem de ativar a depuração de registo do lado do cliente. Eis como, pode fazê-lo:

1. Coloque a seguinte propriedade nas propriedades de log4j na configuração do ramo de registo. Isto pode ser feito a partir da vista Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG reiniciar todos os nós/serviço de configuração entrem em vigor.

2. Se a introdução limitada, verá o código de erro HTTP 429 no ficheiro de registo de ramo de registo. O ficheiro de registo do ramo de registo está a ser /tmp/&lt;utilizador&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Mais informações sobre a otimização de ramo de registo

Seguem-se alguns blogues que o irão ajudar a otimizar as consultas do Hive:
* [Otimizar as consultas do Hive do Hadoop no HDInsight](https://azure.microsoft.com/en-us/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Resolução de problemas de desempenho das consultas do Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite peça no otimizar do Hive no HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)

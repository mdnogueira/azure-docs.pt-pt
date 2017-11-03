---
title: Copiar dados de e para WASB no Data Lake Store utilizando o Distcp | Microsoft Docs
description: Utilizar o Distcp ferramenta para copiar dados de e para o Blobs Storage do Azure para o Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: 1c9e100b4a0e7781f0782a49835d50492895ded1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Utilizar o Distcp para copiar dados entre os Blobs de Armazenamento do Azure e o Arquivo do Data Lake
> [!div class="op_single_selector"]
> * [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Se tiver um cluster do HDInsight com acesso ao Data Lake Store, pode utilizar as ferramentas do ecossistema de Hadoop, como o Distcp para copiar dados **de e para** um armazenamento de cluster do HDInsight (WASB) para uma conta do Data Lake Store. Este artigo fornece instruções sobre como utilizam a ferramenta de Distcp.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster do HDInsight Azure** com acesso a uma conta de Data Lake Store. Consulte [criar um cluster do HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que ativar o ambiente de trabalho remoto para o cluster.

## <a name="do-you-learn-fast-with-videos"></a>Aprende depressa com vídeos?
[Veja este vídeo](https://mix.office.com/watch/1liuojvdx6sie) sobre como copiar dados entre o Blobs Storage do Azure e utilizar o DistCp do Data Lake Store.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Utilizar o Distcp de um cluster do HDInsight com Linux

Um cluster do HDInsight é fornecido com o utilitário Distcp, que pode ser utilizado para copiar dados de diferentes origens para um cluster do HDInsight. Se tiver configurado o cluster do HDInsight ao utilizar o Data Lake Store como armazenamento adicional, o utilitário de Distcp pode ser utilizado fora-de-a-box para copiar dados de e para uma conta de Data Lake Store também. Nesta secção, vamos ver como utilizar o utilitário de Distcp.

1. Do ambiente de trabalho, utilize o SSH para ligar ao cluster. Consulte [ligar a um cluster do HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Execute os comandos a partir da linha de SSH.

2. Verifique se pode aceder a Blobs de armazenamento do Azure (WASB). Execute o seguinte comando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdo no storage blob.

3. Da mesma forma, verifique se pode aceder à conta de Data Lake Store do cluster. Execute o seguinte comando:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    A saída deve fornecer uma lista de ficheiros/pastas na conta do Data Lake Store.

4. Utilize o Distcp para copiar dados de WASB para uma conta de Data Lake Store.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    O comando copia os conteúdos do **/exemplo dados/gutenberg/** pasta WASB para **/myfolder** na conta do Data Lake Store.

5. Da mesma forma, utilize o Distcp para copiar dados de conta do Data Lake Store para WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    O comando copia os conteúdos do **/myfolder** na conta do Data Lake Store para **/exemplodados/gutenberg/** pasta WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações sobre o desempenho ao utilizar o DistCp

Uma vez granularidade mais baixa do DistCp é um ficheiro único, definir o número máximo de cópias em simultâneo é o parâmetro mais importante para otimizá-lo contra o Data Lake Store. Número de cópias em simultâneo é controlado ao definir o número de mappers (Estou ') parâmetro na linha de comandos. Este parâmetro especifica o número máximo de mappers que são utilizados para copiar dados. Valor predefinido é 20.

**Exemplo**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como posso determinar o número de mappers utilizar?

Eis algumas orientações que poderá utilizar.

* **Passo 1: Determinar o total de memória YARN** -o primeiro passo consiste em determinar a memória YARN disponível para o cluster em que executou a tarefa de DistCp. Esta informação está disponível no portal do Ambari associado com o cluster. Navegue para YARN e ver o separador de cálculo de configurações para ver a memória YARN. Para obter a memória total do YARN, multiplique a YARN de memória por nó com o número de nós que tem no seu cluster.

* **Passo 2: Calcular o número de mappers** -o valor de **m** é igual ao quociente de memória total do YARN dividido pelo tamanho do contentor YARN. As informações de tamanho do contentor YARN estão disponíveis no portal do Ambari. Navegue para YARN e ver o separador de folhas. É apresentado o tamanho de contentor do YARN nesta janela. A equação de chegam o número de mappers (**m**) é

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemplo**

Vamos assumir que tem um 4 D14v2s nós no cluster e está a tentar a transferência de 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contém variando quantidades de dados e os tamanhos de ficheiro dentro de cada pasta são diferentes.

* Total de memória YARN - do Ambari portal a determinar se a memória YARN é 96 GB para um nó de D14. Por isso, é o total de memória YARN para o cluster de quatro nós: 

        YARN memory = 4 * 96GB = 384GB

* Número de mappers - portal do Ambari a determinar se o tamanho de contentor do YARN é 3072 para um nó de cluster D14. Por isso, é o número de mappers:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se outras aplicações estão a utilizar memória, em seguida, pode optar por utilizar apenas uma parte da memória do seu cluster YARN para DistCp.

### <a name="copying-large-datasets"></a>Copiar grandes conjuntos de dados

Quando o tamanho do conjunto de dados sejam movidos é grande (por exemplo, > 1 TB) ou se tiver muitos pastas diferentes, deve considerar a utilização de várias tarefas de DistCp. É provável que não existem ganhos de desempenho, mas propaga terminar as tarefas de modo a que o se qualquer tarefa falhar, apenas terá de reiniciar essa tarefa específica em vez da tarefa de toda.

### <a name="limitations"></a>Limitações

* DistCp tenta criar mappers são semelhantes de tamanho para otimizar o desempenho. Aumento do número de mappers poderá não sempre aumentar o desempenho.

* DistCp está limitado a apenas um mapeador por ficheiro. Por conseguinte, não deve ter mais mappers superior ao número de ficheiros. Uma vez que o DistCp só pode atribuir um mapeador para um ficheiro, isto limita a quantidade de concorrência que pode ser utilizada para copiar ficheiros grandes.

* Se tiver um pequeno número de ficheiros grandes, deve dividi-los em segmentos de ficheiro de 256 MB para lhe dar mais simultaneidade potencial. 
 
* Se estiver a copiar a partir de uma conta do Blob Storage do Azure, poderá ser limitada a tarefa de cópia no lado de armazenamento de Blobs. Isto degrada o desempenho da sua tarefa de cópia. Para saber mais sobre os limites do Blob Storage do Azure, consulte os limites de armazenamento do Azure em [subscrição do Azure e limites de serviço](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Consultar também
* [Copiar dados de Blobs de armazenamento do Azure para o Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)

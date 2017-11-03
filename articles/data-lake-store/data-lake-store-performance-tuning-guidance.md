---
title: "Diretrizes de otimização do desempenho de Azure Data Lake Store | Microsoft Docs"
description: "Diretrizes de otimização do desempenho de Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 900447ab931f15e4d27aedd525eba7881ba813b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tuning-azure-data-lake-store-for-performance"></a>Otimização do Azure Data Lake Store para desempenho

Data Lake Store suporta débito elevado para análise intensiva de i / e de movimento de dados.  No Azure Data Lake Store, utilizar o débito disponíveis todos os – a quantidade de dados que podem ser lidos ou escritos por segundo – é importante para obter o melhor desempenho.  Isto é conseguido através de leituras tantos e escreve em paralelo quanto possível.

![Desempenho de data Lake Store](./media/data-lake-store-performance-tuning-guidance/throughput.png)

O Azure Data Lake Store pode dimensionar para fornecer o débito necessário para todos os cenário de análise. Por predefinição, uma conta do Azure Data Lake Store fornece automaticamente suficiente débito para satisfazer as necessidades de uma categoria abrangente de cenários de utilização. Para os casos em que os clientes depare com o limite predefinido, a conta ADLS pode ser configurada para fornecer o débito mais contactando o suporte da Microsoft.

## <a name="data-ingestion"></a>Ingestão de dados

Quando a ingestão de dados de um sistema de origem para ADLS, é importante a ter em consideração que podem ser o hardware de origem, o hardware de rede de origem e a conectividade de rede para ADLS valor.  

![Desempenho de data Lake Store](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

É importante certificar-se de que o movimento de dados não é afetado por estes fatores.

### <a name="source-hardware"></a>Hardware de origem

Se estiver a utilizar máquinas no local ou VMs no Azure, deve selecionar cuidadosamente o hardware adequado. Para Hardware do disco de origem, preferir SSDs HDDs e escolha o hardware de disco com spindles mais rápidas. Para o Hardware de rede de origem, utilize os possíveis NICs mais rápido.  No Azure, é recomendável VMs do Azure D14 que tem o disco adequadamente poderoso e o hardware de rede.

### <a name="network-connectivity-to-azure-data-lake-store"></a>Conectividade de rede para o Azure Data Lake Store

A conectividade de rede entre a origem de dados e o Azure Data Lake store, por vezes, pode ser o congestionamento. Quando os dados de origem no local, considere utilizar uma ligação dedicada com [Azure ExpressRoute](https://azure.microsoft.com/en-us/services/expressroute/) . Se os dados de origem no Azure, será melhor o desempenho quando os dados estão na mesma região do Azure Data Lake Store.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Configurar as ferramentas de ingestão de dados para parallelization máximo

Após ter resolvido o hardware de origem e de rede congestionamentos de conetividade acima, está pronto para configurar as ferramentas da ingestão. A tabela seguinte resume as definições da chave para várias ferramentas de ingestão populares e fornece aprofundado Otimização do desempenho artigos para eles.  Para mais informações sobre que ferramenta a utilizar para o seu cenário, visite este [artigo](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-data-scenarios).

| Ferramenta               | Definições     | Obter mais detalhes                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount |  [Ligação](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-powershell#performance-guidance-while-using-powershell)   |
| AdlCopy    | Unidades de análise do Azure Data Lake  |   [Ligação](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapeador)   | [Ligação](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Ligação](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.Block.size, -m (mapeador)    |   [Ligação](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Estrutura do conjunto de dados

Quando os dados são armazenados no Data Lake Store, o tamanho do ficheiro, o número de ficheiros e estrutura de pastas tem um impacto no desempenho.  A secção seguinte descreve as melhores práticas nas seguintes áreas.  

### <a name="file-size"></a>Tamanho dos ficheiros

Normalmente, os motores de análise, tais como o HDInsight e o Azure Data Lake Analytics tem um overhead por ficheiro.  Se armazenar os dados tantos pequenos ficheiros, isto pode afetar negativamente desempenho.  

Em geral, organize os dados em ficheiros superiores de tamanho para um melhor desempenho.  Como uma regra geral, organizar os conjuntos de dados nos ficheiros de 256MB ou superior. Em alguns casos, tais como imagens e os dados binários, não é possível processá-los em paralelo.  Nestes casos, é recomendado para manter os ficheiros individuais em 2GB.

Por vezes, pipelines de dados limitada controlo sobre os dados não processados que tem muitos ficheiros pequenos.  É recomendado ter um processo "cooking", que gere ficheiros maiores a utilizar para as aplicações a jusante.  

### <a name="organizing-time-series-data-in-folders"></a>Organizar os dados de séries de tempo em pastas

Hive e ADLA cargas de trabalho de eliminação de partições de dados de séries de tempo pode ajudar a algumas consultas apenas um subconjunto dos dados que melhora o desempenho de leitura.    

Os pipelines que ingestão de dados de séries de tempo, muitas vezes, colocar os seus ficheiros com uma atribuição de nomes estruturados muito para ficheiros e pastas. Segue-se um exemplo muito comum, vemos para dados que estão estruturados por data:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Tenha em atenção que as informações de datetime aparecem como pastas e no nome de ficheiro.

Data e hora, segue um padrão comum

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Novamente, a opção com a pasta e os deve otimizar a organização de ficheiro para os tamanhos de ficheiro maior e um número de ficheiros de cada pasta razoável.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Otimizar as e/s intensivas tarefas em cargas de trabalho Hadoop e o Spark no HDInsight

As tarefas enquadram-se um dos três categorias a seguir:

* **Intensivo da CPU.**  Estas tarefas têm tempo de computação longo mínimos tempos e/s.  Exemplos de linguagem natural a tarefas de processamento e de aprendizagem.  
* **Memória intensiva.**  Estas tarefas utilizam muita largura de memória.  Os exemplos incluem PageRank e tarefas de análise em tempo real.  
* **Intensiva da e/s.**  Estas tarefas gastam a maior parte do respetivo tempo ao executar a e/s.  Um exemplo comum é uma tarefa de cópia que só de leitura e escrita operações.  Outros exemplos tarefas de preparação de dados que uma grande quantidade de dados de leitura, efetua algumas transformação de dados e, em seguida, escreve os dados de volta para o arquivo.  

As seguintes orientações só é aplicável para tarefas intensivas de e/s.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Considerações gerais para um cluster do HDInsight

* **Versões do HDInsight.** Para melhor desempenho, utilize a versão mais recente do HDInsight.
* **Regiões.** Coloque o Data Lake Store na mesma região que o cluster do HDInsight.  

Um cluster do HDInsight é composto por dois nós principais e alguns nós de trabalho. Cada nó de trabalho fornece um número específico de núcleos e memória, o que é determinada pelo tipo de VM.  Ao executar uma tarefa, o YARN é a negotiator de recurso aloca a memória disponível e núcleos para criar contentores.  Cada contentor executa as tarefas necessárias para concluir a tarefa.  Contentores executam em paralelo para processar tarefas rapidamente. Por conseguinte, o desempenho é melhorado através da execução de contentores paralelas tantas quanto possível.

Existem três camadas dentro de um cluster do HDInsight que podem ser otimizados para aumentar o número de contentores e utilizar todos os débito disponíveis.  

* **Camada física**
* **Camada YARN**
* **Camada de carga de trabalho**

### <a name="physical-layer"></a>Camada física

**Execute cluster com mais nós de e/ou VMs tamanho maior.**  Um cluster maior permitirá a execução mais contentores do YARN conforme mostrado na imagem abaixo.

![Desempenho de data Lake Store](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Utilize as VMs com mais largura de banda de rede.**  A quantidade de largura de banda de rede pode ser um estrangulamento se há menos largura de banda de rede que o débito de Data Lake Store.  Diferentes VMs tem vários tamanhos de largura de banda de rede.  Escolha um tipo de VM que tenha a maior largura de banda de rede possível.

### <a name="yarn-layer"></a>YARN camada

**Utilize contentores YARN mais pequenos.**  Reduza o tamanho de cada contentor YARN para criar mais contentores com a mesma quantidade de recursos.

![Desempenho de data Lake Store](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Consoante a carga de trabalho, não existe será sempre um tamanho de contentor YARN mínimo necessário. Se escolher demasiado pequeno um contentor, as tarefas serão executadas para problemas de memória esgotada. Normalmente, contentores YARN devem não pode ser inferior a 1GB. É comum para ver os contentores YARN de 3GB. Para algumas cargas de trabalho, poderá ser necessário maior contentores YARN.  

**Aumente núcleos por contentor YARN.**  Aumente o número de núcleos alocados para cada contentor para aumentar o número de tarefas paralelas que são executadas em cada contentor.  Isto funciona para aplicações, como Spark que executam várias tarefas por contentor.  Para aplicações, como o ramo de registo que são executadas num único thread em cada contentor, é melhor tem mais contentores em vez de mais núcleos por contentor.   

### <a name="workload-layer"></a>Camada de carga de trabalho

**Utilize todos os contentores disponíveis.**  Defina o número de tarefas para ser igual ou maior do que o número de contentores disponíveis para que todos os recursos são utilizados.

![Desempenho de data Lake Store](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Tarefas falhadas são dispendiosas.** Se cada tarefa tem uma grande quantidade de dados para processar, falha de uma tarefa resulta numa repetição dispendiosas.  Por conseguinte, é melhor criar mais tarefas, cada um dos quais processa uma pequena quantidade de dados.

Para além das Diretrizes gerais acima, cada aplicação dispõe de diferentes parâmetros disponíveis para otimizar para essa aplicação específica. A tabela abaixo lista alguns dos parâmetros e ligações para começar a otimização do desempenho para cada aplicação.

| Carga de trabalho               | Parâmetro de conjunto de tarefas                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [O Spark no HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Núm executores</li><li>Memória de executor</li><li>Executor núcleos</li></ul> |
| [O Hive no HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>Hive.tez.Container.size</li></ul>         |
| [MapReduce no HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>mapreduce.Map.Memory</li><li>Mapreduce.job.Maps</li><li>mapreduce.reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm no HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Número de processos de trabalho</li><li>Número de instâncias de executor de spout</li><li>Número de instâncias de executor bolt </li><li>Número de tarefas de spout</li><li>Número de tarefas bolt</li></ul>|

## <a name="see-also"></a>Consultar também
* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
* [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

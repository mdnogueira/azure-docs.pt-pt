---
title: "As opções de contexto para o servidor R no HDInsight - Azure de computação | Microsoft Docs"
description: "Saiba mais sobre as opções de contexto computação diferentes disponíveis para os utilizadores com o servidor R no HDInsight"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 24df96f55b0f207d8576bd05c2c83a884e7fc2bd
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>As opções de contexto para o servidor R no HDInsight de computação

Microsoft R Server no Azure HDInsight controla a forma como as chamadas são executadas ao definir o contexto de computação. Este artigo descreve as opções disponíveis especificar se e como a execução é paralelizada por núcleos do nó de extremidade ou cluster do HDInsight.

O nó de extremidade de um cluster fornece um local conveniente para ligar ao cluster e executar os scripts de R. Com um nó de extremidade, tem a opção de executar as funções distribuídas parallelized do ScaleR em todos os núcleos do servidor edge nó. Também pode executá-los em todos os nós do cluster através Hadoop mapa reduzir do ScaleR ou contextos de computação de Spark.

## <a name="microsoft-r-server-on-azure-hdinsight"></a>Microsoft R Server no Azure HDInsight
[Microsoft R Server no Azure HDInsight](r-server-overview.md) fornece funcionalidades mais recentes para a análise baseada em R. Pode utilizar os dados armazenados num contentor do HDFS no seu [Blob do Azure](../../storage/common/storage-introduction.md "Blob storage do Azure") conta de armazenamento, um arquivo Data Lake ou o sistema de ficheiros local do Linux. Uma vez que o servidor R está incorporado no código aberto R, podem aplicar as aplicações baseadas em R que criar nenhum dos pacotes de open source para R 8000 +. Também poderá utilizar rotinas no [RevoScaleR](https://msdn.microsoft.com/microsoft-r/scaler/scaler), pacote de análise de macrodados da Microsoft que está incluído com o servidor R.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos de um nó de extremidade de computação
Em geral, um script do R que é executado no servidor R no nó de extremidade é executado dentro do interpretador de R nesse nó. As exceções são esses passos que chamar uma função de ScaleR. As chamadas de ScaleR executam num ambiente de computação que é determinado pelo como definir o contexto de computação ScaleR.  Quando executar o script do R a partir de um nó de extremidade, os valores possíveis do contexto de computação são:

- local sequencial (*'local'*)
- local paralelo (*'localpar'*)
- Reduzir o mapa
- Spark

O *'local'* e *'localpar'* diferem opções apenas em como **rxExec** chamadas são executadas. Ambos os executar outras chamadas de função rx de forma paralela em núcleos disponíveis a menos que especificado em contrário através da utilização do ScaleR **numCoresToUse** opção, por exemplo `rxOptions(numCoresToUse=6)`. Opções de execução paralela oferecem um desempenho ideal.

A tabela seguinte resume as várias opções de contexto de computação para definir a forma como as chamadas são executadas:

| Contexto de cálculo  | Como definir                      | Contexto de execução                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Local sequencial | rxSetComputeContext('local')    | Execução paralelizada em todos os núcleos do servidor de nó edge, exceto as chamadas rxExec, que são executados serialmente |
| Paralelo local   | rxSetComputeContext('localpar') | Execução paralelizada em todos os núcleos do servidor edge nó |
| Spark            | RxSpark()                       | Paralelizada distribuída execução através do Spark em todos os nós do HDI cluster |
| Reduzir o mapa       | RxHadoopMR()                    | Paralelizada distribuída execução através de reduzir o mapa em todos os nós do HDI cluster |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para decidir num contexto de computação

Qual das três opções que escolher que fornecem paralelizada execução depende da natureza do seu trabalho de análise, o tamanho e a localização dos seus dados. Não há nenhum fórmula simple que indica que a computação contexto para utilizar. No entanto, existem algumas princípios de orientação para que possam ajudar a tornar a escolha certa ou, pelo menos, ajudar a restringir as suas escolhas antes de executar um benchmark. Estes princípios ao servir de pelos incluem:

- O sistema de ficheiros local do Linux é mais rápido do que o HDFS.
- Estatísticas repetidas são mais rápidas, se os dados locais e se está a ser XDF.
- É preferível para transmitir pequenas quantidades de dados a partir de uma origem de dados de texto. Se a quantidade de dados for superior, convertê-la XDF antes de análise.
- A sobrecarga de copiar ou os dados ao nó de extremidade para análise de transmissão em fluxo fica unmanageable para grandes quantidades de dados.
- O Spark é superior a reduzir o mapa para análise no Hadoop.

Tendo em conta estes princípios, as secções seguintes disponibilizam algumas prática regras gerais para a seleção de um contexto de computação.

### <a name="local"></a>Local
* Se a quantidade de dados para analisar é pequena e não requerem a análise repetido, em seguida, transmiti-lo diretamente para o Analysis Services rotina utilizando *'local'* ou *'localpar'*.
* Se a quantidade de dados para analisar pequena ou média e requer a análise repetido, em seguida, copie-a para o sistema de ficheiros local, importá-lo para XDF e analisá-lo através de *'local'* ou *'localpar'*.

### <a name="hadoop-spark"></a>Spark de Hadoop
* Se a quantidade de dados para analisar for grande, em seguida, importá-lo para utilizar o Spark DataFrame **RxHiveData** ou **RxParquetData**, ou para XDF no HDFS (a menos que o armazenamento é um problema) e analisá-lo utilizando o contexto de computação de Spark.

### <a name="hadoop-map-reduce"></a>Reduzir o mapa de Hadoop
* Utilize o contexto de computação mapa reduzir apenas se ocorrer um problema com o contexto de computação do Spark insurmountable, uma vez que é, geralmente, mais lenta.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda inline no rxSetComputeContext
Para obter mais informações e exemplos de contextos de computação ScaleR, consulte o inline ajudar R no método rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Também pode consultar o "[ScaleR Distributed informática Guide](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing)" que está disponível a partir do [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "servidor R no MSDN") biblioteca.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu sobre as opções que estão disponíveis para especificar se e como a execução é paralelizada por núcleos do nó de extremidade ou cluster do HDInsight. Para saber mais sobre como utilizar o R Server com clusters do HDInsight, consulte os seguintes tópicos:

* [Descrição geral do R Server para o Hadoop](r-server-overview.md)
* [Introdução ao servidor R para o Hadoop](r-server-get-started.md)
* [Azure Storage options for R Server on HDInsight (Opções do Armazenamento do Azure para o R Server no HDInsight)](r-server-storage.md)


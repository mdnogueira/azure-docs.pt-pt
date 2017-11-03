---
title: "Notas de versão do componentes do Hadoop no Azure HDInsight | Microsoft Docs"
description: "Notas de versão mais recentes e versões de componentes do Hadoop para o Azure HDInsight. Obter sugestões de desenvolvimento e os detalhes para Spark, servidor R, Hive e muito mais."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: nitinme
ms.openlocfilehash: 96b2b4976729da5b7d8b75909dbe099090240c08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notas de versão para componentes do Hadoop no Azure HDInsight

Este artigo fornece informações sobre o **mais recente** atualizações de lançamento do Azure HDInsight. Para obter informações sobre versões anteriores, consulte [arquivo de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [artigo de controlo de versões do HDInsight](hdinsight-component-versioning.md).


## <a name="notes-for-08012017-release-of-hdinsight"></a>Notas de versão de 01/08/2017 do HDInsight

| Título | Descrição | Área afectada  | Tipo de Cluster  | 
| --- | --- | --- | --- | --- |
| Versão do servidor de Microsoft R 9.1 no HDInsight |HDInsight suporta agora o aprovisionamento de clusters de 9.1 de servidor R no HDInsight. Para obter mais informações sobre a versão do Microsoft R Server 9.1, consulte [este blogue](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Serviço |R Server |
| HDInsight 3.6 inclui agora as versões mais recentes da pilha de Hadoop|<ul><li>Para obter uma lista detalhada das versões atualizadas, consulte [versões de componentes do Hadoop no HDInsight disponíveis](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>Para obter uma lista de erros fixo nas versões mais recentes da pilha do Hadoop, consulte [Apache Patch informações](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Para obter uma lista de alterações entre HDP 2.6.1 (que está agora disponível no HDInsight 3.6) de última hora, consulte [https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Para obter uma lista dos problemas conhecidos no HDP 2.6.1, consulte [problemas conhecidos](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Serviço |Todos |N/D |
| Atualizações para clusters interativa de ramo de registo (pré-visualização) |<ul><li><b>Melhoramento de funcionalidade.</b> Implementação do metastore em cache que reduz a carga no back-end do SQL Server ao colocar em cache os metadados e melhora o desempenho para todas as operações de metadados.  Este melhoramento é agora uma predefinição em todos os clusters de ramo de registo interativo. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Melhoramento de funcionalidade.</b> Carregamento de partição dinâmica está otimizado. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Melhoramento de funcionalidade.</b> Otimizações de configuração para o HDInsight no Linux.</li><li><b>Correção de erro.</b> `CredentialProviderFactory$getProviders`Não é seguro para thread. Este problema ser corrigido agora. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Correção de erro.</b> Utilização elevada da CPU com controlador WASB `liststatus` API resultando num mau desempenho de ATS. Este problema ser corrigido agora. Para obter mais informações, consulte [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Serviço |Ramo de registo interativo (pré-visualização) |
| Atualizações para os clusters do Hadoop |Fiabilidade de operação de tarefa Templeton foi melhorada. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Serviço |Hadoop |
| YARN atualizações | HDInsight cria agora uma 250 GB Ambari base de dados (sem o custo aumentar), que resulta numa melhor experiência para os clientes. Esta alteração deve impedir que ATS obter preenchidos cópias de segurança e provavelmente têm um melhor desempenho. |Serviço |Todos |
| Atualizações do Spark | Versão do Spark 2.1.1. Para obter mais informações, consulte [Spark versão 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Serviço | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>06/04/2017 - disponibilidade geral do HDInsight 3.6

* Com esta versão, o Azure HDInsight adiciona versão 3.6, que se baseia no HDP 2.6. Notas de versão HDP 2.6 estão disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) e obter mais informações sobre as versões do HDInsight podem ser encontradas [aqui](hdinsight-component-versioning.md). HDInsight 3.6 está disponível para as cargas de trabalho seguintes:

    * V2.7.3 de Hadoop
    * HBase v1.1.2
    * Storm v1.1.0
    * O Spark v2.1.0
    * Ramo de registo interativo v2.1.0

* **Suporte para a vista do Hive 2.0**. Isto deve melhorar a experiência de utilizador para o ramo de registo interativo. Para obter mais informações, consulte [Hortonworks documentação](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Melhorias de desempenho com o Hive LLAP**. Para obter mais informações, consulte [Hortonworks documentação](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Novas funcionalidades do ramo de registo**. Consulte [Hortonworks documentação](https://hortonworks.com/apache/hive/#section_4).

* **Descontinuação do CLI de ramo de registo**: CLI de ramo de registo está a ser preterido e os clientes são encorajados a utilizar em vez disso, Beeline. Para obter mais informações, consulte [documentação do Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Para obter instruções sobre como utilizar Beeline com o HDInsight, consulte [Beeline utilizar com o HDInsight Hadoop clusters](hdinsight-hadoop-use-hive-beeline.md).

* **Novas funcionalidades do Apache Phoenix e o HBase**.
    * Suporte de quota de armazenamento: normalmente utilizado em ambientes de multi-inquilinos, permitindo que o espaço de armazenamento limitada numa por tabela e por nível de espaço de nomes.
    * O Phoenix indexação melhorias: criação de índices Incremental e reconstrução/retomar a indexação de falhas de anteriores.
    * Ferramenta de integridade de dados de Phoenix: suporta a validação de esquema, índice e outros metadados.


* **Problema com o HBase**: ao executar um carregamento em massa CSV MapReduce da tarefa, a seguinte sintaxe poderá resultar num erro.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Em alternativa, utilize a seguinte sintaxe:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 - versão 2.1 o Spark no HDInsight 3.6 (pré-visualização)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) melhora muitos problemas de estabilidade e a facilidade de utilização com versões anteriores. Também inclui novas funcionalidades em todos os Spark cargas de trabalho, tais como o Spark Core, SQL Server, ML e transmissão em fluxo.
* Transmissão em fluxo estruturada obtém uma maior escalabilidade com suporte para as marcas de água de tempo de eventos e conector Kafka 0.10.
* A criação de partições de Spark SQL agora é processada utilizando o mecanismo de processamento de partição dimensionável de novo. Ver mais detalhes [aqui](http://spark.apache.org/releases/spark-release-2-1-0.html) sobre como atualizar.
* 2.1 do Spark no Azure HDInsight 3.6 pré-visualização atualmente não suporta a conectividade da ferramenta de BI utilizando o controlador ODBC.
* Acesso do Azure Data Lake Store de clusters do Spark 2.1 não é suportado nesta pré-visualização.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>18/11/2016 - versão do 2.0.1 o Spark no HDInsight 3.5
O Spark 2.0.1 está agora disponível nos clusters do Spark (HDInsight versão 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>16/11/2016 - versão do servidor de R 9.0 no HDInsight 3.5 (Spark 2.0)
*   R Server clusters incluem agora a opção para duas versões: R Server 9.0 HDI 3.5 (Spark 2.0) e 8.0 do servidor R no HDI 3.4 (Spark 1.6).
*   R Server 9.0 no HDI 3.5 (Spark 2.0) está incorporada no R 3.3.2 e inclui as funções de origem de dados de ScaleR novo denominadas RxHiveData e RxParquetData para carregar dados do Hive e Parquet diretamente para o Spark DataFrames para análise pelo ScaleR. Para obter mais informações, consulte o inline ajuda nestas funções do R através da utilização do **? RxHiveData** e **? RxParquetData** comandos.
*   Edição de Comunidade do RStudio servidor está agora instalada por predefinição (com a opção de recusa) no painel de configuração de Cluster como parte do fluxo de aprovisionamento.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - versão do 2.0 o Spark no HDInsight
* Clusters 2.0 do Spark no HDInsight 3.5 suportam agora o Livy e Jupyter serviços.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>26/10/2016 - versão do servidor R no HDInsight
* O URI para acesso de nó de extremidade foi alterada para **clustername**-ed-h
* R Server no aprovisionamento de cluster de HDInsight foi está mais simples.
* R Server no HDInsight está agora disponível como regular HDInsight "Servidor R" tipo de cluster e já não está instalado como uma aplicação HDInsight separada. O nó de extremidade e os binários de servidor de R são agora aprovisionados como parte da implementação de cluster do servidor R. Isto melhora a velocidade e fiabilidade de aprovisionamento. Modelo de preços para o servidor R é atualizado em conformidade.
* Preços de tipo de cluster de servidor R agora é baseado no preço de escalão Standard plus preços de surcharge R Server. Escalão Premium é reservado para as funcionalidades Premium disponíveis em tipos de cluster diferentes e não é utilizado para o tipo de cluster de servidor R. Esta alteração não afeta o preço Efetivo do servidor R; as alterações apenas como os encargos são apresentados na factura. Todos os clusters de servidor R existentes continuem a funcionar e modelos do Resource Manager continuam a funcionar até que o aviso de preterição. **Recomenda-se para atualizar as implementações de script para utilizar o novo modelo do Resource Manager.**







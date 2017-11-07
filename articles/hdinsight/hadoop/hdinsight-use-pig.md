---
title: Utilizar o Pig do Hadoop no HDInsight | Microsoft Docs
description: Saiba como utilizar o Pig com o Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: larryfr
ms.openlocfilehash: 5f5b34c6610a1c07687f0681480f7422986d6f20
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Utilizar o Pig com o Hadoop no HDInsight

Saiba como utilizar [Apache Pig](http://pig.apache.org/) com o HDInsight...

PIg é uma plataforma para a criação de programas do Hadoop ao utilizar uma linguagem procedimento conhecida como *Pig Latin*. PIg é uma alternativa à Java para criar *MapReduce* soluções, que é incluído no Azure HDInsight. Utilize a tabela seguinte para detetar as várias formas que Pig podem ser utilizadas com o HDInsight:

| **Utilize esta opção** se pretender que... | … .an **interativa** shell | ... **batch** processamento | … .with isto **sistema de operativo de cluster** | … .from isto **sistema operativo do cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou do Windows |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou do Windows |Windows (por agora) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou do Windows |Windows |
| [Ambiente de trabalho remoto](apache-hadoop-use-pig-remote-desktop.md) (HDInsight 3.2 e 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a id="why"></a>Porquê utilizar o Pig

Um dos desafios de processamento de dados ao utilizar o MapReduce no Hadoop está a implementar a lógica de processamento através da utilização de apenas um mapa e uma função de reduzir. Para processamento complexo, muitas vezes, terá de quebrar processamento em várias operações de MapReduce que estão encadeadas em conjunto para alcançar o resultado pretendido.

PIg permite-lhe definir o processamento como uma série de transformações de que os dados de saída flui através para produzir o resultado pretendido.

O idioma de Pig Latin permite-lhe descrever o fluxo de dados de entrada não processado, através de um ou mais transformações, para produzir o resultado pretendido. Programas de PIg Latin siga este padrão geral:

* **Carga**: ler os dados para ser manipulado entre o sistema de ficheiros

* **Transformar**: manipular os dados

* **Informação do Estado ou armazenar**: dados para o ecrã de saída ou armazene-o para processamento

### <a name="user-defined-functions"></a>Funções definidas pelo utilizador

PIg Latin também suporta as funções definidas pelo utilizador (UDF), que lhe permite invocar componentes externos que implementam a lógica que é difícil de modelo de Pig Latin.

Para mais informações sobre o Pig Latin, consulte [Pig Latin referência Manual 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e [Pig Latin referência Manual 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Para obter um exemplo de utilização UDFs com Pig, consulte os seguintes documentos:

* [Utilizar DataFu com Pig no HDInsight](apache-hadoop-use-pig-datafu-udf.md) -DataFu é uma coleção de UDFs útil mantida por Apache
* [Utilizar o Python com o Pig e o Hive no HDInsight](python-udf-hdinsight.md)
* [Utilizar c# com o Hive e do Pig no HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados no `/example/data` e `/HdiSamples` diretórios. Estes diretórios são no armazenamento de predefinido para o cluster. O exemplo de Pig neste documento utiliza o *log4j* de ficheiros de `/example/data/sample.log`.

Cada registo dentro do ficheiro é composta por uma linha de campos que contém um `[LOG LEVEL]` campo para mostrar o tipo e a gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de registo se o erro.

> [!NOTE]
> Também pode gerar um ficheiro de log4j, utilizando o [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) ferramenta de registo e, em seguida, carregue esse ficheiro para o blob. Consulte [carregar dados para o HDInsight](../hdinsight-upload-data.md) para obter instruções. Para obter mais informações sobre como os blobs no armazenamento do Azure são utilizados com o HDInsight, consulte [utilizar armazenamento de Blobs do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Tarefa de exemplo

A tarefa Pig Latin seguinte carrega o `sample.log` ficheiro a partir do armazenamento de predefinido para o seu cluster HDInsight. Em seguida, executa uma série de transformações que resulta numa contagem de quantas vezes a cada nível de registo ocorreu nos dados de entrada. Os resultados são escritos STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

A imagem seguinte mostra um resumo do que faz cada transformação aos dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

## <a id="run"></a>Executar a tarefa Pig Latin

HDInsight pode executar tarefas do Pig Latin, utilizando uma variedade de métodos. Utilize a tabela seguinte para decidir qual o método é mais adequado para si e, em seguida, siga a ligação para obter instruções.

| **Utilize esta opção** se pretender que... | … .an **interativa** shell | ... **batch** processamento | … .with isto **sistema de operativo de cluster** | … .from isto **cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou do Windows |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou do Windows |Windows (por agora) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou do Windows |Windows |
| [Ambiente de trabalho remoto](apache-hadoop-use-pig-remote-desktop.md) (HDInsight 3.2 e 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="pig-and-sql-server-integration-services"></a>PIg e SQL Server Integration Services

Pode utilizar o SQL Server Integration Services (SSIS) para executar uma tarefa Pig. O pacote de funcionalidades do Azure para SSIS fornece os seguintes componentes que funcionam com tarefas do Pig no HDInsight.

* [Tarefas do Azure HDInsight Pig][pigtask]

* [Gestor de ligações de subscrição do Azure][connectionmanager]

Saiba mais sobre o pacote de funcionalidades do Azure para SSIS [aqui][ssispack].

## <a id="nextsteps"></a>Passos seguintes
Agora que aprendeu como utilizar o Pig com o HDInsight, utilize as hiperligações seguintes para explorar as outras formas de trabalhar com o Azure HDInsight.

* [Carregar dados para o HDInsight](../hdinsight-upload-data.md)
* [Use Hive with HDInsight (Utilizar o Hive com o HDInsight)][hdinsight-use-hive]
* [Utilizar o Sqoop com o HDInsight](hdinsight-use-sqoop.md)
* [Utilizar o Oozie com o HDInsight](../hdinsight-use-oozie.md)
* [Utilizar tarefas de MapReduce com o HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif

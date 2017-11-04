---
title: Utilizar DataFu com Pig no HDInsight - Azure | Microsoft Docs
description: "DataFu é uma coleção de bibliotecas para utilização com o Hadoop. Saiba como pode utilizar DataFu com Pig no cluster do HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: b8cb277a7101e2627b541e2d4e8ebae854aba4ca
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-datafu-with-pig-on-hdinsight"></a>Utilizar DataFu com pig no HDInsight

Saiba como utilizar DataFu com o HDInsight. DataFu é uma coleção de bibliotecas de Open Source para utilização com Pig no Hadoop.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.

* Um cluster do Azure HDInsight (Linux ou baseado no Windows)

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Uma básica familiaridade com [utilizando Pig no HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Instalar DataFu no HDInsight baseado em Linux

> [!IMPORTANT]
> DataFu está instalado numa versão de clusters baseados em Linux 3.3 e posterior e em clusters baseados em Windows. Não é instalada em clusters baseados em Linux anteriores ao 3.3.
>
> Se estiver a utilizar um cluster baseado em Windows ou a um cluster baseado em Linux superior à versão 3.3, ignore esta secção.

DataFu pode ser transferido e instalado a partir do repositório Maven. Utilize os seguintes passos para adicionar DataFu ao cluster do HDInsight:

1. Ligar ao cluster do HDInsight baseado em Linux através do SSH. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para transferir o ficheiro jar de DataFu utilizando o utilitário de wget, ou copie e cole a hiperligação no seu browser para iniciar a transferência.

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. Em seguida, carregue o ficheiro para o armazenamento de predefinido para o seu cluster HDInsight. Colocar o ficheiro predefinido no armazenamento torna disponível para todos os nós do cluster.

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > O comando anterior armazena jar no `/example/jars` , uma vez que este diretório já existe no armazenamento de cluster. Pode utilizar qualquer localização que desejar no armazenamento de cluster do HDInsight.

## <a name="use-datafu-with-pig"></a>Utilizar DataFu com Pig

Os passos nesta secção partem do princípio de que está familiarizado com a utilização do Pig no HDInsight. Para obter mais informações sobre como utilizar o Pig com o HDInsight, consulte [utilizar o Pig com o HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]
> Se instalou manualmente DataFu utilizando os passos na secção anterior, tem de registá-lo antes de o utilizar.
>
> * Se o cluster utiliza armazenamento do Azure, utilize um `wasb://` caminho. Por exemplo, `register wasb:///example/jars/datafu-1.2.0.jar`.
>
> * Se o cluster utiliza o Azure Data Lake Store, utilize um `adl://` caminho. Por exemplo, `register adl://home/example/jars/datafu-1.2.0.jar`.

Muitas vezes, é possível definir um alias para DataFu funções. O exemplo seguinte define um alias de `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Em seguida, pode utilizar este alias no script Pig Latin para gerar um hash para os dados de entrada. Por exemplo, o seguinte código substitui a localização dos dados de entrada com um valor de hash:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Gera o seguinte resultado:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre DataFu ou Pig, consulte os seguintes documentos:

* [Guia do Apache DataFu Pig](http://datafu.incubator.apache.org/docs/datafu/guide.html).
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)

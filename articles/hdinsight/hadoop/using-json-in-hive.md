---
title: Analisar e processamento de documentos JSON com Apache Hive no Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar documentos JSON e analisá-las ao utilizar o apache Hive no Azure HDInsight"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: c32872a79cc87fe0ecfff269f941b29730266110
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Processar e analisar os documentos JSON utilizando o Apache Hive no Azure HDInsight

Saiba como processar e analisar os ficheiros de JavaScript Object Notation (JSON) utilizando o Apache Hive no Azure HDInsight. Este tutorial utiliza o documento JSON seguinte:

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
                    ]
    }

O ficheiro pode ser encontrado em  **wasb://processjson@hditutorialdata.blob.core.windows.net/** . Para obter mais informações sobre como utilizar o Blob storage do Azure com o HDInsight, consulte [compatível com a utilização HDFS Blob storage do Azure com o Hadoop no HDInsight](../hdinsight-hadoop-use-blob-storage.md). Pode copiar o ficheiro para o contentor predefinido do cluster.

Neste tutorial, utilize a consola do Hive. Para obter instruções sobre como abrir a consola do Hive, consulte [utilizar o Hive com o Hadoop no HDInsight com o ambiente de trabalho remoto](apache-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Aplanar documentos JSON
Os métodos apresentados na secção seguinte requerem que o documento JSON ser composto por uma única linha. Por isso, tem aplanar o documento JSON numa cadeia. Se o documento JSON já é simplificado, pode ignorar este passo e ir diretamente para a secção seguinte no analisar dados JSON. Para aplanar o documento JSON, execute o seguinte script:

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

O ficheiro raw de JSON está localizado em  **wasb://processjson@hditutorialdata.blob.core.windows.net/** . O **StudentsRaw** pontos de tabela de ramo de registo para o documento JSON não processado que não é simplificado.

O **StudentsOneLine** tabela do Hive armazena os dados no sistema de ficheiros predefinido HDInsight sob o **/json/estudantes/** caminho.

O **inserir** instrução preenche a **StudentOneLine** tabela com os dados JSON simplificadas.

O **SELECIONE** instrução devolve apenas uma linha.

Eis o resultado a **SELECIONE** instrução:

![Pelo documento JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON no ramo de registo
Ramo de registo fornece três mecanismos diferentes para executar consultas em documentos JSON, ou pode escrever o seu próprio:

* Utilize a função no get_json_object definido pelo utilizador (UDF).
* Utilize o json_tuple UDF.
* Utilize o serializador/desserializador personalizado (SerDe).
* Escreva o seus próprios UDF utilizando o Python ou outros idiomas. Para obter mais informações sobre como executar o seu próprio código Python com o Hive, consulte [UDF de Python com Apache Hive e do Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Utilize o get_json_object UDF
Ramo de registo fornece um UDF incorporada, denominada [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que pode efetuar a consulta JSON durante o tempo de execução. Este método aceita dois argumentos – o nome da tabela e o nome de método, o que tem o documento JSON simplificado e o campo JSON que necessita para ser analisada. Vamos ver um exemplo para ver como funciona esta UDF.

A seguinte consulta devolve o nome próprio e apelido para cada estudante:

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

O resultado é ao executar esta consulta na janela da consola:

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Existem limitações do get_json_object UDF:

* Uma vez que cada campo na consulta requer reparsing da consulta, afeta o desempenho.
* **OBTER\_JSON_OBJECT()** devolve a representação de cadeia de uma matriz. Para converter esta matriz de uma matriz de ramo de registo, tem de utilizar expressões regulares para substituir os parênteses Retos "[" e "]", e, em seguida, também tem de chamar divisão para obter a matriz.

É por isso wiki do ramo de registo recomenda que utilize json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Utilize o json_tuple UDF
Outro UDF fornecida pelo ramo de registo é chamado [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que executa a melhor do que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método utiliza um conjunto de chaves e uma cadeia JSON e devolve uma cadeia de identificação de valores através da utilização de uma função. A seguinte consulta devolve o ID de estudante e o nível do documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

O resultado deste script na consola do ramo de registo:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

Json_tuple UDF utiliza o [lateral vista](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) sintaxe no ramo de registo, o que lhe permite json\_cadeias de identificação para criar uma tabela virtual aplicando a função UDT para cada linha da tabela original. JSONs complexas ficar demasiado unwieldy devido à utilização repetida de **vista LATERAL**. Além disso, **JSON_TUPLE** não consegue processar JSONs aninhadas.

### <a name="use-a-custom-serde"></a>Utilize um SerDe personalizado
SerDe é a melhor opção para analisar aninhados documentos JSON. Permite-lhe definir o esquema JSON e, em seguida, pode utilizar o esquema para analisar os documentos. Para obter instruções, consulte [como utilizar um SerDe JSON personalizado com o Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumo
No conclusion, o tipo de operador JSON no ramo de registo que escolher depende do seu cenário. Se tiver um documento JSON simple e tiver apenas um campo para procurar, pode optar por utilizar o Hive UDF get_json_object. Se tiver mais do que uma chave para procurar, em seguida, pode utilizar o json_tuple. Se tiver um documento aninhado, deve utilizar o SerDe JSON.

## <a name="next-steps"></a>Passos seguintes

Para artigos relacionados, consulte:

* [Utilizar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um ficheiro Apache log4j de exemplo](../hdinsight-use-hive.md)
* [Analisar dados de atraso do voo utilizando o Hive no HDInsight](../hdinsight-analyze-flight-delay-data.md)
* [Analisar dados do Twitter através do Hive no HDInsight](../hdinsight-analyze-twitter-data.md)
* [Executar uma tarefa de Hadoop, utilizando a BD do Cosmos do Azure e o HDInsight](../../cosmos-db/run-hadoop-with-hdinsight.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png


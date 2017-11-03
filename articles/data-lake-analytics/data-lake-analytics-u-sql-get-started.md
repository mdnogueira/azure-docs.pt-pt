---
title: "Introdução à linguagem U-SQL | Microsoft Docs"
description: "Aprender as noções básicas de linguagem U-SQL."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 38c4e1b9bd24ef0b8a81f6154620f3f98d3b5ac1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-u-sql"></a>Introdução ao U-SQL
U-SQL é uma linguagem que combina SQL declarativa com imperativo c# para informá-processar os dados em qualquer escala. Através da capacidade de dimensionável, consulta distribuído de U-SQL, pode de analisar eficazmente os dados em arquivos relacionais, tais como SQL Database do Azure. Com o U-SQL, pode processar dados não estruturados ao aplicar o esquema na leitura e a inserção lógica personalizada e UDFs. Além disso, o U-SQL inclui extensibilidade dá-lhe controlo detalhado sobre como executar à escala. 

## <a name="learning-resources"></a>Recursos de aprendizagem

* O [U-SQL Tutorial](http://aka.ms/usqltutorial) fornece instruções orientadas de maioria de linguagem U-SQL. Este documento é recomendado ler para todos os programadores intenção saber mais sobre U-SQL.
* Para obter informações detalhadas sobre o **sintaxe de linguagem U-SQL**, consulte o [referência de linguagem U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).
* Para compreender o **philosophy de design do U-SQL**, consulte a mensagem de blogue do Visual Studio [introdução de U-SQL – uma linguagem que facilita grande processamento de dados](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir os exemplos de U-SQL neste documento, leia e concluir [Tutorial: desenvolver scripts SQL-U utilizando ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Este tutorial explica mechanics de utilizando U-SQL com o Azure Data Lake Tools para Visual Studio.

## <a name="your-first-u-sql-script"></a>O seu primeiro script U-SQL

O seguinte script U-SQL é simple e permite-nos analisar vários aspetos a linguagem U-SQL.

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Este script não tem quaisquer passos de transformação. Também lê do ficheiro de origem chamado `SearchLog.tsv`, schematizes-lo e escreve o conjunto de linhas de volta para um ficheiro denominado SearchLog-first-u-sql.csv.

Tenha em atenção o ponto de interrogação junto os dados escreva a `Duration` campo. Significa que o `Duration` campo pode ser nulo.

### <a name="key-concepts"></a>Conceitos-chave
* **Variáveis de conjunto de linhas**: cada expressão de consulta que produz um conjunto de linhas pode ser atribuído a uma variável. U-SQL segue o padrão de nomenclatura de variável T-SQL (`@searchlog`, por exemplo) no script.
* O **EXTRAIR** palavra-chave lê dados a partir de um ficheiro e define o esquema na leitura. `Extractors.Tsv`é um incorporada extrator de U-SQL para os ficheiros de separador separados-valor. Pode desenvolver extractors personalizados.
* O **saída** escreve dados de um conjunto de linhas num ficheiro. `Outputters.Csv()`é um outputter incorporada do U-SQL para criar um ficheiro de valores separados por vírgulas. Pode desenvolver outputters personalizados.

### <a name="file-paths"></a>Caminhos de ficheiro

As declarações de EXTRAÇÃO e saída de utilizar caminhos de ficheiro. Caminhos de ficheiro podem ser absoluto ou relativo:

Este caminho absoluto do ficheiro seguinte refere-se para um ficheiro num com o nome do Data Lake Store `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Este caminho de ficheiro seguintes começa com `"/"`. Se refere a um ficheiro na conta do Data Lake Store predefinida:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Utilizar variáveis escalares

Pode utilizar variáveis escalares, bem como para facilitar a manutenção de script. O script U-SQL anterior também pode ser escrito como:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Conjuntos de linhas de transformação

Utilize **SELECIONE** para transformar conjuntos de linhas:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

A cláusula WHERE utiliza um [expressão c# booleana](https://msdn.microsoft.com/library/6a71f45d.aspx). Pode utilizar a linguagem de expressão c# para as suas próprias expressões e funções. Mesmo pode efetuar uma filtragem mais complexas de excelência combinando-los com conjunctions lógicas (ANDs) e disjunctions (ORs).

O script seguinte utiliza o método DateTime.Parse() e um conjunto.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >A segunda consulta está a funcionar no resultado do primeiro conjunto de linhas, que cria um compostos de dois filtros. Também pode reutilizar um nome de variável e os nomes estão abrangidas pelo âmbito lexically.

## <a name="aggregate-rowsets"></a>Conjuntos de linhas agregados
U-SQL dá-lhe o familiar ORDER BY, GROUP BY e agregações.

A consulta seguinte localiza a duração total por região e, em seguida, mostra as principais durações de cinco por ordem.

Conjuntos de linhas do U-SQL não conservar a sua ordem para a consulta seguinte. Assim, ordenar uma saída, tem de adicionar ORDER BY à declaração de saída:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

A cláusula ORDER BY U-SQL necessita de utilizar a cláusula de obtenção numa expressão SELECT.

A cláusula U-SQL que pode ser utilizada para restringir a saída para os grupos que satisfaçam a condição HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Para cenários avançados de agregação, consulte a documentação de referência o U-SQL para [agregar, análise e as funções de referência](https://msdn.microsoft.com/en-us/library/azure/mt621335.aspx)

## <a name="next-steps"></a>Passos seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)

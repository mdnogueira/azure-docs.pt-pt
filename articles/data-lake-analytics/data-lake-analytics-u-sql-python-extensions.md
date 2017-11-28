---
title: Expandir scripts U-SQL com o Python no Azure Data Lake Analytics | Microsoft Docs
description: "Saiba como executar código Python no Scripts U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/20/2017
ms.author: saveenr
ms.openlocfilehash: 51c0bac3cd03965d002b3fe87804a0d7ea6cf63e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="tutorial-get-started-with-extending-u-sql-with-python"></a>Tutorial: Introdução ao expandir o U-SQL com o Python

Extensões de Python do U-SQL permitem aos programadores efetuar paralela em grande escala execução do código de Python. O exemplo a seguir ilustra os passos básicos:

* Utilize o `REFERENCE ASSEMBLY` instrução para ativar extensões do Python para o Script U-SQL
* Utilizar o `REDUCE` operação para os dados de entrada numa chave de partição
* As extensões do Python para U-SQL incluem um reducer incorporada (`Extension.Python.Reducer`) que executa o código de Python em cada vértice atribuído para o reducer
* O script U-SQL contém o código de Python incorporado que tem uma função chamada `usqlml_main` que aceita um pandas DataFrame como entrada e devolve um pandas DataFrame como saída.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Como o Python integra U-SQL

### <a name="datatypes"></a>Tipos de dados

* Colunas de cadeia e numérica U-SQL são convertidas como-entre Pandas e U-SQL
* U-SQL nulos são convertidos para e da Pandas `NA` valores

### <a name="schemas"></a>Esquemas

* Os vetores de índice Pandas não são suportados em U-SQL. Todos os pacotes de dados de entrada na função Python sempre dispor de um índice numérico 64-bit entre 0 e o número de linhas menos 1. 
* U-SQL conjuntos de dados não podem ter nomes de coluna duplicados
* U-SQL conjuntos de dados nomes de colunas que não são cadeias. 

### <a name="python-versions"></a>Versões de Python
Apenas o Python 3.5.1 (compilado para o Windows) é suportado. 

### <a name="standard-python-modules"></a>Módulos do Python padrão
Todos os módulos de Python padrão são incluídos.

### <a name="additional-python-modules"></a>Módulos adicionais do Python
Para além das bibliotecas padrão do Python, bibliotecas de python frequentemente utilizadas várias estão incluídas:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Mensagens de exceção
Atualmente, uma exceção no código Python aparece como falha de vértice genérico. No futuro, as mensagens de erro da tarefa de U-SQL irão apresentar a mensagem de exceção do Python.

### <a name="input-and-output-size-limitations"></a>Entrada e saída limitações de tamanho
Cada vértice tem uma quantidade limitada de memória atribuída ao mesmo. Atualmente, esse limite é de 6 GB para uma AU. Porque o DataFrames de entrada e de saída tem de existir na memória no código Python, o tamanho total de entrada e de saída não pode exceder os 6 GB.

## <a name="see-also"></a>Consultar também
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilizar as funções de janela U-SQL para tarefas do Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)
* [Utilize as ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)

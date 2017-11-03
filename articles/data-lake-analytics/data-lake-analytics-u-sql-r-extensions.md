---
title: Expandir scripts U-SQL com R no Azure Data Lake Analytics | Microsoft Docs
description: "Saiba como executar o código do R no Scripts U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: sukvg
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/20/2017
ms.author: saveenr
ms.openlocfilehash: d479af515566f497d9611e75426f6acb8f8276d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-get-started-with-extending-u-sql-with-r"></a>Tutorial: Introdução ao expandir o U-SQL com R

O exemplo a seguir ilustra os passos básicos para implementar o código do R:
* Utilize o `REFERENCE ASSEMBLY` instrução para ativar extensões de R para o Script U-SQL.
* Utilize o` REDUCE` operação para os dados de entrada numa chave de partição.
* As extensões de R para U-SQL incluem um reducer incorporada (`Extension.R.Reducer`) que executa o código do R em cada vértice atribuído para o reducer. 
* Utilização de dedicado com o nome de pacotes de dados chamados `inputFromUSQL` e `outputToUSQL `respetivamente para transmitir dados entre U-SQL e R. entrada e saída DataFrame corrigidos nomes de identificadores (ou seja, os utilizadores não é possível alterar estes nomes predefinidos de entrada e saída DataFrame identificadores).

## <a name="embedding-r-code-in-the-u-sql-script"></a>Ao incorporar o código de R no script U-SQL

Pode inline o R code o script U-SQL, utilizando o parâmetro do comando do `Extension.R.Reducer`. Por exemplo, podem declarar o script do R como uma variável de cadeia e transmita-o como um parâmetro para o Reducer.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Manter o código do R num ficheiro separado e referencie-o script U-SQL

O exemplo seguinte ilustra uma utilização mais complexa. Neste caso, o código do R é implementado como um recurso que o script U-SQL.

Guarde este código de R como um ficheiro separado.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Utilize um script U-SQL para implementar este script do R com a instrução de implementar recursos.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Como R integra-U-SQL

### <a name="datatypes"></a>Tipos de dados
* Colunas de cadeia e numérica U-SQL são convertidas como-entre R DataFrame e U-SQL [tipos suportados: `double`, `string`, `bool`, `integer`, `byte`].
* O `Factor` datatype não é suportado em U-SQL.
* `byte[]`tem de ser serializada como um com codificação base64 `string`.
* Cadeias de U-SQL podem ser convertidas para fatores no código do R, depois de U-SQL criar dataframe entrada R ou definindo o parâmetro reducer `stringsAsFactors: true`.

### <a name="schemas"></a>Esquemas
* U-SQL conjuntos de dados não podem ter nomes de colunas duplicados.
* Os nomes das colunas de conjuntos de dados de U-SQL têm de ser cadeias.
* Os nomes de coluna tem de ser os mesmos scripts de R e U-SQL.
* Coluna só de leitura não pode ser parte dataframe a saída. Porque readonly colunas são automaticamente injetadas volta na tabela U-SQL, se faz parte do esquema de saída de UDO.

### <a name="functional-limitations"></a>Limitações funcionais
* Não é possível instanciar o motor de R duas vezes no mesmo processo. 
* Atualmente, U-SQL não suporta UDOs de combinação para a predição de modelos particionados gerados utilizando Reducer UDOs a utilizar. Os utilizadores podem declarar os modelos particionados como recurso e utilizá-las no Script de R (consulte o código de exemplo `ExtR_PredictUsingLMRawStringReducer.usql`)

### <a name="r-versions"></a>Versões de R
Apenas R 3.2.2 é suportada.

### <a name="standard-r-modules"></a>Padrão dos módulos R

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Entrada e saída limitações de tamanho
Cada vértice tem uma quantidade limitada de memória atribuída ao mesmo. Porque o DataFrames de entrada e de saída tem de existir na memória com o código do R, o tamanho total de entrada e de saída não pode exceder os 500 MB.

### <a name="sample-code"></a>Código de exemplo
Mais de código de exemplo está disponível na sua conta do Data Lake Store depois de instalar as extensões de análise avançadas do U-SQL. O caminho de código de exemplo mais: `<your_account_address>/usqlext/samples/R`. 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Implementar módulos R personalizados com o U-SQL

Em primeiro lugar, crie um módulo personalizado de R zip-lo e, em seguida, carregue o ficheiro de módulo personalizado de R zipped ao seu arquivo ADL. No exemplo, iremos irá carregar magittr_1.5.zip para a raiz da conta ADLS predefinido para a conta ADLA que estamos a utilizar. Depois de carregar o módulo para o arquivo ADL, declare-lo tal como utilizar o recurso de implementar para disponibilizá-lo no seu script U-SQL e a chamada `install.packages` instalá-la.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomePackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilizar as funções de janela U-SQL para tarefas do Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)

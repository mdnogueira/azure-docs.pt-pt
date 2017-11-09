---
title: Utilizando as capacidades de cognitivos U-SQL no Azure Data Lake Analytics | Microsoft Docs
description: Saiba como utilizar o intelligence das capacidades cognitivos em U-SQL
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: ec48a07af0aba78f2e508bad232f34102f0c2073
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Tutorial: Introdução com as capacidades cognitivos do U-SQL

## <a name="overview"></a>Descrição geral
As funcionalidades cognitivos para U-SQL permitem aos programadores utilize intelligence colocar os seus programas de macrodados. 

Estão disponíveis as seguintes capacidades de cognitivos:
* Imagens: Detetar faces
* Imagens: Detetar emoções
* Imagens: Detetar objetos (marcação)
* Imagens: OCR (reconhecimento de caráter optical)
* Texto: Extração de expressão de chave
* Texto: Análise de dados de sentimento

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>Como utilizar Cognitive no script U-SQL

O processo geral é simple:

* Utilize a instrução da referência de ASSEMBLAGEM para ativar as funcionalidades cognitivos para o Script U-SQL
* Utilizar o processo de um conjunto de linhas de entrada com um cognitivos UDO, para gerar uma saída conjunto de linhas

### <a name="detecting-objects-in-images"></a>Detetar objetos nas imagens

O exemplo seguinte mostra como utilizar as capacidades cognitivos para detetar objetos nas imagens.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
Para obter mais exemplos, observe o **amostras de U-SQL/Cognitive** no **passos** secção.

## <a name="next-steps"></a>Passos seguintes
* [Exemplos de U-SQL/cognitivos](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Desenvolver scripts U-SQL com as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Utilizar as funções de janela U-SQL para tarefas do Azure Data Lake Analytics](data-lake-analytics-use-window-functions.md)

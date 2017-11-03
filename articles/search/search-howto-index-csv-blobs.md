---
title: "A indexação de blobs CSV com o indexador de blob do Azure Search | Microsoft Docs"
description: "Saiba como índice blobs CSV pesquisa do Azure"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: ed3c9cff-1946-4af2-a05a-5e0b3d61eb25
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/15/2016
ms.author: eugenesh
ms.openlocfilehash: af9da85c37211d2436c23cc05400031c661ef51e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>A indexação de blobs CSV com o indexador de blob do Azure Search
Por predefinição, [indexador de blob do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa os blobs de texto delimitado por como um segmento de texto único. No entanto, com blobs que contém os dados CSV, muitas vezes, pretende tratar cada linha no blob como um documento separado. Por exemplo, dado o seguinte texto delimitado: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Pode querer analisá-lo para 2 documentos, cada que contém campos de "etiquetas", "datePublished" e "id".

Neste artigo, ficará a saber como analisar os blobs CSV com um indexador de blob do Azure Search. 

> [!IMPORTANT]
> Esta funcionalidade está atualmente em pré-visualização. Está disponível apenas na API REST utilizando a versão **pré-visualização 2015-02-28**. . Lembre-se, pré-visualizar APIs foram concebidas para avaliação e de teste e não deve ser utilizadas em ambientes de produção. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Como configurar a indexação de CSV
Índice de blobs CSV, criar ou atualizar uma definição de indexador com o `delimitedText` análise modo:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Para obter mais detalhes sobre a API de indexador criar, veja [criar indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders`indica que a primeira linha (não em branco) de cada blob contém os cabeçalhos.
Se os blobs não contém uma linha de cabeçalho inicial, os cabeçalhos devem ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Atualmente, é suportada apenas a codificação UTF-8. Além disso, apenas a vírgula `','` caráter é suportado como o delimitador. Se precisar de suporte para outras codificações ou delimitadores, indique no [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando utiliza o modo de análise de texto delimitado, da Azure Search parte do princípio de que todos os blobs na sua origem de dados será CSV. Se precisar de suportar uma mistura de blobs CSV e sem CSV na mesma origem de dados, indique no [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemplos de pedido
Colocar este todos os em conjunto, Eis os exemplos de payload completo. 

Origem de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Ajude-na tornar o melhor da Azure Search
Se tiver de pedidos de funcionalidades ou ideias para melhoramentos, contacte-no nosso [UserVoice site](https://feedback.azure.com/forums/263029-azure-search/).


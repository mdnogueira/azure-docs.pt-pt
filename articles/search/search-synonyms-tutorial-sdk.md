---
title: "Tutorial de pré-visualização de sinónimos no Azure Search | Microsoft Docs"
description: "Adicione a funcionalidade de pré-visualização de sinónimos a um índice no Azure Search."
services: search
manager: jhubbard
documentationcenter: 
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 03/31/2017
ms.author: heidist
ms.openlocfilehash: 014959ed471f796d2184f0f8ff10d15cdc8a2ec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="synonym-preview-c-tutorial-for-azure-search"></a>Tutorial de C# (pré-visualização) de sinónimos para o Azure Search

Os sinónimos expandem uma consulta, ao efetuar a correspondência em termos considerados semanticamente equivalentes ao termo introduzido. Por exemplo, poderá pretender que a palavra "carro" corresponda nos documentos que contêm os termos "automóvel" ou "veículo".

No Azure Search, os sinónimos são definidos num *mapa de sinónimos*, através de *regras de mapeamento* que associam termos equivalentes. Pode criar vários mapas de sinónimos, publicá-los como um recurso amplo de serviços disponível para qualquer índice e, em seguida, referenciar o que irá utilizar ao nível do campo. No momento da consulta, para além de procurar um índice, o Azure Search faz uma pesquisa num mapa de sinónimos, se um for especificado nos campos utilizados na consulta.

> [!NOTE]
> A funcionalidade de sinónimos está atualmente em pré-visualização e só é suportada na API de pré-visualização mais recente e versões do SDK (api-version=2016-09-01-Preview, SDK version 4.x-preview). Não existe suporte do portal do Azure neste momento. As APIs de pré-visualização não estão sob o SLA e as funcionalidades de pré-visualização podem ser alteradas, pelo que não é recomendado utilizá-las em aplicações de produção.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos de tutorial incluem o seguinte:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Serviço do Azure Search](search-create-service-portal.md)
* [Versão de pré-visualização da biblioteca Microsoft.Azure.Search.NET](https://aka.ms/search-sdk-preview)
* [Como utilizar o Azure Search a partir de uma Aplicação .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Descrição geral

As consultas de antes e após demonstram o valor dos sinónimos. Neste tutorial, utilizamos uma aplicação de exemplo que executa consultas e devolve resultados num índice de exemplo. A aplicação de exemplo cria um índice pequeno com o nome "hotéis" preenchido com dois documentos. A aplicação executa consultas de pesquisa com termos e expressões que não são apresentados no índice, ativa a funcionalidade de sinónimos e, em seguida, inicia as mesmas pesquisas novamente. O código abaixo demonstra o fluxo geral.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Os passos para criar e preencher o índice de exemplo serão explicados em [How to use Azure Search from a .NET Application (Como utilizar o Azure Search a partir de uma Aplicação .NET)](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Consultas "antes"

No `RunQueriesWithNonExistentTermsInIndex`, podemos emitir consultas de pesquisa com "cinco estrelas", "internet" e "economia E hotel".
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Nenhum dos dois documentos indexados contém os termos, pelo que vamos obter o seguinte resultado do primeiro `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Ativar sinónimos

A ativação dos sinónimos é um processo de dois passos. Em primeiro lugar, definimos e carregamos as regras de sinónimos e, em seguida, configuramos os campos para os utilizar. O processo está destacado em `UploadSynonyms` e em `EnableSynonymsInHotelsIndex`.

1. Adicione um mapa de sinónimos ao seu serviço de pesquisa. No `UploadSynonyms`, definimos quatro regras no nosso mapa de sinónimos "desc-synonymmap" e carregamos para o serviço.
```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
```
Um mapa de sinónimos tem de estar em conformidade para abrir o formato `solr` standard de origem. O formato é explicado em [Synonyms in Azure Search (Sinónimos no Azure Search)](search-synonyms.md) na secção `Apache Solr synonym format`.

2. Configure os campos pesquisáveis para utilizar o mapa de sinónimos na definição do índice. No `EnableSynonymsInHotelsIndex`, ativamos sinónimos em dois campos `category` e `tags`, ao definir a propriedade `synonymMaps` para o nome do mapa de sinónimos carregado recentemente.
```csharp
  Index index = serviceClient.Indexes.Get("hotels");
  index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
  index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

  serviceClient.Indexes.CreateOrUpdate(index);
```
Ao adicionar um mapa de sinónimos, as reconstruções do índice não são necessárias. Pode adicionar um mapa de sinónimos ao seu serviço e, em seguida, corrigir definições de campo existente em qualquer índice para utilizar o novo mapa de sinónimos. A adição de novos atributos não tem qualquer impacto na disponibilidade do índice. O mesmo aplica-se na desativação de sinónimos num campo. Pode definir simplesmente a propriedade `synonymMaps` numa lista vazia.
```csharp
  index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
```

## <a name="after-queries"></a>Consultas de "após"

Depois de o mapa de sinónimos ser carregado e o índice ser atualizado para utilizar o mapa de sinónimos, a segunda chamada `RunQueriesWithNonExistentTermsInIndex` produz o seguinte:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
A primeira consulta encontra o documento a partir da regra `five star=>luxury`. A segunda consulta expande a pesquisa com `internet,wifi` e a terceira com `hotel, motel` e `economy,inexpensive=>budget` ao encontrar os documentos que corresponderam.

Adicionar sinónimos altera completamente a experiência de pesquisa. Neste tutorial, as consultas originais não conseguiram devolver resultados com significado, apesar de os documentos no nosso índice serem relevantes. Ao ativar os sinónimos, podemos expandir um índice para incluir termos na utilização comum, sem alterações aos dados subjacentes no índice.

## <a name="sample-application-source-code"></a>Código de origem da aplicação de exemplo
Pode encontrar o código de origem completo da aplicação de exemplo utilizado nesta apresentação no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="next-steps"></a>Passos seguintes

* Consulte [How to use synonyms in Azure Search (Como utilizar sinónimos no Azure Search)](search-synonyms.md)
* Consulte [Synonyms REST API documentation (Documentação da API REST de sinónimos)](https://aka.ms/rgm6rq)
* Procure as referências para o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e a [API REST](https://docs.microsoft.com/rest/api/searchservice/).

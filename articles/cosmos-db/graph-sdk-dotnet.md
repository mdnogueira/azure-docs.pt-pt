---
title: SDK .NET da Azure CosmosDB Graph API & recursos | Microsoft Docs
description: "Saiba tudo sobre o Azure CosmosDB Graph API, incluindo as datas de versão, as datas de extinção e as alterações efetuadas entre cada versão."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: mimig
ms.openlocfilehash: 7d6ba5794e4a3e431abd72a780b60b9e59e9f4db
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Azure Cosmos DB Graph API do .NET: Transferir e notas de versão

|   |   |
|---|---|
|**Transferência do SDK**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**Documentação da API**|[Documentação de referência da API de .NET](https://aka.ms/acdbgraphapiref)|
|**Início rápido**|[Azure Cosmos DB: Criar uma aplicação de gráfico com o .NET e a Graph API](create-graph-dotnet.md)|
|**Tutorial**|[Azure CosmosDB: Criar um contentor com a Graph API](tutorial-develop-graph-dotnet.md)|
|**Arquitetura suportada atual**| [O Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [O Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Notas de versão

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-Preview

#### <a name="bug-fixes"></a>Correções de erros
* Corrigir opcionalmente carregar `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>Novidades
* Mude Microsoft.Azure.Graphs para AnyCPU da plataforma de destino.
* Remover uma assemblagem Mono da `net461` manifesto do pacote.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-Preview

#### <a name="whats-new"></a>Novidades
* Suporte adicionado para`.netstandard 1.6`
  * Requer`Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Adicionar um novo `gremlin-groovy` parser para substituir o parser existente. Este parser suporta um subconjunto do Tinkerpop `gremlin-groovy` sintaxe e inclui:
  * Melhorado análise de desempenho por 2 x.
  * Resolver um número de problemas relacionados com o caráter de escape em cadeias, valores literais incorretamente processados e outros irregularities no parser de antigo.
* Foram adicionadas otimizações para traversals com predicados de limite.
  *  Saltos Traversal com filtros deverá ver este melhoramento, por exemplo: `g.V('1').outE().has('name', 'marko').inV()`.
* Adicionar otimizações para traversals com `limit()` passo.

#### <a name="breaking-changes"></a>As alterações de última hora
* Suporte removido para o .NET Framework 4.5.1

* O novo parser está alinhada com `gremlin-groovy` gramática. Como resultado, alguns expressões que funcionavam anteriormente são ambíguas para o parser de novo. Um cenário de Nota:
  * `in`e `as` são palavras-chave reservadas no `gremlin-groovy`, pelo que estes passos, devem estar qualificados com `.in()` ou `.as()` para evitar erros de sintaxe. Por exemplo: `g.V().repeat(in()).times(2)`  ->  _emite um erro de sintaxe_  
 `g.V().repeat(__.in()).times(2)` -> _será efetuada com êxito_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-Preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-Preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-Preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-Preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview
* Versão de pré-visualização inicial.

## <a name="release--retirement-dates"></a>Versão & extinção datas
A Microsoft vai fornecer pelo menos notificação **12 meses** previamente extinguir um SDK para smooth a transição para uma versão mais recente/suportado.

Novas funcionalidades e a funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal, recomenda-se que atualize sempre para a versão mais recente SDK como antecipadamente quanto possível. 

Qualquer pedido de BD do Cosmos do Azure utilizando um SDK extinto será rejeitado pelo serviço.

<br/>

| Versão | Data da versão | Data de retirada |
| --- | --- | --- |
| [0.3.1-Preview](#0.3.1-preview) |17 de Outubro de 2017 |--- |
| [0.3.0-Preview](#0.3.0-preview) |2 de Outubro de 2017 |--- |
| [0.2.4-Preview](#0.2.4-preview) |4 de Agosto de 2017 |--- |
| [0.2.2-Preview](#0.2.2-preview) |23 de Junho de 2017 |--- |
| [0.2.1-Preview](#0.2.2-preview) |8 de Junho de 2017 |--- |
| [0.2.0-Preview](#0.2.2-preview) |10 de maio de 2017 |--- |
| [0.1.0-Preview](#0.1.0-preview) |8 de Maio de 2017 |--- |

## <a name="see-also"></a>Consultar também
Para saber mais sobre a Graph API do Azure Cosmos DB, consulte o artigo [introdução à base de dados do Azure Cosmos: Graph API](graph-introduction.md). 

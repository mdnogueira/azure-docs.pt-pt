---
title: "Como dados de gráfico de consulta na base de dados do Azure Cosmos? | Microsoft Docs"
description: "Saiba como consultar dados de gráfico do BD Azure Cosmos"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
tags: 
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: denlee
ms.openlocfilehash: ee420983104f199edf57966bf22aa4d8451b4782
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-cosmos-db-how-to-query-with-the-graph-api-preview"></a>Azure Cosmos DB: Como consultar com a Graph API (pré-visualização)?

A BD do Cosmos Azure [Graph API](graph-introduction.md) (pré-visualização) suporta [Gremlin](https://github.com/tinkerpop/gremlin/wiki) consultas. Este artigo fornece documentos de exemplo e consultas para começar. A detalhadas Gremlin referência é fornecida no [suporte Gremlin](gremlin-support.md) artigo.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Consultar os dados com Gremlin

## <a name="prerequisites"></a>Pré-requisitos

Para estas consultas funcione, tem de ter uma conta de base de dados do Azure Cosmos e ter dados de gráfico no contentor. Não tem qualquer um desses? Concluir o [início rápido de 5 minutos](create-graph-dotnet.md) ou [tutorial programador](tutorial-query-graph.md) para criar uma conta e preencher a base de dados. Pode executar as seguintes consultas utilizando o [biblioteca de gráfico de .NET de BD do Azure Cosmos](graph-sdk-dotnet.md), [consola Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), ou o controlador de Gremlin favorito.

## <a name="count-vertices-in-the-graph"></a>Contagem de vértices no gráfico

O fragmento seguinte mostra como contabilizar o número de vértices no gráfico de:

```
g.V().count()
```

## <a name="filters"></a>Filtros

Pode efetuar filtros através do Gremlin `has` e `hasLabel` passos e combine-os utilizando `and`, `or`, e `not` para criar filtros mais complexos. BD do Azure do Cosmos fornece desconhecidas do esquema de indexação de todas as propriedades dentro da sua vértices e graus para consultas rápidas:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projeção

Pode projetar a algumas propriedades nos resultados da consulta utilizando o `values` passo:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Localizar contornos relacionados e vértices

Até ao momento, estamos apenas viu operadores de consulta que funcionam em qualquer base de dados. Gráficos são rápidos e eficientes para operações de transversal quando precisar navegar para contornos relacionados e vértices. Vamos localizar todos os amigos de blogue. Podemos fazê-lo através do Gremlin `outE` passo para localizar todas as de out-extremidades do blogue, em seguida, que atravessa no-vértices desses contornos através do Gremlin `inV` passo:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

A consulta seguinte executa duas saltos para localizar todos os "amigos de blogue de amigos", chamando `outE` e `inV` duas vezes. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Pode construir consultas mais complexas e implementar a lógica do gráfico poderosas transversal utilizando Gremlin, incluindo a mistura de expressões de filtro, efetuar a utilizar o ciclo de `loop` passo e a utilização de navegação condicional implementar o `choose` passo. Saiba mais sobre o que pode fazer com [suporte Gremlin](gremlin-support.md)!

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Aprendeu a consultar com gráfico 

Agora pode avançar para o próximo tutorial para saber como distribuir dados globalmente.

> [!div class="nextstepaction"]
> [Distribuir dados globalmente](tutorial-global-distribution-documentdb.md)

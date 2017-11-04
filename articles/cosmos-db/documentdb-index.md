---
title: 'Azure Cosmos DB: Artigos DocumentDB API | Microsoft Docs'
description: "Uma lista de todos os artigos específicos para a criação de bases de dados de documento com a API do DocumentDB do BD Azure Cosmos."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 82bec99a-ac2b-474e-b41f-d2fb296c8feb
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: mimig
ms.openlocfilehash: ce24e445177b7e2e499309f016478030efa6bfe8
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="azure-cosmos-db-documentdb-api-documentation"></a>Do Azure Cosmos DB: Documentação do DocumentDB API

Este artigo fornece ligações para todos os conteúdos da base de dados do Azure Cosmos específico para a API do DocumentDB.

Estes artigos não se aplicam para a Graph API, a API de tabela ou a API do MongoDB. 

## <a name="introduction-and-concepts"></a>Introdução e conceitos

Estes são tem leia os tópicos e recursos para começar a aprender mais sobre a API do DocumentDB para a base de dados do Azure Cosmos.

- [Introdução de API do DocumentDB](documentdb-introduction.md)
- [Modelo de recursos de base de dados](documentdb-resources.md)
- Web site: [playground de consulta](https://www.documentdb.com/sql/demo)
- Cheat folha: [gramática do SQL Server](documentdb-sql-query-cheat-sheet.md)

## <a name="quickstarts"></a>Inícios rápidos

Os tópicos do guia de introdução são a forma mais rápida para criar uma aplicação de trabalho com base de dados do Azure Cosmos. Cada guia de introdução, irá aprender a utilizar baseadas na IU do portal do Azure e o favorito linguagem de programação para criar soluções de base de dados com base de dados do Azure Cosmos. Aplicações web clonáveis da área de GitHub disponível para cada início rápido. 

- [.NET + portal do azure + aplicações Web](create-documentdb-dotnet.md)
- [Java + portal do Azure + aplicações Web](create-documentdb-java.md)
- [NODE.js + portal do Azure + aplicações Web](create-documentdb-nodejs.md)
- [Python + portal do Azure + aplicações Web](create-documentdb-python.md)
- [Xamarin + .NET + portal do Azure + aplicações Web](create-documentdb-xamarin-dotnet.md)

## <a name="tutorials"></a>Tutoriais

Estes tutoriais são um nível mais profundo do que os inícios rápidos. Nos tutoriais, criar aplicações a partir do zero e copie e cole o código da aplicação. Também irá aprender como importar dados, consultar dados e distribuir as bases de dados globalmente.

### <a name="create-a-web-app"></a>Criar uma aplicação Web

- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md) 
- [Java](documentdb-java-application.md)
- [python](documentdb-python-application.md)

### <a name="create-a-console-app"></a>Criar uma aplicação de consola

- [.NET](documentdb-get-started.md)
- [.NET Core](documentdb-dotnetcore-get-started.md) 
- [Java](documentdb-java-get-started.md) 
- [Node.js](documentdb-nodejs-get-started.md) 
- [C++](documentdb-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>Criar uma aplicação móvel

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>Trabalhar com dados

- [Importar dados](import-data.md)
- [Consulta](tutorial-query-documentdb.md)
- [Distribuir dados globalmente](tutorial-global-distribution-documentdb.md)

### <a name="work-with-azure-functions"></a>Trabalhar com as funções do Azure

- [Criar um acionador de base de dados do Azure Cosmos](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [Utilizar base de dados do Azure Cosmos na entrada e saída enlaces](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>Guia de programadores

- [Consultas SQL](documentdb-sql-query.md)
- [Criação de partições](documentdb-partition-data.md)
- [Programação do lado do servidor: e procedimentos armazenados, acionadores de base de dados, UDFs](programming.md)
- [Teste de desempenho](performance-testing.md)
- [Sugestões de desempenho](performance-tips.md)
- [Configuração com múltiplos principais](multi-region-writers.md)
- [DateTimes](working-with-dates.md)
- [Dados de documento de modelação](modeling-data.md) 

## <a name="sdks"></a>SDKs

BD do Cosmos do Azure fornece um número de SDKs para ativar o desenvolvimento de aplicações do lado do cliente.

- [Java](documentdb-sdk-java.md)
- [.NET](documentdb-sdk-dotnet.md)
- [Feed de alteração do .NET](documentdb-sdk-dotnet-changefeed.md)
- [.NET Core](documentdb-sdk-dotnet-core.md)
- [Node.js](documentdb-sdk-node.md)
- [python](documentdb-sdk-python.md)

## <a name="reference"></a>Referência

- [REST](/rest/api/documentdb/)
- [Fornecedor de Recursos REST](/rest/api/documentdbresourceprovider/)
- [Referência de consulta SQL](documentdb-sql-query-reference.md)
- [Referência de funções do Azure](../azure-functions/functions-bindings-documentdb.md)

## <a name="samples"></a>Amostras

Estas páginas de exemplo fornecem ligações para o código de exemplo e API fazer referência a conteúdo para as tarefas mais comuns de API do DocumentDB.

- [.NET](documentdb-dotnet-samples.md)
- [Node.js](documentdb-nodejs-samples.md)
- [python](documentdb-python-samples.md) 
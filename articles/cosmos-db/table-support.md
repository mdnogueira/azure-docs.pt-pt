---
title: Suporte de Table Storage do Azure do BD Azure Cosmos | Microsoft Docs
description: Saiba como API de tabela de base de dados do Azure Cosmos e tabelas de armazenamento do Azure funcionam em conjunto.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: c0c8f1aee75c4ee5cc35758b71ef573637fd3edd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Programar com o armazenamento de API de tabela de base de dados do Azure Cosmos e tabelas do Azure

API de tabela de base de dados do Azure Cosmos e Table storage do Azure partilham o mesmo modelo de dados de tabela e expõem o mesmo criar, eliminar, atualização e operações de consulta através do respetivos SDKs. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Desenvolver com o Azure Cosmos API de tabela de base de dados

Neste momento, o [API de tabela de base de dados do Azure Cosmos](table-introduction.md) tem quatro SDKs disponíveis para o desenvolvimento: 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK. Esta biblioteca tem o mesmas classes e assinaturas do método que público [SDK de armazenamento do Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage), mas também tem a capacidade de ligar a contas de Azure Cosmos BD utilizando a API de tabela. 
- [Python SDK](table-sdk-python.md). O novo Azure Cosmos DB Python SDK é apenas SDK que suporte o Table storage do Azure no Python. Este SDK liga ao Table storage do Azure e API de tabela de base de dados do Azure Cosmos.
- [Java SDK](table-sdk-java.md). Este SDK de armazenamento do Azure tem capacidade para ligar a contas de Azure Cosmos BD utilizando a API de tabela.
- [NODE.js SDK](table-sdk-nodejs.md). Este SDK de armazenamento do Azure tem capacidade para ligar a contas de Azure Cosmos BD utilizando a API de tabela.

Estão disponíveis informações adicionais sobre como trabalhar com a API de tabela no [FAQ: desenvolver com a API de tabela](faq.md#develop-with-the-table-api) artigo.

## <a name="developing-with-the-azure-table-storage"></a>Desenvolver com o Table storage do Azure

[Table storage do Azure](table-storage-overview.md) tem muitas SDKs disponíveis e tutoriais disponíveis, todos os que estão agora disponíveis no [Table storage do Azure](table-storage-overview.md) secção. Estes artigos estão a ser atualizados como interoperabilidade entre o Table storage do Azure SDKs e APIs de tabela de base de dados do Azure Cosmos ficar disponível.  






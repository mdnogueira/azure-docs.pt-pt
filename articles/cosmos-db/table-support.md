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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Programar com o armazenamento de API de tabela de base de dados do Azure Cosmos e tabelas do Azure

API de tabela de base de dados do Azure Cosmos e Table storage do Azure partilham o mesmo modelo de dados de tabela e expõem o mesmo criar, eliminar, atualização e operações de consulta através do respetivos SDKs. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Desenvolver com o Azure Cosmos API de tabela de base de dados

Neste momento, o [API de tabela de base de dados do Azure Cosmos](table-introduction.md) tem um SDK .NET disponível, o [tabelas de Premium do armazenamento do Azure (pré-visualização)](https://aka.ms/premiumtablenuget). Esta biblioteca tem o mesmas classes e assinaturas do método que público [SDK de armazenamento do Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage), mas também tem a capacidade de ligar a contas de Azure Cosmos BD utilizando a API de tabela (pré-visualização). Para um início rápido e tutorial utilizando a API de tabela de base de dados do Azure Cosmos, consulte os artigos seguintes:
- Início rápido: [BD do Azure do Cosmos: criar uma aplicação .NET através da API de tabela](create-table-dotnet.md)
- Tutorial: [Cosmos BD do Azure: desenvolver com a API de tabela no .NET](tutorial-develop-table-dotnet.md)

Estão disponíveis informações adicionais sobre como trabalhar com a API de tabela no [FAQ: desenvolver com a API de tabela](faq.md#develop-with-the-table-api-preview) artigo.

## <a name="developing-with-the-azure-table-storage"></a>Desenvolver com o Table storage do Azure

[Table storage do Azure](table-storage-overview.md) tem muitas SDKs disponíveis e tutoriais disponíveis, todos os que estão agora disponíveis no [Table storage do Azure](table-storage-overview.md) secção. Estes artigos estão a ser atualizados como interoperabilidade entre o Table storage do Azure SDKs e APIs de tabela de base de dados do Azure Cosmos ficar disponível.  






---
title: Amostras da CLI do Azure para o Azure Cosmos DB | Microsoft Docs
description: "Exemplos CLI do Azure - criar e gerir contas de Azure Cosmos DB, bases de dados, contentores, regiões e firewalls."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 06/07/2017
ms.author: mimig
ms.openlocfilehash: 189034b049e776a3b929930be937ec60bc1db241
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemplos da CLI do Azure para a base de dados do Azure Cosmos

A tabela seguinte inclui ligações para scripts da CLI do Azure de exemplo para a base de dados do Azure Cosmos. Estão disponíveis nas páginas de referência para todos os comandos da CLI do Azure Cosmos DB o [Azure CLI 2.0 referência](https://docs.microsoft.com/cli/azure/cosmosdb).

| |  |
|---|---|
|**Criar conta de base de dados do Azure Cosmos, base de dados e contentores**||
|[Criar uma conta DocumentDB API, gráfico ou tabela API](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma única conta de API de BD do Cosmos do Azure, base de dados e um contentor para utilização com o DocumentDB, gráfico ou APIs de tabela. |
| [Criar uma conta de API do MongoDB](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma única API do Azure Cosmos DB MongoDB conta, base de dados e coleção. |
|**Dimensionar Cosmos BD do Azure**||
| [Débito do contentor de escala](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Altera o througput aprovisionado num contentor.|
|[Replicar a conta de base de dados de base de dados do Azure Cosmos em várias regiões e configurar as prioridades de ativação pós-falha](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Globalmente replica os dados de conta em várias regiões, com uma prioridade de ativação pós-falha especificado.|
|**Proteger Cosmos BD do Azure**||
| [Obter chaves de conta](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtém as chaves primária e secundária escrita principal e chaves primária e secundária só de leitura para a conta.|
| [Obter a cadeia de ligação do MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Obtém a cadeia de ligação para ligar a aplicação do MongoDB à sua conta de base de dados do Azure Cosmos.|
|[Voltar a gerar chaves de conta](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|A chave mestra ou só de leitura para a conta de gera de novo.|
|[Criar uma firewall](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma política de controlo de acesso IP entrada para limitar o acesso à conta de um conjunto de máquinas aprovado e/ou serviços em nuvem.|
|**Elevada disponibilidade, a recuperação após desastre, a cópia de segurança e restauro**||
|[Configurar a política de ativação pós-falha](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Define a prioridade de ativação pós-falha de cada região na qual a conta é replicada.|
|**Ligar a base de dados do Azure Cosmos recursos**||
|[Ligar uma aplicação web à base de dados do Azure Cosmos](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Criar e ligar uma base de dados de base de dados do Azure Cosmos e uma aplicação web do Azure.|
|||

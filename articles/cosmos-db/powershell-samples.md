---
title: Exemplos do Azure PowerShell para Azure Cosmos DB | Microsoft Docs
description: Exemplos do PowerShell do Azure - Scripts para o ajudar a criar e gerir contas de base de dados do Azure Cosmos.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 10/16/2017
ms.author: mimig
ms.openlocfilehash: f2687369b8e247f00e9de5f3f79d8e44be5b8300
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemplos do PowerShell do Azure para a base de dados do Azure Cosmos

A tabela seguinte inclui ligações para os scripts de Azure PowerShell de exemplo para a base de dados do Azure Cosmos. Neste momento, só pode gerir o accountlayer de BD do Cosmos Azure através do PowerShell; outros recursos, tais como bases de dados e de coleções não podem ser geridos através do PowerShell.

| |  |
|---|---|
|**Criar uma conta de base de dados do Azure Cosmos**||
|[Criar uma conta de DocumentDB API](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma única conta de base de dados do Azure Cosmos para utilizar com a API do DocumentDB. |
|**Dimensionar Cosmos BD do Azure**||
|[Replicar a conta de base de dados do Azure Cosmos em várias regiões e configurar as prioridades de ativação pós-falha](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Globalmente replica os dados de conta em várias regiões, com uma prioridade de ativação pós-falha especificado.|
|**Proteger Cosmos BD do Azure**||
| [Obter chaves de conta](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtém as chaves primária e secundária escrita principal e chaves primária e secundária só de leitura para a conta.|
| [Obter a cadeia de ligação do MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtém a cadeia de ligação para ligar a aplicação do MongoDB à sua conta de base de dados do Azure Cosmos.|
|[Voltar a gerar chaves de conta](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|A chave mestra ou só de leitura para a conta de gera de novo.|
|[Criar uma firewall](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma política de controlo de acesso IP entrada para limitar o acesso à conta de um conjunto de máquinas aprovado e/ou serviços em nuvem.|
|**Elevada disponibilidade, a recuperação após desastre, a cópia de segurança e restauro**||
|[Configurar a política de ativação pós-falha](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Define a prioridade de ativação pós-falha de cada região na qual a conta é replicada.|
|||

---
title: Exemplos do PowerShell do Azure - Service Fabric | Microsoft Docs
description: Exemplos do PowerShell do Azure - Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: rwike77
manager: timlt
editor: 
tags: 
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 738677def8b0eb70cfcab46e6fe57f9a344867a5
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-powershell-samples"></a>Exemplos do Azure PowerShell

A tabela seguinte inclui ligações para exemplos de scripts do PowerShell que criar e gerem clusters, aplicações e serviços do Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Criar cluster** ||
| [Criar um cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Cria um cluster do Service Fabric do Azure. |
| **Gerir o cluster, nós e infraestrutura** ||
| [Adicionar um certificado de aplicação](./scripts/service-fabric-powershell-add-application-certificate.md)| Adiciona um certificado x. 509 de aplicação para todos os nós num cluster. |
| [Atualizar o intervalo de portas RDP no cluster VMs](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Altera o intervalo de portas RDP no nó de cluster VMs num cluster implementado.|
| [Atualizar o utilizador de admin e a palavra-passe para as VMs do nó de cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Atualiza o nome de utilizador de administrador e a palavra-passe para o nó de cluster VMs. |
| [Abrir uma porta no balanceador de carga](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Abra uma porta de aplicação no balanceador de carga do Azure para permitir tráfego de entrada numa porta específica. |
| [Criar uma regra de grupo de segurança de rede de entrada](./scripts/service-fabric-powershell-add-nsg-rule.md) | Crie uma regra de grupo de segurança de rede de entrada para permitir tráfego de entrada para o cluster numa porta específica. |
| **Gerir aplicações** ||
| [Implementar uma aplicação](./scripts/service-fabric-powershell-deploy-application.md)| Implemente uma aplicação para um cluster.|
| [Atualizar uma aplicação](./scripts/service-fabric-powershell-upgrade-application.md)| Atualize uma aplicação.|
| [Remover uma aplicação](./scripts/service-fabric-powershell-remove-application.md)| Remova uma aplicação de um cluster.|

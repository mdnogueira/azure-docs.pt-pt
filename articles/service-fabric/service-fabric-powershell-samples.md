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
ms.openlocfilehash: ae132dbb650e08c3a25a9366563e70c6d56e089d
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
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
| **Gerir clusters e nós** ||
| [Adicionar um certificado de aplicação](./scripts/service-fabric-powershell-add-application-certificate.md)| Adiciona um certificado x. 509 de aplicação para todos os nós num cluster. |
|[Alterar o intervalo de portas RDP no nó de cluster VMs](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Altera o intervalo de portas RDP no nó de cluster VMs num cluster implementado.|
| [Atualizar o utilizador de admin e a palavra-passe para as VMs do nó de cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Atualiza o nome de utilizador de administrador e a palavra-passe para o nó de cluster VMs. |
| **Gerir aplicações** ||
| [Implementar uma aplicação](./scripts/service-fabric-powershell-deploy-application.md)| Implemente uma aplicação para um cluster.|
| [Atualizar uma aplicação](./scripts/service-fabric-powershell-upgrade-application.md)| Atualizar uma aplicação |
| [Remover uma aplicação](./scripts/service-fabric-powershell-remove-application.md)| Remova uma aplicação de um cluster.|
| [Abrir uma porta no balanceador de carga](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Abra uma porta de aplicação no balanceador de carga do Azure. |

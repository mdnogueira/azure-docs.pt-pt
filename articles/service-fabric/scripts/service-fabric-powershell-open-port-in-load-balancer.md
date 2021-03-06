---
title: Exemplo de Script do PowerShell do Azure - porta abrir o application no balanceador de carga | Microsoft Docs
description: "Azure PowerShell Script de exemplo - abrir uma porta no balanceador de carga do Azure para uma aplicação de Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 08/15/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 9dbb0bedd02752c4735ae097a7bd64b7b5383d6e
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Abrir uma porta de aplicação no balanceador de carga do Azure

Uma aplicação de Service Fabric em execução no Azure se encontra por trás do Balanceador de carga do Azure. Este script de exemplo abre uma porta no balanceador de carga do Azure para que uma aplicação de Service Fabric pode comunicar com clientes externos. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo do PowerShell de Service Fabric com o [SDK de Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtém um recurso do Azure.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Obtém o Balanceador de carga do Azure. |
| [AzureRmLoadBalancerProbeConfig adicionar](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Adiciona uma configuração de pesquisa para um balanceador de carga.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Obtém uma configuração de pesquisa para um balanceador de carga. |
| [AzureRmLoadBalancerRuleConfig adicionar](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Adiciona uma configuração de regra a um balanceador de carga. |
| [Conjunto AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Define o estado de objetivos para um balanceador de carga. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos do Powershell adicionais para o Azure Service Fabric podem ser encontrados no [exemplos do PowerShell do Azure](../service-fabric-powershell-samples.md).

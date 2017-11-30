---
title: "Script do PowerShell do Azure de exemplo - adicionar uma regra de grupo de segurança de rede | Microsoft Docs"
description: "Exemplo de Script do PowerShell do Azure - adiciona um grupo de segurança de rede para permitir tráfego de entrada numa porta específica."
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="add-an-inbound-network-security-group-rule"></a>Adicionar uma regra de grupo de segurança de rede de entrada

Este script de exemplo cria uma regra de grupo de segurança de rede para permitir tráfego de entrada na porta 8081.  O script obtém o `Microsoft.Network/networkSecurityGroups` recursos que o cluster está localizado na, cria uma nova regra de configuração de segurança de rede e atualiza o grupo de segurança de rede. Personalize os parâmetros conforme necessário.

Se necessário, instale o Azure PowerShell, utilizando as instruções encontradas no [Guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtém o `Microsoft.Network/networkSecurityGroups` recursos. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Obtém o grupo de segurança de rede por nome.|
|[AzureRmNetworkSecurityRuleConfig adicionar](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
|[Conjunto AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Define o estado de objetivos para um grupo de segurança de rede.|

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

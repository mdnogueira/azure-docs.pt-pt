---
title: "Exemplo de Script do PowerShell do Azure - criar uma aplicação web e implementar o código para um ambiente de teste | Microsoft Docs"
description: "Exemplo de Script do PowerShell do Azure - criar uma aplicação web e implementar o código para um ambiente de teste"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 55adc13350eb0f4711efa3c901f6e4e7755dfb27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Criar uma aplicação web e implementar o código para um ambiente de teste

Este script de exemplo cria uma aplicação web no App Service, com um bloco de implementação adicionais denominado "testes" e, em seguida, implementa uma aplicação de exemplo para a ranhura de "transição".

Se necessário, instale o Azure PowerShell com a instrução foi encontrada no [Guia do Azure PowerShell](/powershell/azure/overview)e, em seguida, execute `Login-AzureRmAccount` para criar uma ligação com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover o grupo de recursos, aplicação web e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Novo AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Cria um plano de serviço de aplicações. |
| [Novo AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Cria uma aplicação web. |
| [Conjunto AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Modifica um plano de serviço de aplicações para alterar o escalão de preço. |
| [Novo AzureRmWebAppSlot](/powershell/module/azurerm.websites/new-azurermwebappslot) | Cria uma ranhura de implementação para uma aplicação web. |
| [Conjunto AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modifica um recurso num grupo de recursos. |
| [Comutação AzureRmWebAppSlot](/powershell/module/azurerm.websites/swap-azurermwebappslot) | Troca a ranhura de implementação de uma aplicação web em produção. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos do Azure Powershell adicionais para Web Apps do Azure App Service podem ser encontrados no [exemplos do PowerShell do Azure](../app-service-powershell-samples.md).

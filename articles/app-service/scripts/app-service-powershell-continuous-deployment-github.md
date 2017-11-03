---
title: "Script do PowerShell do Azure de exemplo - criar uma aplicação web com a implementação contínua do GitHub | Microsoft Docs"
description: "Script do PowerShell do Azure de exemplo - criar uma aplicação web com a implementação contínua do GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f0574c8c549c45682206624ffe38a4a3919cb1c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Criar uma aplicação web com a implementação contínua do GitHub

Este script de exemplo cria uma aplicação web no App Service com respetivos recursos relacionados e, em seguida, configura a implementação contínua provém de um repositório do GitHub. Para a implementação do GitHub sem a implementação contínua, consulte [criar uma aplicação web e implementar código a partir do GitHub](app-service-powershell-deploy-github.md).

Se necessário, instale o Azure PowerShell com a instrução foi encontrada no [Guia do Azure PowerShell](/powershell/azure/overview)e, em seguida, execute `Login-AzureRmAccount` para criar uma ligação com o Azure. Além disso, certifique-se de que:

- Foi criada uma ligação com o Azure utilizando o `az login` comando.
- Código da aplicação está a ser um repositório do GitHub público ou privado que é proprietário.
- Tiver [criado um token de acesso na sua conta GitHub](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

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
| [Conjunto AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modifica um recurso num grupo de recursos. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos do Azure Powershell adicionais para Web Apps do Azure App Service podem ser encontrados no [exemplos do PowerShell do Azure](../app-service-powershell-samples.md).

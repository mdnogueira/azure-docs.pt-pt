---
title: "Exemplo de Script do PowerShell do Azure - eliminar uma cópia de segurança para uma aplicação web | Microsoft Docs"
description: "Exemplo de Script do PowerShell do Azure - eliminar uma cópia de segurança para uma aplicação web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f4204cbb4aefe590b87d0a72675823321f280f33
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="delete-a-backup-for-a-web-app"></a>Eliminar uma cópia de segurança para uma aplicação web

Este script de exemplo cria uma aplicação web no App Service com os respetivos recursos relacionados e, em seguida, cria uma única cópia de segurança para o mesmo. 

Para executar este script, precisa de uma cópia de segurança existente para uma aplicação web. Para criar um, consulte [cópia de segurança se uma aplicação web](app-service-powershell-backup-onetime.md) ou [criar uma cópia de segurança agendada para uma aplicação web](app-service-powershell-backup-scheduled.md).

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover o grupo de recursos, aplicação web e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Obtém uma lista de cópias de segurança para uma aplicação web. |
| [Remover AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Remove a cópia de segurança especificada de uma aplicação web. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos do Azure Powershell adicionais para Web Apps do Azure App Service podem ser encontrados no [exemplos do PowerShell do Azure](../app-service-powershell-samples.md).

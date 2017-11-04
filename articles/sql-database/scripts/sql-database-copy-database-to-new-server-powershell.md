---
title: "Do novo para a base de dados do SQL Server de exemplo-cópia-Azure PowerShell | Microsoft Docs"
description: Script de exemplo do PowerShell do Azure para copiar uma base de dados do SQL Server para um novo servidor
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: 94314b3652a05ea387f0930a3a6315fcba6833d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-copy-a-sql-database-to-a-new-server"></a>Utilize o PowerShell para copiar uma base de dados do SQL Server para um novo servidor

Neste exemplo de script do PowerShell cria uma cópia da base de dados existente num servidor novo. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="copy-a-database-to-a-new-server"></a>Copiar uma base de dados para um novo servidor

[!code-powershell[main](../../../powershell_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.ps1?highlight=18-21 "Copy database to new server")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $sourceresourcegroupname
Remove-AzureRmResourceGroup -ResourceGroupName $targetresourcegroupname
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Novo AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Cria um servidor lógico que aloja um agrupamento de base de dados ou elástico. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Cria uma base de dados de um servidor lógico como um único ou uma base de dados agrupado. |
| [Novo AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) | Cria uma cópia da base de dados que utiliza o instantâneo no momento atual. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de base de dados do SQL adicionais podem ser encontrados no [scripts do PowerShell de base de dados do SQL Azure](../sql-database-powershell-samples.md).

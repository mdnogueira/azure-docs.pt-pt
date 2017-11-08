---
title: "PowerShell exemplo importação BACPAC ficheiro SQL database do Azure | Microsoft Docs"
description: Script de exemplo do PowerShell do Azure para importar um ficheiro BACPAC para uma base de dados do SQL Server
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
ms.openlocfilehash: f06473fcd9d0133e05f2ed83c243f0d707ba4567
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="use-powershell-to-import-a-bacpac-file-into-an-azure-sql-database"></a>Utilizar o PowerShell para importar um ficheiro BACPAC para uma base de dados SQL do Azure

Neste exemplo de script do PowerShell importa uma base de dados de um ficheiro BACPAC para uma base de dados SQL do Azure.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1?highlight=18-19 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode ser utilizado o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Novo AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Cria um servidor lógico que aloja a base de dados do SQL Server. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | Cria uma regra de firewall para permitir o acesso a todas as bases de dados do SQL Server no servidor do intervalo de endereços IP introduzido. |
| [Novo AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) | Importa um BACPAC de ficheiros e crie uma nova base de dados no servidor. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, consulte [documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos de script do PowerShell de base de dados do SQL adicionais podem ser encontrados no [scripts do PowerShell de base de dados do SQL Azure](../sql-database-powershell-samples.md).

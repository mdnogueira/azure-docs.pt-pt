---
title: "Azure PowerShell: Criar uma única base de dados SQL | Microsoft Docs"
description: "Saiba como criar um servidor lógico da Base de Dados SQL, regras de firewall ao nível do servidor e bases de dados no portal do Azure."
keywords: tutorial de base de dados sql, criar uma base de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fe527f7de573b87fbc644cb6d71ae13816bc284b
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Criar uma única base de dados SQL do Azure com o PowerShell

O PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia detalha a utilização do PowerShell para implementar uma base de dados SQL do Azure num grupo de recursos do Azure num servidor lógico de Base de Dados SQL.

Antes de começar, certifique-se de que a versão mais recente do PowerShell está instalada. A CLI do Azure foi instalada. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando [Add-AzureRmAccount](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) e siga as instruções no ecrã.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um servidor lógico de Base de Dados SQL com o comando [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). O exemplo seguinte cria um servidor com um nome aleatório no seu grupo de recursos com um início de sessão de administrador denominado `ServerAdmin` e uma palavra-passe de `ChangeYourAdminPassword1`. Substitua estes valores predefinidos conforme quiser.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Crie uma regra de firewall ao nível do servidor da Base de Dados SQL com o comando [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). Uma regra de firewall ao nível do servidor permite a uma aplicação externa, como o SQL Server Management Studio ou o utilitário SQLCMD, ligar a uma base de dados SQL através da firewall do serviço Base de Dados SQL. O exemplo seguinte cria uma regra de firewall para um intervalo de endereços predefinido que, neste exemplo, constitui todo o intervalo de endereços IP possível. Substitua estes valores predefinidos pelos valores do seu endereço IP externo ou intervalo de endereços IP. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Criar uma base de dados vazia

Crie uma base de dados SQL vazia com um nível de desempenho S0 no servidor com o comando [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase). O exemplo seguinte cria uma base de dados denominada `mySampleDatabase`. Substitua este valor predefinido conforme quiser.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados por este Manual de Início Rápido, execute o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

- Para ligar e consultar com o SQL Server Management Studio, veja [Ligar e consultar com SSMS](sql-database-connect-query-ssms.md)
- Para ligar com o Visual Studio, veja [Ligar e consultar com o Visual Studio](sql-database-connect-query.md).
* Para obter uma descrição geral técnica da Base de Dados SQL, veja [Acerca do serviço Base de Dados SQL](sql-database-technical-overview.md).


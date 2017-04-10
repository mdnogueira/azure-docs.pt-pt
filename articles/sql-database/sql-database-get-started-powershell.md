---
title: 'Azure PowerShell: criar uma base de dados SQL | Microsoft Docs'
description: "Saiba como criar um servidor lógico da Base de Dados SQL, regras de firewall ao nível do servidor e bases de dados no portal do Azure."
keywords: tutorial de base de dados sql, criar uma base de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 04/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 90dc7e4a07f2a3c514c25b4031128f4df4e32aab
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Criar uma única base de dados SQL do Azure com o PowerShell

O PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia detalha a utilização do PowerShell para implementar uma base de dados SQL do Azure num [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) num [servidor lógico de Base de Dados SQL do Azure](sql-database-features.md).

Para concluir este tutorial, confirme que tem instalado o [Azure PowerShell](/powershell/azureps-cmdlets-docs) mais recente. 

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando [Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) e siga as instruções no ecrã.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com o comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um [servidor lógico de Base de Dados SQL do Azure](sql-database-features.md) com o comando [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). Um servidor lógico contém um grupo de bases de dados geridas como um grupo. O exemplo seguinte cria um servidor com um nome aleatório no seu grupo de recursos com um início de sessão de administrador denominado `ServerAdmin` e uma palavra-passe de `ChangeYourAdminPassword1`. Substitua estes valores predefinidos conforme quiser.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Crie uma [regra de firewall ao nível do servidor da Base de Dados SQL do Azure](sql-database-firewall-configure.md) com o comando [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). Uma regra de firewall ao nível do servidor permite a uma aplicação externa, como o SQL Server Management Studio ou o utilitário SQLCMD, ligar a uma base de dados SQL através da firewall do serviço Base de Dados SQL. O exemplo seguinte cria uma regra de firewall para um intervalo de endereços predefinido que, neste exemplo, constitui todo o intervalo de endereços IP possível. Substitua estes valores predefinidos pelos valores do seu endereço IP externo ou intervalo de endereços IP. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Criar uma base de dados vazia

Crie uma base de dados SQL vazia com um [nível de desempenho S0](sql-database-service-tiers.md) no servidor com o comando [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase). O exemplo seguinte cria uma base de dados denominada `mySampleDatabase`. Substitua este valor predefinido conforme quiser.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução nesta coleção são criados através deste guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não quiser continuar, utilize os comandos seguintes para eliminar todos os recursos criados por este guia de introdução.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

- Para ligar e consultar com o SQL Server Management Studio, veja [Ligar e consultar com SSMS](sql-database-connect-query-ssms.md)
- Para ligar com o Visual Studio, veja [Ligar e consultar com o Visual Studio](sql-database-connect-query.md).
* Para obter uma descrição geral técnica da Base de Dados SQL, veja [Acerca do serviço Base de Dados SQL](sql-database-technical-overview.md).


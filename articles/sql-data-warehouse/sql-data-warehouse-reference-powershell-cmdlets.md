---
title: Cmdlets do PowerShell para o Azure SQL Data Warehouse
description: Localize os principais cmdlets do PowerShell para o Azure SQL Data Warehouse, incluindo como colocar em pausa e retomar uma base de dados.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 6f0d5772-f05f-4cc8-9749-4adb153dfd50
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: ce3e11587c2e0cb92923868a4f26d7f59c7ef4ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Cmdlets do PowerShell e APIs REST para o SQL Data Warehouse
Muitas tarefas de administração do armazém de dados do SQL Server podem ser geridas através de cmdlets do Azure PowerShell ou REST APIs.  Seguem-se alguns exemplos de como utilizar comandos do PowerShell para automatizar tarefas comuns no SQL Data Warehouse.  Para alguns bons exemplos REST, consulte o artigo [gerir escalabilidade REST][Manage scalability with REST].

> [!NOTE]
> Para utilizar o Azure PowerShell com o SQL Data Warehouse, precisa do Azure PowerShell, versão 1.0.3 ou superior.  Pode verificar a sua versão, executando **Get-Module - ListAvailable-Name Azure**.  A versão mais recente pode ser instalada na [instalador de plataforma Web Microsoft][Microsoft Web Platform Installer].  Para mais informações sobre como instalar a versão mais recente, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)][How to install and configure Azure PowerShell].
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introdução aos cmdlets do PowerShell do Azure
1. Abra o Windows PowerShell.
2. Na linha de comandos do PowerShell, execute estes comandos para iniciar o Gestor de recursos do Azure e selecionar a sua subscrição.
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemplo de armazém de dados SQL de pausa
Colocar em pausa de uma base de dados com o nome "Database02" alojada num servidor com o nome "Servidor01."  O servidor está num grupo de recursos do Azure com o nome "ResourceGroup1."

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, neste exemplo encaminha o objecto obtido em [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase].  Como resultado, a base de dados está em pausa. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Iniciar o exemplo de armazém de dados do SQL Server
A operação de retoma da base de dados com o nome "Database02" alojada num servidor com o nome "Servidor01." O servidor está contido num grupo de recursos com o nome "ResourceGroup1."

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo apresenta uma base de dados com o nome "Database02" de um servidor com o nome "Servidor01" que está contido num grupo de recursos com o nome "ResourceGroup1." Encaminha o objecto obtido em [retoma-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> Tenha em atenção que o se o servidor é foo.database.windows.net, utilize "foo" como ServerName - os cmdlets do PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Suportado outros cmdlets do PowerShell
Estes cmdlets do PowerShell são suportados com o Azure SQL Data Warehouse.

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [Novo-AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Restauro-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Retoma-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [SELECT-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Suspender-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>Passos seguintes
Para obter mais exemplos do PowerShell, consulte:

* [Criar um SQL Data Warehouse com o PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Restauro da base de dados][Database restore]

Para outras tarefas que podem ser automatizadas com o PowerShell, consulte [Cmdlets de base de dados SQL do Azure][Azure SQL Database Cmdlets]. Tenha em atenção que nem todos os cmdlets da SQL Database do Azure são suportados para o Azure SQL Data Warehouse.  Para obter uma lista de tarefas que pode ser automatizada com REST, consulte [operações para bases de dados do Azure SQL][Operations for Azure SQL Databases].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://msdn.microsoft.com/library/mt574084.aspx
[Operations for Azure SQL Databases]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

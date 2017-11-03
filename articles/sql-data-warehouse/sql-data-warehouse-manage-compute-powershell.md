---
title: "Gerir a capacidade de computação no Azure SQL Data Warehouse (PowerShell) | Microsoft Docs"
description: "As tarefas do PowerShell para gerir poder de computação. Dimensionar os recursos de computação ao ajustar as DWUs. Ou, colocar em pausa e retomar a recursos de computação para reduzir os custos."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6a185d96447c2e1b0b463439dd062081e783da5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gerir a capacidade de computação no Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Descrição geral](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

## <a name="before-you-begin"></a>Antes de começar
### <a name="install-the-latest-version-of-azure-powershell"></a>Instale a versão mais recente do Azure PowerShell
> [!NOTE]
> Para utilizar o Azure PowerShell com o SQL Data Warehouse, precisa do Azure PowerShell, versão 1.0.3 ou superior.  Para verificar a versão atual, execute o comando **Get-Module - ListAvailable-Name Azure**. Pode instalar a versão mais recente do [instalador de plataforma Web Microsoft][Microsoft Web Platform Installer].  Para obter mais informações, consulte [como instalar e configurar o Azure PowerShell][How to install and configure Azure PowerShell].
>
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Introdução aos cmdlets do PowerShell do Azure
Para começar a utilizar:

1. Abra o PowerShell do Azure.
2. Na linha de comandos do PowerShell, execute estes comandos para iniciar o Gestor de recursos do Azure e selecionar a sua subscrição.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Capacidade de computação de escala
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar as DWUs, utilize o [Set-AzureRmSqlDatabase] [ Set-AzureRmSqlDatabase] cmdlet do PowerShell. O exemplo a seguir define o objetivo de nível de serviço para DW1000 MySQLDW que está alojado no servidor MyServer da base de dados.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Computação pausa
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para colocar em pausa uma base de dados, utilize o [Suspend-AzureRmSqlDatabase] [ Suspend-AzureRmSqlDatabase] cmdlet. O exemplo seguinte interrompe uma base de dados com o nome Database02 alojada num servidor com o nome servidor01. O servidor está num grupo de recursos do Azure com o nome ResourceGroup1.

> [!NOTE]
> Tenha em atenção que o se o servidor é foo.database.windows.net, utilize "foo" como ServerName - os cmdlets do PowerShell.
>
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, neste exemplo seguinte obtém a base de dados para o objeto de $database. -Lo, em seguida, encaminha o objecto a [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. Os resultados são armazenados no resultDatabase objeto. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Retomar de computação
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar uma base de dados, utilize o [retoma-AzureRmSqlDatabase] [ Resume-AzureRmSqlDatabase] cmdlet. O exemplo seguinte inicia uma base de dados com o nome Database02 alojada num servidor com o nome servidor01. O servidor está num grupo de recursos do Azure com o nome ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, neste exemplo seguinte obtém a base de dados para o objeto de $database. -Lo, em seguida, encaminha o objecto a [retoma-AzureRmSqlDatabase] [ Resume-AzureRmSqlDatabase] e armazena os resultados no $resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state"></a>Verificar o estado da base de dados

Conforme mostrado nos exemplos acima, pode utilizar um [Get-AzureRmSqlDatabase] [ Get-AzureRmSqlDatabase] cmdlet para obter informações sobre uma base de dados, aumentando assim a verificar o estado, mas também para utilizar como um argumento. 

```powershell
Get-AzureRmSqlDatabase [-ResourceGroupName] <String> [-ServerName] <String> [[-DatabaseName] <String>]
 [-InformationAction <ActionPreference>] [-InformationVariable <String>] [-Confirm] [-WhatIf]
 [<CommonParameters>]
```

Que irá resultar na algo como 

```powershell   
ResourceGroupName             : nytrg
ServerName                    : nytsvr
DatabaseName                  : nytdb
Location                      : West US
DatabaseId                    : 86461aae-8e3d-4ded-9389-ac9d4bc69bbb
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1General_CP1CI_AS
CatalogCollation              :
MaxSizeBytes                  : 32212254720
Status                        : Online
CreationDate                  : 10/26/2016 4:33:14 PM
CurrentServiceObjectiveId     : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
CurrentServiceObjectiveName   : System2
RequestedServiceObjectiveId   : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           : 1/1/0001 12:00:00 AM
```

Onde, em seguida, pode verificar para ver o *estado* da base de dados. Neste caso, pode ver que esta base de dados está online. 

Quando executar este comando, deverá receber um valor de estado de um Online, Pausing, a retomar, dimensionamento e em pausa.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passos seguintes
Para outras tarefas de gestão, consulte [descrição geral da gestão][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Get-AzureRmSqlDatabase]: /powershell/servicemanagement/azure.sqldatabase/v1.6.1/get-azuresqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/

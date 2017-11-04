---
title: Importar um ficheiro BACPAC para criar uma base de dados SQL do Azure | Microsoft Docs
description: Crie uma base de dados do SQL Server newAzure ao importar um ficheiro BACPAC.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 06/26/2017
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 34dee9511822acec46ba4854729939b84f3c06c6
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Importar um ficheiro BACPAC para uma nova SQL Database do Azure

Quando é necessário importar uma base de dados de arquivo ou quando a migrar de outra plataforma, pode importar o esquema de base de dados e dos dados de um [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ficheiro. Um ficheiro BACPAC é um ficheiro ZIP com uma extensão BACPAC que contém os metadados e dados a partir de uma base de dados do SQL Server. Um ficheiro BACPAC pode ser importado a partir do blob storage do Azure (apenas armazenamento standard) ou a partir do armazenamento local numa localização no local. Para maximizar a velocidade de importação, recomendamos que especifique um nível mais elevado serviço camada e o desempenho, tais como um P6 e, em seguida, aumentar pendente, conforme adequado, após a importação for concluída com êxito. Além disso, o nível de compatibilidade de base de dados após a importação baseia-se no nível de compatibilidade da base de dados de origem. 

> [!IMPORTANT] 
> Depois de migrar a base de dados para a SQL Database do Azure, pode optar por utilizar a base de dados ao respetivo nível de compatibilidade atual (nível 100 para a base de dados AdventureWorks2008R2) ou com um nível superior. Para obter mais informações sobre as implicações e as opções para o funcionamento uma base de dados a um nível de compatibilidade específicos, consulte [nível de compatibilidade de base de dados ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [configuração de um âmbito de base de dados de falha de ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre definições de nível de base de dados adicionais relacionados com níveis de compatibilidade.   >

> [!NOTE]
> Para importar um BACPAC para uma nova base de dados, tem primeiro de criar um servidor lógico da SQL Database do Azure. Para um tutorial mostrar como migrar uma base de dados do SQL Server para utilizar SQLPackage de SQL Database do Azure, consulte [migrar uma base de dados do SQL Server](sql-database-migrate-your-sql-server-database.md)
>

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Importar de um ficheiro BACPAC através do portal do Azure

Este artigo fornece instruções para criar uma base de dados SQL do Azure a partir de um ficheiro BACPAC armazenado no armazenamento de Blobs do Azure utilizando o [portal do Azure](https://portal.azure.com). Importar através do portal do Azure só suporta a importação de um ficheiro BACPAC do armazenamento de Blobs do Azure.

Para importar uma base de dados no portal do Azure, abra a página para o servidor ao qual associar a base de dados e, em seguida, clique em **importar** na barra de ferramentas. Especifique a conta de armazenamento e o contentor e selecione o ficheiro BACPAC que pretende importar. Selecione o tamanho da nova base de dados (normalmente, os mesmos como origem) e o destino de fornecer credenciais do SQL Server.  

   ![Importação de base de dados](./media/sql-database-import/import.png)

Para monitorizar o progresso da operação de importação, abra a página para o servidor lógico que contém a base de dados que está a ser importado. Desloque para baixo até **operações** e, em seguida, clique em **importar/exportar** histórico.

### <a name="monitor-the-progress-of-an-import-operation"></a>Monitorizar o progresso de uma operação de importação

Para monitorizar o progresso da operação de importação, abra a página para o servidor lógico no qual está a ser importado a base de dados importado. Desloque para baixo até **operações** e, em seguida, clique em **importar/exportar** histórico.
   
   ![importar](./media/sql-database-import/import-history.png) ![Importar estado](./media/sql-database-import/import-status.png)

Para verificar a base de dados está em direto no servidor, clique em **bases de dados SQL** e certifique-se de que a base de dados nova **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importar de um ficheiro BACPAC utilizando SQLPackage

Para importar uma base de dados do SQL Server utilizando o [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) utilitário de linha de comandos, consulte [importar parâmetros e as propriedades](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties). O utilitário de SQLPackage é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou pode transferir a versão mais recente do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente a partir do Centro de transferências da Microsoft.

Recomendamos a utilização do utilitário de SQLPackage para dimensionamento e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Consulte o seguinte comando SQLPackage para obter um exemplo de script como importar o **AdventureWorks2008R2** base de dados do armazenamento local para um servidor lógico de base de dados do Azure SQL, denominado **mynewserver20170403** neste exemplo. Este script mostra a criação de uma nova base de dados chamado **myMigratedDatabase**, com uma camada de serviço de **Premium**e um objetivo do serviço de **P6**. Altere estes valores conforme adequado ao seu ambiente.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Um servidor lógico da Base de Dados SQL do Azure ouve na porta 1433. Se está a tentar ligar a um servidor lógico da Base de Dados SQL do Azure a partir de uma firewall empresarial, esta porta tem de estar aberta na firewall empresarial para se ligar com êxito.
>

Este exemplo mostra como importar uma base de dados utilizando SqlPackage.exe com autenticação de Universal do Active Directory:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importar de um ficheiro BACPAC através do PowerShell

Utilize o [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) cmdlet para submeter um pedido de base de dados de importação para o serviço SQL Database do Azure. Dependendo do tamanho da base de dados, a operação de importação pode demorar algum tempo a concluir.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Para verificar o estado do pedido de importação, utilize o [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet. Com esta imediatamente após o pedido geralmente devolve **Estado: em curso**. Quando vir **Estado: foi concluída com êxito** a importação estiver concluída.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Para obter outro exemplo de script, consulte [importar uma base de dados de um ficheiro BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="next-steps"></a>Passos seguintes
* Para saber como ligar e consultar uma base de dados importados do SQL Server, consulte [ligar à base de dados SQL com o SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md).
* Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Para um debate sobre o completo do SQL Server da base de dados do processo de migração, incluindo as recomendações de desempenho, consulte [migrar uma base de dados do SQL Server a SQL Database do Azure](sql-database-cloud-migrate.md).




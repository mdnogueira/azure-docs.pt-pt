---
title: Exportar uma base de dados SQL do Azure para um ficheiro BACPAC | Microsoft Docs
description: "Exportar uma base de dados SQL do Azure para um ficheiro BACPAC através do Portal do Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 10/11/2017
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 2291088b3d8e50f4e8143bf8560580e89db4cae3
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportar uma base de dados SQL do Azure para um ficheiro BACPAC

Quando for necessário exportar uma base de dados para arquivar ou para mover para outra plataforma, pode exportar o esquema de base de dados e dos dados para um [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ficheiro. Um ficheiro BACPAC é um ficheiro ZIP com uma extensão BACPAC que contém os metadados e dados a partir de uma base de dados do SQL Server. Um ficheiro BACPAC pode ser armazenado no blob storage do Azure ou no armazenamento local numa localização no local e posteriormente importado para o SQL Database do Azure ou para uma instalação do SQL Server no local. 

> [!IMPORTANT] 
> Azure base de dados de SQL automatizada exportar foi reformada no dia 1 de Março de 2017. Pode utilizar [retenção de cópias de segurança de longa duração](sql-database-long-term-retention.md
) ou [da automatização do Azure](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) arquivar periodicamente SQL as bases de dados com o PowerShell, de acordo com uma agenda à sua escolha. Para um exemplo, transfira o [script do PowerShell de exemplo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) a partir do Github.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considerações ao exportar uma base de dados SQL do Azure

* Para a exportação ser uma forma consistente, tem de garantir o que não escrita atividade está a ocorrer durante a exportação ou que são exportar de um [cópia de uma forma consistente](sql-database-copy.md) da base de dados SQL do Azure.
* Se estiver a exportar para o blob storage, o tamanho máximo de um ficheiro BACPAC é 200 GB. Para arquivar um ficheiro BACPAC maior, exportar para o armazenamento local.
* Não é suportada a exportar um ficheiro BACPAC para armazenamento premium do Azure utilizando os métodos abordados neste artigo.
* Se a operação de exportação da base de dados do Azure SQL exceder 20 horas, pode ser cancelada. Para aumentar o desempenho durante a exportação, pode:
  * Temporariamente aumente o nível de serviço.
  * Cessar todos de leitura e escrita atividade durante a exportação.
  * Utilize um [índice em cluster](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem os índices em cluster, uma exportação poderá falhar se demora mais de 6-12 horas. Isto acontece porque o serviço de exportação precisa de concluir uma análise da tabela para tentar exportar a tabela inteira. Uma boa forma de determinar se as suas tabelas estão otimizadas para exportação está a ser executado **DBCC SHOW_STATISTICS** e certifique-se de que o *RANGE_HI_KEY* não seja nulo e o valor tem um bom distribuição. Para obter mais informações, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs não se destinam a ser utilizada para cópia de segurança e restaurar as operações. Base de dados SQL do Azure cria automaticamente cópias de segurança para cada base de dados do utilizador. Para obter mais informações, consulte [descrição geral da continuidade do negócio](sql-database-business-continuity.md) e [cópias de segurança da base de dados SQL](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exporte para um ficheiro BACPAC no portal do Azure

Para exportar uma base de dados utilizando o [portal do Azure](https://portal.azure.com), abra a página da base de dados e clique em **exportar** na barra de ferramentas. Especifique o nome de ficheiro BACPAC, forneça a conta de armazenamento do Azure e um contentor para a exportação e forneça as credenciais para ligar à base de dados de origem.  

![Exportação de base de dados](./media/sql-database-export/database-export.png)

Para monitorizar o progresso da operação de exportação, abra a página para o servidor lógico que contém a base de dados que está a ser exportado. Desloque para baixo até **operações** e, em seguida, clique em **importar/exportar** histórico.

![Exportar histórico](./media/sql-database-export/export-history.png)
![exportar o estado do histórico](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exporte para um ficheiro BACPAC utilizando o utilitário de SQLPackage

Para exportar uma base de dados do SQL Server utilizando o [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) utilitário de linha de comandos, consulte [exportar parâmetros e as propriedades](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). O utilitário de SQLPackage é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou pode transferir a versão mais recente do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente a partir do Centro de transferências da Microsoft.

Recomendamos a utilização do utilitário de SQLPackage para dimensionamento e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar uma base de dados utilizando SqlPackage.exe com autenticação de Universal do Active Directory:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exporte para um ficheiro BACPAC utilizando o SQL Server Management Studio (SSMS)

As versões mais recentes do SQL Server Management Studio também fornecem um Assistente para exportar uma base de dados do SQL do Azure para um ficheiro BACPAC. Consulte o [exportar uma aplicação de camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exporte para um ficheiro BACPAC através do PowerShell

Utilize o [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) cmdlet para submeter um pedido de base de dados de exportação para o serviço SQL Database do Azure. Dependendo do tamanho da base de dados, a operação de exportação pode demorar algum tempo a concluir.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Para verificar o estado do pedido de exportação, utilize o [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet. Com esta imediatamente após o pedido geralmente devolve **Estado: em curso**. Quando vir **Estado: foi concluída com êxito** a exportação for concluída.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre retenção de cópias de segurança de longa duração de uma cópia de segurança de base de dados SQL do Azure como uma alternativa ao exportar uma base de dados para efeitos de arquivo, consulte [retenção de cópias de segurança de longa duração](sql-database-long-term-retention.md).
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Para saber mais sobre como importar um BACPAC de uma base de dados do SQL Server, consulte [importar um BACPCAC para uma base de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
* Para saber mais sobre como exportar um BACPAC de uma base de dados do SQL Server, consulte o artigo [exportar uma aplicação de camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) e [migrar a sua primeira base de dados](sql-database-migrate-your-sql-server-database.md).
* Se estiver exportação do SQL Server como um prelude para a migração para a SQL Database do Azure, consulte o artigo [migrar uma base de dados do SQL Server a SQL Database do Azure](sql-database-cloud-migrate.md).

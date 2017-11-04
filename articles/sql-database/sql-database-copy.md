---
title: Copiar uma base de dados SQL do Azure | Microsoft Docs
description: "Crie uma forma consistente cópia da base de dados SQL do Azure existente no mesmo servidor ou um servidor diferente."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 06/15/2017
ms.author: carlrab
ms.workload: On Demand
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 7999c52a81165a9f8efb2f1302d04c9cb851faca
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="copy-an-azure-sql-database"></a>Copiar uma base de dados SQL do Azure

Base de dados SQL do Azure fornece vários métodos para criar uma cópia de uma forma consistente da base de dados SQL do Azure existente no mesmo servidor ou um servidor diferente. Pode copiar uma base de dados do SQL Server utilizando o portal do Azure, o PowerShell ou o T-SQL. 

## <a name="overview"></a>Descrição geral

Uma cópia da base de dados é um instantâneo da base de dados de origem a partir do momento do pedido de cópia. Pode selecionar o mesmo servidor ou um servidor diferente, a camada de serviço e nível de desempenho ou um nível de desempenho diferente dentro da mesma camada de serviço (edição). Depois de concluída a cópia, torna-se uma base de dados totalmente funcional, independente. Neste momento, pode atualizar ou mudá-lo para qualquer edição. Os inícios de sessão, utilizadores e permissões podem ser geridas de forma independente.  

## <a name="logins-in-the-database-copy"></a>Inícios de sessão na cópia da base de dados

Quando copiar uma base de dados para o mesmo servidor lógico, os inícios de sessão mesmos podem ser utilizados em ambas as bases de dados. O principal a que utilizar para copiar a base de dados de segurança torna-se o proprietário da base de dados na base de dados de novo. Todos os utilizadores de base de dados, as suas permissões e os identificadores de segurança (SIDs) são copiados para a cópia da base de dados.  

Quando copiar uma base de dados para um servidor lógico diferentes, o principal no novo servidor de segurança torna-se o proprietário da base de dados na base de dados de novo. Se utilizar [continha os utilizadores de base de dados](sql-database-manage-logins.md) para acesso a dados, certifique-se de que as bases de dados primários e secundários tem sempre as mesmas credenciais de utilizador, para que depois de concluída a cópia tem imediatamente acesso com as mesmas credenciais . 

Se utilizar [do Azure Active Directory](../active-directory/active-directory-whatis.md), pode eliminar completamente a necessidade de gerir credenciais na cópia. No entanto, quando copiar a base de dados para um novo servidor, o acesso baseado no início de sessão poderá não funcionar, porque não existem inícios de sessão no novo servidor. Para saber mais sobre a gestão de inícios de sessão quando copiar uma base de dados para um servidor lógico diferente, consulte [como gerir a segurança de base de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md). 

Depois da cópia for bem sucedida e antes de outros utilizadores são novamente mapeados, apenas o início de sessão que iniciou a cópia, o proprietário da base de dados, pode iniciar sessão na base de dados de novo. Para resolver os inícios de sessão depois de concluída a operação de cópia, consulte [resolver inícios de sessão](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copiar uma base de dados utilizando o portal do Azure

Para copiar uma base de dados utilizando o portal do Azure, abra a página da base de dados e, em seguida, clique em **cópia**. 

   ![Cópia de base de dados](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Copiar uma base de dados utilizando o PowerShell

Para copiar uma base de dados utilizando o PowerShell, utilize o [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) cmdlet. 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Para um script de exemplo completa, consulte [copiar uma base de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Copiar uma base de dados, utilizando Transact-SQL

Inicie sessão base de dados mestra com o início de sessão principal ao nível do servidor ou o início de sessão que criou a base de dados que pretende copiar. Para copiar da base de dados tenha êxito, os inícios de sessão que não são o principal ao nível do servidor tem de ser membros da função dbmanager. Para obter mais informações sobre os inícios de sessão e ligar ao servidor, consulte [gerir inícios de sessão](sql-database-manage-logins.md).

Começar a copiar a base de dados de origem com o [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) instrução. Executar esta instrução inicia o processo de copiar a base de dados. Como copiar uma base de dados é um processo assíncrono, a instrução CREATE DATABASE devolve antes da cópia da base de dados está concluída.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar uma base de dados do SQL Server para o mesmo servidor
Inicie sessão base de dados mestra com o início de sessão principal ao nível do servidor ou o início de sessão que criou a base de dados que pretende copiar. Para copiar da base de dados tenha êxito, os inícios de sessão que não são o principal ao nível do servidor tem de ser membros da função dbmanager.

Este comando copia Database1 para uma nova base de dados com o nome Database2 no mesmo servidor. Dependendo do tamanho da base de dados, a operação de cópia pode demorar algum tempo a concluir.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar uma base de dados do SQL Server para um servidor diferente

Inicie sessão base de dados mestra do servidor de destino, o servidor de base de dados do SQL Server onde a nova base de dados está a ser criado. Utilize um início de sessão que tenha o mesmo nome e palavra-passe como o proprietário da base de dados da base de dados de origem no servidor de base de dados do SQL Server de origem. O início de sessão no servidor de destino têm também de ser um membro da função dbmanager ou o início de sessão principal ao nível do servidor.

Este comando copia Database1 no servidor1 para uma nova base de dados com o nome Database2 no Servidor2. Dependendo do tamanho da base de dados, a operação de cópia pode demorar algum tempo a concluir.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorizar o progresso da operação de cópia

Monitorize o processo de cópia ao consultar as vistas Databases e sys.dm_database_copies. Enquanto a cópia estiver em curso, o **state_desc** coluna da vista Databases para a nova base de dados está definida como **copiar**.

* Se a cópia falhar, o **state_desc** coluna da vista Databases para a nova base de dados está definida como **SUSPEITAR**. Execute a instrução de LARGAR na base de dados nova e tente novamente mais tarde.
* Se a cópia tiver êxito, o **state_desc** coluna da vista Databases para a nova base de dados está definida como **ONLINE**. A cópia foi concluída e a nova base de dados é uma base de dados normal que pode ser alterado independentes da base de dados de origem.

> [!NOTE]
> Se optar por cancelar a cópia enquanto esta se encontra em curso, execute o [remover a base de dados](https://msdn.microsoft.com/library/ms178613.aspx) instrução na base de dados de novo. Em alternativa, executar a instrução de remover a base de dados na base de dados de origem também cancela o processo de cópia.
> 

## <a name="resolve-logins"></a>Resolver inícios de sessão

Após a conclusão da nova base de dados online no servidor de destino, utilize o [ALTER utilizador](https://msdn.microsoft.com/library/ms176060.aspx) instrução ao remapear os utilizadores da base de dados nova para inícios de sessão no servidor de destino. Para resolver utilizadores órfãos, consulte [resolver utilizadores órfãos](https://msdn.microsoft.com/library/ms175475.aspx). Consulte também [como gerir a segurança de base de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Todos os utilizadores na base de dados nova mantém as permissões que tinham na base de dados de origem. O utilizador que iniciou a cópia da base de dados torna-se o proprietário da base de dados da base de dados nova e tem atribuído um novo identificador de segurança (SID). Depois da cópia for bem sucedida e antes de outros utilizadores são novamente mapeados, apenas o início de sessão que iniciou a cópia, o proprietário da base de dados, pode iniciar sessão na base de dados de novo.

Para saber mais sobre a gestão de utilizadores e os inícios de sessão quando copiar uma base de dados para um servidor lógico diferente, consulte [como gerir a segurança de base de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre os inícios de sessão, consulte [gerir inícios de sessão](sql-database-manage-logins.md) e [como gerir a segurança de base de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).
* Para exportar uma base de dados, consulte [exportar a base de dados para um BACPAC](sql-database-export.md).

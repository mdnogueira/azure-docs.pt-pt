---
title: "A encriptação transparente de dados no armazém de dados do SQL (T-SQL) | Microsoft Docs"
description: "Encriptação de dados transparente (TDE) no armazém de dados do SQL (T-SQL)"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: barbkess
editor: 
ms.assetid: 8ccefef3-1308-41ee-b336-5e491d1098ae
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: 74c9032aababdce91ed617cd7a4c628915b42504
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Começar com encriptação de dados transparente (TDE)
> [!div class="op_single_selector"]
> * [Descrição geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>Permssions necessária
Para ativar a encriptação de dados transparente (TDE), tem de ser um administrador ou membro da função dbmanager.

## <a name="enabling-encryption"></a>Ativar a encriptação
Siga estes passos para ativar a TDE para um SQL Data Warehouse:

1. Ligar para o *mestre* base de dados no servidor que aloja a base de dados utilizando um início de sessão que é um administrador ou membro do **dbmanager** função na base de dados mestra
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>A desativação da encriptação
Siga estes passos para desativar o TDE para um SQL Data Warehouse:

1. Ligar para o *mestre* da base de dados utilizando um início de sessão que é um administrador ou membro do **dbmanager** função na base de dados mestra
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Antes de efetuar alterações às definições de TDE necessário recomeçar um SQL Data Warehouse em pausa.
> 
> 

## <a name="verifying-encryption"></a>Verificar a encriptação
Para verificar o estado de encriptação para um SQL Data Warehouse, siga os passos abaixo:

1. Ligar para o *mestre* ou utilizar um início de sessão que é um administrador ou membro de base de dados da instância a **dbmanager** função na base de dados mestra
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Um resultado de ```1``` indica uma base de dados encriptado, ```0``` indica uma base de dados não encriptados.

## <a name="encryption-dmvs"></a>Encriptação DMVs
* [Databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->

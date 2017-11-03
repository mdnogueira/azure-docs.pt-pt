---
title: "Ativar a encriptação transparente de dados para a Stretch Database TSQL - Azure | Microsoft Docs"
description: "Ativar a encriptação de dados transparente (TDE) para o SQL Server Stretch Database no TSQL do Azure"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: anvang
ms.openlocfilehash: ed26c2b386e08b76f78b4a05e12c46d2b97c20f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Ativar a encriptação de dados transparente (TDE) para a Stretch Database no Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Encriptação de dados transparente (TDE) ajuda a proteger contra a ameaça de atividade maliciosa através de encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e os ficheiros de registo de transações Inativos sem necessidade de alterações à aplicação.

TDE encripta o armazenamento de uma base de dados completa através de uma chave simétrica chamada a chave de encriptação da base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor incorporada. O certificado de servidor incorporada é exclusivo para cada servidor do Azure. Microsoft roda automaticamente estes certificados, pelo menos, todos os 90 dias. Para obter uma descrição geral de TDE, consulte [encriptação de dados transparente (TDE)].

## <a name="enabling-encryption"></a>Ativar a encriptação
Para ativar a TDE para um Azure base de dados que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com a Stretch ativada, efetue os seguintes procedimentos:

1. Ligar para o *mestre* base de dados no servidor do Azure que aloja a base de dados utilizando um início de sessão que é um administrador ou membro do **dbmanager** função na base de dados mestra
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>A desativação da encriptação
Para desativar o TDE para um Azure base de dados que está a armazenar os dados migrados a partir de uma base de dados do SQL Server com a Stretch ativada, efetue os seguintes procedimentos:

1. Ligar para o *mestre* da base de dados utilizando um início de sessão que é um administrador ou membro do **dbmanager** função na base de dados mestra
2. Execute a seguinte instrução para encriptar a base de dados.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Verificar a encriptação
Para verificar o estado de encriptação para uma base de dados do Azure que está a armazenar os dados migrados de uma base de dados do SQL Server com a Stretch ativada, efetue os seguintes procedimentos:

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

<!--Anchors-->
[encriptação de dados transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

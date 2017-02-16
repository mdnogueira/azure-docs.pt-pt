---
title: "Portal do Azure: Regras de firewall ao nível do servidor da Base de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como configurar regras de firewall ao nível do servidor para endereços IP que acedem ao servidor SQL do Azure através do portal do Azure."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2a85b3dc1078bad9e5e2fc0ce0bec7e994b29150
ms.openlocfilehash: 36938e642853d587cd28405f5e518ac9ac0697ac


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Create and manage Azure SQL Database server-level firewall rules using the Azure portal (Criar e gerir regras de firewall ao nível do servidor da Base de Dados SQL do Azure com o Portal do Azure)
> [!div class="op_single_selector"]
> * [Descrição geral](sql-database-firewall-configure.md)
> * [Portal do Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 

As regras de firewall ao nível do servidor permitem que os administradores acedam a um servidor de Base de Dados SQL a partir de um endereço IP especificado ou intervalo de endereços IP. Também pode utilizar as regras de firewall ao nível do servidor para os utilizadores quando tem muitas bases de dados que têm os mesmos requisitos de acesso e não quer perder tempo a configurar individualmente cada base de dados. A Microsoft recomenda a utilização de regras de firewall ao nível da base de dados sempre que possível, para melhorar a segurança e tornar a sua base de dados mais portátil. Para obter uma descrição geral de firewalls da Base de Dados SQL, consulte [Overview of SQL Database firewall rules (Descrição geral das regras de firewall da Base de Dados SQL)](sql-database-firewall-configure.md).

> [!Note]
> Para obter informações sobre bases de dados portáteis no contexto de continuidade do negócio, veja [Authentication requirements for disa ster recovery (Requisitos de autenticação para a recuperação após desastre)](sql-database-geo-replication-security-config.md).
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor existentes através do Portal do Azure
Repita os passos para gerir as regras de firewall ao nível do servidor.

* Para adicionar o computador atual, clique em Adicionar IP de cliente.
* Para adicionar endereços IP adicionais, escreva o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique.
* Para eliminar uma regra existente, paire sobre a regra até que apareça X no final da linha. Clique em X para remover a regra.

Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes

- Para obter um tutorial introdutório, veja o artigo [Tutorial da Base de Dados SQL: Criar um servidor, uma regra de firewall ao nível do servidor, uma base de dados de exemplo, uma regra de firewall ao nível da base de dados e ligar com o SQL Server](sql-database-get-started.md).
- Para uma introdução com um tutorial de segurança, consulte [Introdução à segurança](sql-database-control-access-sql-authentication-get-started.md)
- Para obter ajuda para ligar a uma base de dados SQL do Azure a partir de aplicações de código aberto ou de terceiros, veja [Client quick-start code samples to SQL Database (Exemplos de código de início rápido de cliente para a Base de Dados SQL)](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para compreender como criar utilizadores adicionais que se podem ligar a bases de dados, consulte [SQL Database Authentication and Authorization: Granting Access (Autenticação e Autorização da Base de Dados SQL: Conceder Acesso)](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Recursos adicionais
* [Proteger a sua base de dados](sql-database-security-overview.md)   
* [Centro de Segurança do Motor da Base de Dados do SQL Server e da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Jan17_HO3-->



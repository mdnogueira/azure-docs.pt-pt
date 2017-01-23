---
title: "Descrição geral da base de dados SQL do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral das bases de dados SQL do Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 315fb49ba25c46afaa6571d9249ecd1c8da13e91


---
# <a name="azure-sql-database-overview"></a>Descrição geral da base de dados SQL do Azure
Este tópico fornece uma descrição geral das bases de dados SQL do Azure. Para obter informações sobre servidores lógicos de SQL do Azure, veja [Servidores lógicos](sql-database-server-overview.md).

## <a name="what-is-azure-sql-database"></a>O que é a base de dados SQL do Azure?
Cada base de dados na Base de Dados SQL do Azure está associada a um servidor lógico. A base de dados pode ser:

- Uma base de dados individual com um [conjunto próprio de recursos](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTUs)
- Parte de um [conjunto elástico](sql-database-elastic-pool.md) que [partilha um conjunto de recursos](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTUs)
- Parte de um [conjunto aumentado horizontalmente de bases de dados partilhadas](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), que podem ser bases de dados individuais ou agrupadas
- Parte de um conjunto de bases de dados que participam num [padrão de estrutura de SaaS multi-inquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md), e cujas bases de dados podem ser individuais ou agrupadas (ou ambas) 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Como posso ligar e autenticar-me numa base de dados SQL do Azure?

- **Autenticação e autorização**: a Base de Dados SQL do Azure suporta a autenticação SQL e a Autenticação do Azure Active Directory (com determinadas limitações - veja [Ligar à Base de Dados SQL com a Autenticação do Azure Active Directory](sql-database-aad-authentication.md) para autenticação. Pode ligar e autenticar-se nas bases de dados SQL do Azure através da base de dados mestra do servidor ou diretamente a uma base de dados de utilizador. Para obter mais informações, veja [Gerir Bases de Dados e Inícios de Sessão na Base de Dados SQL do Azure](sql-database-manage-logins.md). A autenticação do Windows não é suportada. 
- **TDS**: a Base de Dados SQL do Microsoft Azure suporta a versão 7.3 ou posterior do cliente de protocolo TDS.
- **TCP/IP**: apenas são permitidas ligações TCP/IP.
- **Firewall da Base de Dados SQL**: para ajudar a proteger os seus dados, uma firewall da Base de Dados SQL impede todo o acesso ao servidor da base de dados ou às respetivas bases de dados até especificar que computadores têm permissão. Veja [Firewalls](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Quais são os agrupamentos suportados?
O agrupamento de base de dados predefinido utilizado pela Base de Dados SQL do Microsoft Azure é **SQL_LATIN1_GENERAL_CP1_CI_AS**, onde **LATIN1_GENERAL** é inglês (Estados Unidos), **CP1** é o código de página 1252, **CI** é sensível a maiúsculas e minúsculas e **AS** é sensível a acentos. Não é possível alterar o agrupamento para bases de dados V12. Para obter mais informações sobre como definir o agrupamento, veja [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Quais são os requisitos de nomenclatura para objetos de base de dados?

Os nomes de todos os novos objetos têm de estar em conformidade com as regras do SQL Server para identificadores. Para obter mais informações, veja [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Que funcionalidades são suportadas pelas bases de dados SQL do Azure?

Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md). Veja também [Diferenças do Transact-SQL da Base de dados SQL do Azure](sql-database-transact-sql-information.md) para obter mais contexto sobre as razões para a falta de suporte de determinados tipos de funcionalidades.

## <a name="how-do-i-manage-an-azure-sql-database"></a>Como posso gerir uma base de dados SQL do Azure?

Pode gerir os servidores lógicos da Base de Dados SQL do Azure através de vários métodos:
- [Portal do Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o serviço Base de Dados SQL do Azure, veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md)
- Para obter uma descrição geral dos servidores lógicos do SQL Azure, veja [Descrição geral do servidor lógico da Base de Dados SQL](sql-database-server-overview.md)
- Para obter informações sobre o suporte de Transact-SQL e as diferenças, veja [Diferenças do Transact-SQL da Base de Dados SQL do Azure](sql-database-transact-sql-information.md).
- Para obter informações sobre quotas e limitações de recursos específicas com base na sua **camada de serviços**. Para obter uma descrição geral de camadas de serviços, veja [Camadas de serviços da Base de Dados SQL](sql-database-service-tiers.md).
- Para obter uma descrição geral da segurança, veja [Descrição Geral da Segurança da Base de Dados SQL do Azure](sql-database-security-overview.md).
- Para obter informações sobre a disponibilidade de controlador e o suporte da Base de Dados SQL, veja [Bibliotecas de Ligação para a Base de Dados SQL e o SQL Server](sql-database-libraries.md).




<!--HONumber=Dec16_HO4-->



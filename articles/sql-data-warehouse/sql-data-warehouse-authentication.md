---
title: "Autenticação ao Azure SQL Data Warehouse | Microsoft Docs"
description: Authentication do Azure Active Directory (AAD) e o SQL Server para o Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
tags: 
ms.assetid: fefaaa75-2d0c-4e5d-aadb-410342d1ad73
ms.service: sql-data-warehouse
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.custom: security
ms.date: 03/21/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: 92f48027051bc4aff4d6b8d66fdd6de81bba3657
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-to-azure-sql-data-warehouse"></a>Authentication to Azure SQL Data Warehouse (Autenticação no Azure SQL Data Warehouse)
> [!div class="op_single_selector"]
> * [Descrição geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Para ligar ao SQL Data Warehouse, tem de passar em credenciais de segurança para efeitos de autenticação. Depois de estabelecer uma ligação, determinadas definições de ligação estão configuradas como parte de estabelecer a sessão de consulta.  

Para obter mais informações sobre como ativar as ligações ao seu armazém de dados e de segurança, consulte [proteger uma base de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Autenticação do SQL
Para ligar ao SQL Data Warehouse, tem de fornecer as seguintes informações:

* Servername completamente qualificado
* Especifique a autenticação do SQL Server
* Nome de utilizador
* Palavra-passe
* Base de dados predefinida (opcional)

Por predefinição, a ligação estabelece ligação com o *mestre* base de dados e não a base de dados do utilizador. Para ligar a sua base de dados do utilizador, pode optar por fazer uma das duas coisas:

* Especifique a base de dados predefinida quando registar o seu servidor com o SQL Server Object Explorer no SSDT, SSMS, ou na sua cadeia de ligação da aplicação. Por exemplo, inclua o parâmetro InitialCatalog para uma ligação de ODBC.
* Realce a base de dados do utilizador antes de criar uma sessão no SSDT.

> [!NOTE]
> A instrução de Transact-SQL **utilize MyDatabase;** não é suportada para alterar a base de dados para uma ligação. Para obter orientações sobre a ligação ao SQL Data Warehouse com SSDT, consulte o [consulta com o Visual Studio] [ Query with Visual Studio] artigo.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do Azure Active Directory (AAD)
[Azure Active Directory] [ What is Azure Active Directory] a autenticação é um mecanismo de ligar ao Microsoft Azure SQL Data Warehouse, utilizando as identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure Active Directory, pode gerir centralmente as identidades de utilizadores de base de dados e outros serviços Microsoft numa localização central. Gestão de ID central fornece um único local para gerir utilizadores do SQL Data Warehouse e simplifica a gestão de permissão. 

### <a name="benefits"></a>Benefícios
Vantagens do Azure Active Directory incluem:

* Fornece uma alternativa à autenticação do SQL Server.
* Ajuda a parar a proliferação de identidades de utilizador em servidores de base de dados.
* Permite a rotação de palavra-passe num único local
* Gerir permissões de base de dados através de grupos do externos (AAD).
* Elimina armazenar palavras-passe através da autenticação integrada do Windows e outras formas de autenticação suportados pelo Azure Active Directory.
* Utilizadores de base de dados utiliza contido para autenticar identidades ao nível da base de dados.
* Suporta a autenticação baseada em tokens para aplicações de ligação ao SQL Data Warehouse.
* Suporta autenticação Multifator através da autenticação de Universal do Active Directory para SQL Server Management Studio. Para obter uma descrição do multi-factor Authentication, consulte [SSMS suporte para o MFA do Azure AD com base de dados SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Azure Active Directory é ainda relativamente novo e tem algumas limitações. Para garantir que o Azure Active Directory é uma boa opção para o seu ambiente, consulte [funcionalidades do Azure AD e limitações][Azure AD features and limitations], especificamente as considerações adicionais.
> 
> 

### <a name="configuration-steps"></a>Passos de configuração
Siga estes passos para configurar a autenticação do Azure Active Directory.

1. Criar e preencher um Azure Active Directory
2. Opcional: Associar ou altere o active directory que está atualmente associado a sua subscrição do Azure
3. Crie um administrador do Azure Active Directory para o Azure SQL Data Warehouse.
4. Configurar os computadores cliente
5. Criar utilizadores de base de dados contida na base de dados mapeado para as identidades do Azure AD
6. Ligar ao seu armazém de dados através da utilização de identidades do Azure AD

Atualmente, os utilizadores do Azure Active Directory não são apresentados no SSDT Object Explorer. Como solução, ver os utilizadores [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Localizar os detalhes
* Os passos para configurar e utilizar a autenticação do Azure Active Directory são praticamente idênticos para SQL Database do Azure e Azure SQL Data Warehouse. Siga os passos detalhados no tópico [ligar à base de dados SQL ou SQL dados do armazém por utilizar o Azure autenticação do Active Directory](../sql-database/sql-database-aad-authentication.md).
* Criar funções de base de dados personalizada e adicionar utilizadores a funções. Em seguida, conceda permissões granulares para as funções. Para obter mais informações, consulte [introdução de permissões do motor de base de dados](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Passos seguintes
Para começar a consultar o armazém de dados com o Visual Studio e outras aplicações, consulte [consulta com o Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations

---
title: "Descrição Geral do Servidor Lógico da Base de Dados SQL do Azure | Microsoft Docs"
description: "Esta página fornece considerações e diretrizes para trabalhar com os servidores lógicos de SQL do Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: d772dabf84fc3b6c061c0f4607c989aabf9dd272
ms.openlocfilehash: 942638cfd670bc25804a7e2babeffda58fd38995


---
# <a name="azure-sql-database-logical-servers"></a>Servidores lógicos da Base de Dados SQL do Azure

Este tópico fornece considerações e diretrizes para trabalhar com os servidores lógicos de SQL do Azure. Para obter informações sobre as bases de dados SQL do Azure, veja [Bases de dados SQL](sql-database-overview.md).

## <a name="what-is-an-azure-sql-database-logical-server"></a>O que é um servidor lógico da Base de Dados SQL do Azure?
Um servidor lógico da Base de Dados SQL do Azure funciona como um ponto administrativo central para várias bases de dados. Na Base de Dados SQL, um servidor é uma construção lógica distinta de uma instância do SQL Server com a qual poderá estar familiarizado no mundo no local. Especificamente, o serviço Base de Dados SQL não dá qualquer garantia relativamente à localização das bases de dados em relação aos respetivos servidores lógicos, nem expõe qualquer acesso de nível de instância ou funcionalidades. Para obter mais informações sobre servidores lógicos de SQL do Azure, veja [Servidores lógicos](sql-database-server-overview.md). 

Um servidor lógico da Base de Dados do Azure:

- É criado numa subscrição do Azure, mas pode ser movido com os recursos nele contidos para outra subscrição
- É o recurso principal para bases de dados, conjuntos elásticos e armazéns de dados
- Fornece um espaço de nomes para bases de dados, conjuntos elásticos e armazéns de dados
- É um contentor lógico com semântica de duração forte - elimine um servidor e este elimina as bases de dados, os conjuntos elásticos e os armazéns de dados nele contidos
- Participa no controlo de acesso baseado em funções (RBAC) do Azure; as bases de dados e os conjuntos elásticos dentro de um servidor herdam direitos de acesso do servidor
- É um elemento significativo da identidade de bases de dados e conjuntos elásticos para fins de gestão de recursos do Azure (veja o esquema de URL para bases de dados e conjuntos)
- Coloca recursos numa região
- Fornece um ponto final de ligação para o acesso à base de dados (<serverName>.database.windows.net)
- Fornece acesso a metadados relativamente aos recursos contidos através de DMVs, estabelecendo ligação a uma base de dados mestra 
- Fornece o âmbito para políticas de gestão que se aplicam às respetivas bases de dados: inícios de sessão, firewall, auditoria, deteção de ameaças, etc. 
- Está limitado por uma quota na subscrição principal (seis servidores por subscrição - [veja Limites de subscrição aqui](../azure-subscription-service-limits.md))
- Fornece o âmbito para a quota da base de dados e a quota de DTU para os recursos que contém (por exemplo, 45000 DTU no V12)
- É o âmbito do controlo de versões para capacidades ativadas nos recursos contidos (a versão mais recente é V12)
- Os inícios de sessão principais ao nível do servidor podem gerir todas as bases de dados num servidor
- Pode conter inícios de sessão semelhantes àqueles nas instâncias do SQL Server no local, aos quais é concedido acesso a uma ou mais bases de dados no servidor e aos quais podem ser concedidos direitos administrativos limitados. Para obter mais informações, veja [Inícios de sessão](sql-database-manage-logins.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>Como posso ligar-me e autenticar-me num servidor lógico da Base de Dados SQL do Azure?

- **Autenticação e autorização**: a Base de Dados SQL do Azure suporta a autenticação SQL e a Autenticação do Azure Active Directory (com determinadas limitações - veja [Ligar à Base de Dados SQL com a Autenticação do Azure Active Directory](sql-database-aad-authentication.md) para autenticação. Pode ligar e autenticar-se nas bases de dados SQL do Azure através da base de dados mestra do servidor ou diretamente a uma base de dados de utilizador. Para obter mais informações, veja [Gerir Bases de Dados e Inícios de Sessão na Base de Dados SQL do Azure](sql-database-manage-logins.md). A autenticação do Windows não é suportada. 
- **TDS**: a Base de Dados SQL do Microsoft Azure suporta a versão 7.3 ou posterior do cliente de protocolo TDS.
- **TCP/IP**: apenas são permitidas ligações TCP/IP.
- **Firewall da Base de Dados SQL**: para ajudar a proteger os seus dados, uma firewall da Base de Dados SQL impede todo o acesso ao servidor da base de dados ou às respetivas bases de dados até especificar que computadores têm permissão. Veja [Firewalls](sql-database-firewall-configure.md)

## <a name="what-collations-are-supported"></a>Quais são os agrupamentos suportados?

O agrupamento de base de dados predefinido utilizado pela Base de Dados SQL do Microsoft Azure (incluindo a base de dados mestra) é **SQL_LATIN1_GENERAL_CP1_CI_AS**, onde **LATIN1_GENERAL** é inglês (Estados Unidos), **CP1** é o código de página 1252, **CI** é sensível a maiúsculas e minúsculas e **AS** é sensível a acentos. Não é recomendado alterar o agrupamento para bases de dados V12 após a criação. Para obter mais informações sobre agrupamentos, veja [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Quais são os requisitos de nomenclatura para objetos de base de dados?

Os nomes de todos os novos objetos têm de estar em conformidade com as regras do SQL Server para identificadores. Para obter mais informações, veja [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported"></a>Que funcionalidades são suportadas?

Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md). Veja também [Diferenças do Transact-SQL da Base de dados SQL do Azure](sql-database-transact-sql-information.md) para obter mais contexto sobre as razões para a falta de suporte de determinados tipos de funcionalidades.

## <a name="how-do-i-manage-a-logical-server"></a>Como posso gerir um servidor lógico?

Pode gerir os servidores lógicos da Base de Dados SQL do Azure através de vários métodos:
- [Portal do Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre o serviço Base de Dados SQL do Azure, veja [O que é a Base de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md)
- Para obter uma descrição geral das bases de dados SQL do Azure, veja [Descrição geral da base de dados SQL](sql-database-overview.md)
- Para obter informações sobre o suporte de Transact-SQL e as diferenças, veja [Diferenças do Transact-SQL da Base de Dados SQL do Azure](sql-database-transact-sql-information.md).
- Para obter informações sobre quotas e limitações de recursos específicas com base na sua **camada de serviços**. Para obter uma descrição geral de camadas de serviços, veja [Camadas de serviços da Base de Dados SQL](sql-database-service-tiers.md).
- Para obter diretrizes relacionadas com segurança, veja [Diretrizes e Limitações de Segurança da Base de Dados SQL do Azure](sql-database-security-guidelines.md).
- Para obter informações sobre a disponibilidade de controlador e o suporte da Base de Dados SQL, veja [Bibliotecas de Ligação para a Base de Dados SQL e o SQL Server](sql-database-libraries.md).




<!--HONumber=Dec16_HO1-->



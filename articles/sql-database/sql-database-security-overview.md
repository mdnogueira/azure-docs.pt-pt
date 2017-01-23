---
title: "Descrição Geral da Segurança da Base de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre a segurança da Base de Dados SQL do Azure e do SQL Server, incluindo as diferenças entre a cloud e o SQL Server no local relativamente à autenticação, autorização, segurança de ligação, encriptação e conformidade."
services: sql-database
documentationcenter: 
author: tmullaney
manager: jhubbard
editor: 
ms.assetid: a012bb85-7fb4-4fde-a2fc-cf426c0a56bb
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 06/09/2016
ms.author: thmullan;jackr
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 9ebf3cd290151449c05c89307500fa5679de3b09


---
# <a name="securing-your-sql-database"></a>Proteger a sua Base de Dados SQL

Este artigo explica como utilizar a Base de Dados SQL do Azure para proteger a camada de dados de uma aplicação. Em particular, este artigo irá ajudá-lo a começar a utilizar recursos para proteger dados, controlar o acesso e a monitorização proativa. O diagrama seguinte ilustra as camadas de segurança fornecida pela Base de Dados SQL.

![Segurança e conformidade de SQL](./media/sql-database-security-overview/diagram.png)

Para obter uma descrição geral completa das funcionalidades de segurança disponíveis em todos os tipos de SQL, veja [Security Center for SQL Server Database Engine and Azure SQL Database (Centro de Segurança do Motor da Base de Dados SQL e da Base de Dados SQL do Azure)](https://msdn.microsoft.com/library/bb510589). Também estão disponíveis informações adicionais no [documento técnico Security and Azure SQL Database (A Base de Dados SQL do Azure e a Segurança)](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="protect-data"></a>Proteger os dados
A Base de Dados SQL protege os seus dados ao fornecer encriptação para dados em movimento com [Transport Layer Security](https://support.microsoft.com/en-us/kb/3135244), para dados inativos com [Encriptação de Dados Transparente](http://go.microsoft.com/fwlink/?LinkId=526242) e para dados em utilização com [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Para ver um debate da utilização destas funcionalidades de proteção de dados na Base de Dados SQL, veja [Proteção de dados e segurança](sql-database-protect-data.md).

> [!IMPORTANT]
>Todas as ligações à Base de Dados SQL do Azure precisam de encriptação (SSL/TLS) em todos os momentos enquanto os dados se encontram "em trânsito" para e a partir da base de dados. Na cadeia de ligação da sua aplicação, tem de especificar parâmetros para encriptar a ligação e *não* para confiar no certificado de servidor (isto é feito por si se copiar a cadeia de ligação fora do Portal Clássico do Azure), caso contrário, a ligação não irá verificar a identidade do servidor e estará suscetível a ataques man-in-the-middle. Para o controlador ADO.NET, por exemplo, estes parâmetros da cadeia de ligação são **Encrypt=True** e **TrustServerCertificate=False**. 

Para outras formas de encriptar os seus dados, considere:

* A [encriptação ao nível da célula](https://msdn.microsoft.com/library/ms179331.aspx), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
* Se precisar de um Módulo de Segurança de Hardware ou de fazer a gestão centralizada da hierarquia de chaves de encriptação, considere utilizar o [Azure Key Vault com o SQL Server numa VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

## <a name="control-access"></a>Controlar o acesso
A Base de Dados SQL protege os seus dados ao limitar o acesso à base de dados com regras de firewall, mecanismos de autenticação que exigem que os utilizadores forneçam a sua identidade e autorização a dados através de permissões e associações baseadas em funções, bem como através de segurança ao nível da linha e máscara de dados dinâmicos. Para ver um debate da utilização das funcionalidades de controlo de acesso na Base de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).

> [!IMPORTANT]
> A gestão de bases de dados e servidores lógicos no Azure é controlada pelas atribuições de função da sua conta de utilizador do portal. Para obter mais informações sobre este tópico, veja [Controlo de acesso baseado em funções no Portal do Azure](../active-directory/role-based-access-control-configure.md).
>

### <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall
Para ajudar a proteger os seus dados, as firewalls impedem todo o acesso ao seu servidor de base de dados enquanto não especificar que computadores têm permissão com [regras de firewall](sql-database-firewall-configure.md). A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

### <a name="authentication"></a>Autenticação
A autenticação da Base de Dados SQL diz respeito à forma como o utilizador prova a sua identidade quando se liga à base de dados. A Base de Dados SQL suporta dois tipos de autenticação:

* **Autenticação SQL**, que utiliza um nome de utilizador e palavra-passe. Quando criou o servidor lógico para a sua base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Com estas credenciais, pode autenticar-se em qualquer base de dados nesse servidor como o proprietário da base de dados ou "dbo". 
* **Autenticação do Azure Active Directory**, que utiliza identidades geridas pelo Azure Active Directory e é suportada para domínios geridos e integrados. Utilize a autenticação do Active Directory (segurança integrada) [sempre que possível](https://msdn.microsoft.com/library/ms144284.aspx). Se pretender utilizar a autenticação do Azure Active Directory, tem de criar outro administrador de servidor, denominado "Administrador do Azure AD", que tem permissão para administrar utilizadores e grupos do Azure AD. Este administrador também pode fazer todas as operações que um administrador de servidor normal faz. Veja [Connecting to SQL Database By Using Azure Active Directory Authentication (Utilizar a Autenticação do Azure Active Directory para Ligar à Base de Dados SQL)](sql-database-aad-authentication.md) para obter instruções sobre como criar um administrador do Azure AD e ativar a Autenticação do Azure Active Directory.

### <a name="authorization"></a>Autorização
A autorização diz respeito ao que um utilizador pode fazer dentro de uma Base de Dados SQL do Azure e é controlada pelas permissões de nível de objeto e associações das funções de base de dados da sua conta de utilizador. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação.

### <a name="row-level-security"></a>Segurança ao Nível da Linha
A Segurança ao Nível da Linha permite aos clientes controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que executa uma consulta (por exemplo, associação a um grupo ou contexto de execução). Para obter mais informações, veja [Segurança ao Nível da Linha](https://msdn.microsoft.com/library/dn765131).

### <a name="data-masking"></a>Máscara de dados 
A Máscara de Dados Dinâmicos da Base de Dados SQL limita a exposição de dados confidenciais ao mascará-los para utilizadores sem privilégios. A Máscara de Dados Dinâmicos deteta automaticamente dados potencialmente confidenciais na Base de Dados SQL do Azure e fornece recomendações passíveis de ação para mascarar estes campos, com um impacto mínimo na camada da aplicação. Funciona ao ofuscar os dados confidenciais no conjunto de resultados de uma consulta em campos de base de dados designados, enquanto os dados na base de dados não são alterados. Para obter mais informações, veja [Introdução à Máscara de Dados Dinâmicos da Base de Dados SQL](sql-database-dynamic-data-masking-get-started.md), que pode ser utilizada para limitar a exposição de dados confidenciais.

## <a name="proactive-monitoring"></a>Monitorização proativa
A Base de Dados SQL protege os seus dados, fornecendo capacidades de auditoria e deteção de ameaças. 

### <a name="auditing"></a>Auditoria
A Auditoria da Base de Dados SQL controla as atividades de base de dados e ajuda a manter a conformidade regulamentar, ao registar eventos de base de dados num registo de auditoria na conta de Armazenamento do Azure. A Auditoria permite-lhe compreender as atividades de base de dados em curso, bem como analisar e investigar atividade do histórico para identificar potenciais ameaças ou suspeita de abuso e violações de segurança. Para obter informações adicionais, veja [Introdução à Auditoria da Base de Dados SQL](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Deteção de ameaças
A Deteção de Ameaças complementa a auditoria, fornecendo uma camada adicional de análise de segurança incorporada no serviço Base de Dados SQL do Azure. Funciona continuamente para aprender, traçar o perfil e detetar atividades anómalas da base de dados. O utilizador será alertado relativamente a atividades suspeitas, potenciais vulnerabilidades, ataques de injeção de SQL e padrões de acesso anómalos à base de dados. Pode responder a alertas, seguindo as instruções informativas e passíveis de ação fornecidas. Para obter mais informações, veja [Get started with SQL Database Threat Detection (Introdução à Deteção de Ameaças da Base de Dados SQL)](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformidade
Para além das funcionalidades anteriores que podem ajudar a sua aplicação a cumprir os diversos requisitos de compatibilidade de segurança, a Base de Dados SQL do Azure também participa em auditorias regulares e tem certificação de uma série de normas de conformidade. Para obter mais informações, veja o [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde pode encontrar a lista mais recente de [certificações de conformidade da Base de Dados SQL](https://azure.microsoft.com/support/trust-center/services/).

## <a name="next-steps"></a>Passos seguintes

- Para ver um debate da utilização das funcionalidades de proteção de dados na Base de Dados SQL, veja [Proteção de dados e segurança](sql-database-protect-data.md).
- Para ver um debate da utilização das funcionalidades de controlo de acesso na Base de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).
- Para ver um debate sobre a monitorização proativa, veja [Get started with SQL Database Auditing (Introdução à Auditoria da Base de Dados SQL)](sql-database-auditing-get-started.md) e [Get started with SQL Database Threat Detection (Introdução à Deteção de Ameaças da Base de Dados SQL)](sql-database-threat-detection-get-started.md).


<!--HONumber=Dec16_HO4-->



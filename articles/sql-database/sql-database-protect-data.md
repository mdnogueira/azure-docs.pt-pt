---
title: "Descrição Geral da Proteção de Dados da Base de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre a proteção de dados na base de dados SQL do Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>Proteger dados na sua Base de Dados SQL

A Base de Dados SQL protege os seus dados através de encriptação.  

## <a name="overview"></a>Descrição geral

A Base de Dados SQL protege os seus dados ao fornecer encriptação para dados em movimento com [Transport Layer Security](https://support.microsoft.com/en-us/kb/3135244), para dados inativos com [Encriptação de Dados Transparente](http://go.microsoft.com/fwlink/?LinkId=526242) e para dados em utilização com [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Para ver um debate da utilização destas funcionalidades de proteção de dados na Base de Dados SQL, veja [Proteção de dados e segurança](sql-database-protect-data.md).

Para outras formas de encriptar os seus dados, considere:

* A [encriptação ao nível da célula](https://msdn.microsoft.com/library/ms179331.aspx), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
* Se precisar de um Módulo de Segurança de Hardware ou de fazer a gestão centralizada da hierarquia de chaves de encriptação, considere utilizar o [Azure Key Vault com o SQL Server numa VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).


A Base de Dados SQL protege os seus dados, fornecendo capacidades de auditoria e deteção de ameaças. 

### <a name="auditing"></a>Auditoria
A Auditoria da Base de Dados SQL controla as atividades de base de dados e ajuda a manter a conformidade regulamentar, ao registar eventos de base de dados num registo de auditoria na conta de Armazenamento do Azure. A Auditoria permite-lhe compreender as atividades de base de dados em curso e analisar e investigar atividade do histórico para identificar potenciais ameaças ou suspeita de abuso e violações de segurança. Para obter informações adicionais, veja [Introdução à Auditoria da Base de Dados SQL](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Deteção de ameaças
A Deteção de Ameaças complementa a auditoria, fornecendo uma camada adicional de análise de segurança incorporada no serviço Base de Dados SQL do Azure. Funciona continuamente para aprender, traçar o perfil e detetar atividades anómalas da base de dados. O utilizador é alertado relativamente a atividades suspeitas, potenciais vulnerabilidades, ataques de injeção de SQL e padrões de acesso anómalos à base de dados. Pode responder a alertas, seguindo as instruções informativas e passíveis de ação fornecidas. Para obter mais informações, veja [Get started with SQL Database Threat Detection (Introdução à Deteção de Ameaças da Base de Dados SQL)](sql-database-threat-detection-get-started.md).  

## <a name="connection-security"></a>Segurança da Ligação
A Segurança da Ligação diz respeito à forma como restringe e protege as ligações à sua base de dados através de regras de firewall e de encriptação da ligação.

As regras de firewall são utilizadas pelo servidor e pela base de dados para rejeitar as tentativas de ligação de endereços IP que não estejam explicitamente na lista de permissões. Par permitir que o endereço IP público da sua aplicação ou do seu computador cliente tente ligar a uma base de dados nova, tem, primeiro, de criar uma regra de firewall ao nível do servidor através do Portal Clássico do Azure, da API REST ou do PowerShell. Como melhor prática, deve restringir o máximo possível os intervalos de endereços IP permitidos na firewall do servidor. Para obter mais informações, veja [Firewall da Base de Dados SQL do Azure](https://msdn.microsoft.com/library/ee621782).

Todas as ligações à Base de Dados SQL do Azure precisam de encriptação (SSL/TLS) em todos os momentos enquanto os dados se encontram "em trânsito" para e a partir da base de dados. Na cadeia de ligação da sua aplicação, tem de especificar parâmetros para encriptar a ligação e para *não* confiar no certificado de servidor (isto é feito por si se copiar a cadeia de ligação fora do Portal Clássico do Azure). Caso contrário, a ligação não poderá verificar a identidade do servidor e fica suscetível a ataques man-in-the-middle. Para o controlador ADO.NET, por exemplo, estes parâmetros da cadeia de ligação são **Encrypt=True** e **TrustServerCertificate=False**. Para obter mais informações, veja [Azure SQL Database Connection Encryption and Certificate Validation (Encriptação da Ligação e Validação do Certificado da Base de Dados SQL do Azure)](https://msdn.microsoft.com/library/azure/ff394108#encryption).

## <a name="authentication"></a>Autenticação
A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. A Base de Dados SQL suporta dois tipos de autenticação:

* **Autenticação SQL**, que utiliza um nome de utilizador e palavra-passe
* A **Autenticação do Azure Active Directory**, que utiliza identidades geridas pelo Azure Active Directory e é suportada para domínios geridos e integrados

Quando criou o servidor lógico para a sua base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Com estas credenciais, pode autenticar-se em qualquer base de dados nesse servidor como o proprietário da base de dados ou "dbo". Se pretender utilizar a autenticação do Azure Active Directory, tem de criar outro administrador de servidor, denominado "Administrador do Azure AD", que tem permissão para administrar utilizadores e grupos do Azure AD. Este administrador também pode fazer todas as operações que um administrador de servidor normal faz. Veja [Connecting to SQL Database By Using Azure Active Directory Authentication (Utilizar a Autenticação do Azure Active Directory para Ligar à Base de Dados SQL)](sql-database-aad-authentication.md) para obter instruções sobre como criar um administrador do Azure AD e ativar a Autenticação do Azure Active Directory.

Como melhor prática, a sua aplicação deve utilizar uma conta diferente para se autenticar. Desta forma, pode limitar as permissões concedidas à aplicação e reduzir os riscos de atividades maliciosas, caso o código da aplicação seja vulnerável a um ataque de injeção SQL. A abordagem recomendada consiste em criar um [utilizador de base de dados contido](https://msdn.microsoft.com/library/ff929188), que permite que a sua aplicação se autentique diretamente na base de dados. Pode criar um utilizador de base de dados contido que utilize a Autenticação SQL ao executar o comando T-SQL seguinte enquanto está ligado à sua base de dados de utilizador como o início de sessão de administrador do servidor:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Se criou um administrador do Azure AD, pode criar um utilizador de base de dados contido que utilize a Autenticação do Azure Active Directory ao executar o comando T-SQL seguinte enquanto está ligado à sua base de dados de utilizador como o administrador do Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

Em ambos os casos, a cadeia de ligação da aplicação deve especificar estas credenciais de utilizador, em vez do início de sessão do administrador de servidor, para ligar à base de dados.

Para obter mais informações sobre como se autenticar numa Base de Dados SQL, veja [Managing Databases and Logins in Azure SQL Database (Gerir Bases de Dados e Inícios de Sessão na Base de Dados SQL do Azure)](sql-database-manage-logins.md).

## <a name="authorization"></a>Autorização
A autorização diz respeito ao que pode fazer dentro de uma Base de Dados SQL do Azure e é controlada pelas permissões e associações das funções da sua conta de utilizador. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. A Base de Dados SQL do Azure facilita esta gestão com funções no T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação.

Existem formas de limitar ainda mais o que um utilizador pode fazer com a Base de Dados SQL do Azure:

* As [Funções de Base de Dados](https://msdn.microsoft.com/library/ms189121) que não sejam db_datareader e db_datawriter podem ser utilizadas para criar contas de utilizador de aplicação mais poderosas ou contas de gestão menos poderosas.
* As [permissões](https://msdn.microsoft.com/library/ms191291) granulares permitem-lhe controlar as operações que pode realizar em colunas, tabelas, vistas, procedimentos e outros objetos individuais na base de dados.
* A [representação](https://msdn.microsoft.com/library/vstudio/bb669087) e a [assinatura de módulo](https://msdn.microsoft.com/library/bb669102) podem ser utilizadas para elevar temporariamente permissões de forma segura.
* A [Segurança ao Nível da Linha](https://msdn.microsoft.com/library/dn765131) pode ser utilizada para limitar as linhas a que um utilizador pode aceder.
* A [Máscara de Dados](sql-database-dynamic-data-masking-get-started.md) pode ser utilizada para limitar a exposição de dados confidenciais.
* Os [Procedimentos armazenados](https://msdn.microsoft.com/library/ms190782) podem ser utilizados para limitar as ações que podem ser realizadas na base de dados.

A gestão de bases de dados e servidores lógicos no Portal Clássico do Azure ou com a API do Azure Resource Manager é controlada pelas atribuições de função da sua conta de utilizador do portal. Para obter mais informações sobre este tópico, veja [Controlo de acesso baseado em funções no Portal do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encryption"></a>Encriptação
A Base de Dados SQL do Azure pode ajudá-lo a proteger os seus dados ao encriptar os dados quando estão "inativos" ou armazenados em ficheiros e cópias de segurança de bases de dados, através da [Encriptação de Dados Transparente](http://go.microsoft.com/fwlink/?LinkId=526242). Para encriptar a base de dados, ligue como proprietário da base de dados e execute:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Para outras formas de encriptar os segredos dos dados, considere:

* A [encriptação ao nível da célula](https://msdn.microsoft.com/library/ms179331.aspx), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
* Se precisar de um Módulo de Segurança de Hardware ou de fazer a gestão centralizada da hierarquia de chaves de encriptação, considere utilizar o [Azure Key Vault com o SQL Server numa VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* O [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) (em pré-visualização), que facilita a encriptação transparente de aplicações e permite que os clientes encriptem dados confidenciais dentro das aplicações cliente, sem partilhar as chaves de encriptação com a Base de Dados SQL.

## <a name="auditing"></a>Auditoria
Auditar e acompanhar os eventos de bases de dados pode ajudá-lo a manter a conformidade com as regulamentações e a identificar atividades suspeitas. A Auditoria da Base de Dados SQL permite-lhe registar os eventos da sua base de dados num registo de auditoria na sua conta de Armazenamento do Azure. A Auditoria da Base de Dados SQL também pode ser integrada no Microsoft Power BI para facilitar a leitura detalhada de relatórios e análises. Para obter mais informações, veja [Get started with SQL Database Auditing (Introdução à Auditoria da Base de Dados SQL)](sql-database-auditing-get-started.md).

A Deteção de Ameaças da Base de Dados SQL proporciona uma camada adicional de segurança, para além da Auditoria. Mostra alertas de segurança relativamente a atividades anómalas, o que lhe permite responder a ameaças à medida que ocorrem. Para obter mais informações, veja [Get started with SQL Database Threat Detection (Introdução à Deteção de Ameaças da Base de Dados SQL)](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformidade
Para além das funcionalidades anteriores que podem ajudar a sua aplicação a cumprir os diversos requisitos de compatibilidade de segurança, a Base de Dados SQL do Azure também participa em auditorias regulares e tem certificação de uma série de normas de conformidade. Para obter mais informações, veja o [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde pode encontrar a lista mais recente de [certificações de conformidade da Base de Dados SQL](https://azure.microsoft.com/support/trust-center/services/).


## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral de todas as funcionalidades de segurança da Base de Dados SQL, veja [Descrição geral da segurança de SQL](sql-database-security-overview.md).
- Para ver um debate da utilização das funcionalidades de controlo de acesso na Base de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).
- Para ver um debate sobre a monitorização proativa, veja [Get started with SQL Database Auditing (Introdução à Auditoria da Base de Dados SQL)](sql-database-auditing-get-started.md) e [Get started with SQL Database Threat Detection (Introdução à Deteção de Ameaças da Base de Dados SQL)](sql-database-threat-detection-get-started.md).



<!--HONumber=Dec16_HO4-->



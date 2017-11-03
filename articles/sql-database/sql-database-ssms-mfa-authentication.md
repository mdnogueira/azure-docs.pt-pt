---
title: "Autenticação Multifator - SQL do Azure | Microsoft Docs"
description: "SQL Database do Azure e o Azure SQL Data Warehouse suportam ligações do SQL Server Management Studio (SSMS) utilizando a autenticação de Universal do Active Directory."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/29/2017
ms.author: rickbyh
ms.openlocfilehash: bd5a7a117ec6c4a4b75fcced3f2b5987004c83d0
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Autenticação universal com a base de dados SQL e SQL Data Warehouse (SSMS suporte para a MFA)
SQL Database do Azure e o Azure SQL Data Warehouse suportam ligações da utilização do SQL Server Management Studio (SSMS) *autenticação de Universal do Active Directory*. 
**Transferir o SSMS mais recente** - no computador cliente, transfira a versão mais recente do SSMS, de [transferir SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Para todas as funcionalidades neste tópico, utilize, pelo menos, Julho de 2017, versão 17.2.  A caixa de diálogo de ligação mais recente, tem o seguinte aspeto: ![1mfa universal ligar](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "conclui a caixa de nome de utilizador.")  

## <a name="the-five-authentication-options"></a>As opções de cinco autenticação  
- Autenticação de Universal do Active Directory suporta os dois métodos de autenticação não interativa (`Active Directory - Password` autenticação e `Active Directory - Integrated` autenticação). Não interativo `Active Directory - Password` e `Active Directory - Integrated` métodos de autenticação podem ser utilizados em muitas aplicações diferentes (ADO.NET, JDBC, ODBC, etc.). Estes dois métodos nunca resultam nas caixas de diálogo de pop-up.

- `Active Directory - Universal with MFA`a autenticação é um método interativo que também suporta *Azure multi-factor Authentication* (MFA). A Azure MFA ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples. Fornece autenticação forte com uma gama de opções de verificação fácil (chamada telefónica, mensagem de texto, os smart cards com pin ou a notificação da aplicação móvel), permitindo aos utilizadores escolher o métodos preferem. Interativo MFA com o Azure AD pode resultar numa caixa de diálogo de pop-up para validação.

Para obter uma descrição do multi-factor Authentication, consulte [multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
Para obter passos de configuração, consulte [configurar a base de dados de SQL do Azure multi-factor authentication para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD domínio nome ou o inquilino parâmetro ID   

Começando com [SSMS versão 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), os utilizadores que são importados para o Active Directory atual de outros diretórios de Active Directory do Azure como os utilizadores convidados, pode fornecer o nome de domínio do Azure AD ou ID de inquilino quando se ligam. Os utilizadores convidados incluem utilizadores convidados a partir de outros anúncios do Azure, as contas Microsoft, tais como outlook.com, hotmail.com, live.com ou outras contas como gmail.com. Estas informações, permite **Active Directory universais com autenticação de MFA** para identificar a autoridade de autenticação correta. Esta opção também é necessária para suportar as contas Microsoft (MSA), tais como o outlook.com, hotmail.com, live.com ou contas não MSA. Estes utilizadores que pretendem ser autenticado utilizando a autenticação Universal tem de introduzir o respetivo nome de domínio do Azure AD ou ID de inquilino Este ID de inquilino/nome de domínio de representa o Azure AD atual de parâmetro o servidor do Azure está ligada. Por exemplo, se o servidor do Azure está associado ao domínio do Azure AD `contosotest.onmicrosoft.com` onde utilizador `joe@contosodev.onmicrosoft.com` está alojado como um utilizador de domínio do Azure AD importado `contosodev.onmicrosoft.com`, o nome de domínio necessário para autenticar este utilizador é `contosotest.onmicrosoft.com`. Quando o utilizador é um utilizador nativo do Azure AD ligado ao servidor do Azure e não é uma conta MSA, nenhum ID de nome ou o inquilino do domínio é necessário. Para introduzir o parâmetro (início com o SSMS versão 17.2), no **ligar à base de dados** caixa de diálogo caixa, preencha a caixa de diálogo Selecionar **do Active Directory - Universal com a MFA** autenticação, clique em  **Opções**, concluir o **nome de utilizador** caixa e, em seguida, clique em de **propriedades de ligação** separador. Verifique o **ID de nome ou o inquilino de domínio do AD** caixa e fornecer a autoridade de autenticação, tais como o nome de domínio (**contosotest.onmicrosoft.com**) ou o GUID do ID de inquilino.  
   ![ssms de inquilino de mfa](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Suporte de negócio para empresas do Azure AD   
Suportado para cenários B2B do Azure AD, como os utilizadores convidados os utilizadores do AD do Azure (consulte [o que é a colaboração B2B do Azure](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) pode ligar à base de dados SQL e do armazém de dados do SQL Server apenas como parte de membros de um grupo criado no Azure AD atual e mapeado manualmente com o Transact-SQL `CREATE USER` declaração numa base de dados fornecido. Por exemplo, se `steve@gmail.com` está convidado para o Azure AD `contosotest` (com o domínio do Azure Ad `contosotest.onmicrosoft.com`), grupo de um Azure AD, tal como `usergroup` tem de ser criada no Azure AD que contém o `steve@gmail.com` membro. Em seguida, este grupo tem de ser criado numa base de dados específicos (ou seja, MyDatabase) pelo administrador de SQL do Azure AD ou Azure AD DBO executando Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` instrução. Depois do utilizador de base de dados criado, em seguida, o utilizador `steve@gmail.com` pode iniciar sessão `MyDatabase` utilizando a opção de autenticação do SSMS `Active Directory – Universal with MFA support`. Por predefinição, o grupo de utilizador, tem apenas a permissão connect e qualquer acesso a dados adicional que terá de ser concedido da forma normal. Tenha em atenção que o utilizador `steve@gmail.com` como um utilizador convidado tem de selecionar a caixa e adicione o nome de domínio do AD `contosotest.onmicrosoft.com` no SSMS **ligação propriedade** caixa de diálogo. O **ID de nome ou o inquilino de domínio do AD** opção só é suportada para Universal com opções de ligação de MFA, caso contrário, se estiver a cinzento.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitações de autenticação universais para a base de dados SQL e do armazém de dados do SQL Server
- SSMS e SqlPackage.exe são as ferramentas apenas atualmente ativadas para a MFA através da autenticação de Universal do Active Directory.
- SSMS versão 17.2, suporta acesso de vários utilizadores em simultâneo através da autenticação Universal com a MFA. Versão 17.0 e 17.1, restringido a um início de sessão para uma instância do SSMS utilizando a autenticação Universal para uma única conta do Azure Active Directory. Para iniciar sessão como outra conta do Azure AD, tem de utilizar outra instância do SSMS. (Esta restrição é limitada a autenticação de Universal do Active Directory; pode iniciar sessão em servidores diferentes, utilizando a autenticação de palavra-passe do Active Directory, a autenticação integrada do Active Directory ou autenticação do SQL Server).
- SSMS suporta a autenticação de Universal do Active Directory para visualização Object Explorer, o Editor de consultas e o arquivo de consultas.
- SSMS versão 17.2 fornece suporte de assistente DacFx da base de dados de dados de exportação/extrair/implementar. Depois de um utilizador específico é autenticado através da caixa de diálogo de autenticação inicial através da autenticação Universal, as funções de assistente DacFx da mesma forma que realiza todos os outros métodos de autenticação.
- O estruturador de tabela do SSMS não suporta a autenticação Universal.
- Não existem não requisitos de software adicionais para autenticação de Universal do Active Directory, exceto que tem de utilizar uma versão suportada do SSMS.  
- A versão do Active Directory Authentication Library (ADAL) para autenticação Universal foi atualizada para a última versão lançada disponível ADAL.dll 3.13.9. Consulte [biblioteca de autenticação do Active Directory 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Passos seguintes

- Para obter passos de configuração, consulte [configurar a base de dados de SQL do Azure multi-factor authentication para SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Outras pessoas conceder acesso à base de dados: [autorização e autenticação de base de dados do SQL Server: conceder acesso](sql-database-manage-logins.md)  
- Certifique-se outros utilizadores podem ligar através da firewall: [configurar uma regra de firewall ao nível do servidor da SQL Database do Azure no portal do Azure](sql-database-configure-firewall-settings.md)  
- [Configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL ou SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [Importar um ficheiro BACPAC para uma nova SQL Database do Azure](../sql-database/sql-database-import.md)  
- [Exportar uma base de dados SQL do Azure para um ficheiro BACPAC](../sql-database/sql-database-export.md)  
- Interface de c# [IUniversalAuthProvider Interface](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Quando utilizar **Active Directory - Universal com a MFA** autenticação, o rastreio ADAL está disponível a partir com [SSMS 17.3](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). Desativado por predefinição, pode ativar o rastreio ADAL utilizando o **ferramentas**, **opções** menu, em **serviços do Azure**, **nuvem do Azure**,  **Nível de rastreio de janela de saída ADAL**, seguido pela ativação **saída** no **vista** menu. Os rastreios estão disponíveis na janela de saída quando selecionar **opção do Azure Active Directory**.  

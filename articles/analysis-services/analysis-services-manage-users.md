---
title: "Permissões de autenticação e utilizador no Azure Analysis Services | Microsoft Docs"
description: "Saiba mais sobre as permissões de autenticação e utilizador no Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/09/2017
ms.author: owend
ms.openlocfilehash: e7fdb55ba29fbdc2f3d89fbb19c8b77bf2c05795
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="authentication-and-user-permissions"></a>Permissões de autenticação e utilizador
Serviços de análise do Azure utiliza o Azure Active Directory (Azure AD) para autenticação de utilizador e gestão de identidade. Qualquer utilizador a criar, gerir ou ligar a um Azure Analysis Services server tem de ter uma identidade de utilizador válido [inquilino do Azure AD](../active-directory/active-directory-administer.md) na mesma subscrição.

Serviços de análise do Azure suporta [colaboração B2B do Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Com B2B, os utilizadores fora da organização podem ser convidados como os utilizadores convidados num diretório do Azure AD. Os convidados podem ser de outro diretório de inquilino do Azure AD ou qualquer endereço de correio eletrónico válido. Uma vez convidou e o utilizador aceitar o convite enviado por e-mail a partir do Azure, a identidade do utilizador é adicionada para o diretório do inquilino. Essas identidades podem ser adicionadas aos grupos de segurança ou como membros de uma função de administrador ou a base de dados do servidor.

![Arquitetura de autenticação do Analysis Services do Azure](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autenticação
Todas as aplicações de cliente e as ferramentas de utilizam um ou mais dos Analysis Services [bibliotecas de cliente](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD) para ligar a um servidor. 

Todas as bibliotecas de cliente três suportam fluxo interativo do Azure AD e métodos de autenticação não interativa. A dois métodos não interativa, palavra-passe de diretório Active Directory e os métodos de autenticação de integrada do Active Directory podem ser utilizados em aplicações utilizar AMOMD e MSOLAP. Estes dois métodos nunca resultam nas caixas de diálogo de pop-up.

Aplicações de cliente como o Excel e o ambiente de trabalho do Power BI e ferramentas como o SSMS e SSDT instalar as versões mais recentes das bibliotecas quando atualizado para a versão mais recente. Ambiente de trabalho do Power BI, o SSMS e o SSDT são atualizados mensalmente. É de Excel [atualizado com o Office 365](https://support.office.com/en-us/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Atualizações do Office 365 são menos frequentes e algumas organizações utilizarem o canal diferido, significado atualizações são deferidas cópias de segurança para durante três meses.

Consoante a aplicação de cliente ou a ferramenta que utilizar, o tipo de autenticação e como iniciar sessão pode ser diferente. Cada aplicação pode suportar funcionalidades diferentes para estabelecer ligação a serviços em nuvem como o Azure Analysis Services.

Ambiente de trabalho do Power BI, SSDT e SSMS suporta a autenticação do Active Directory Universal, um método interativo que também suporta o multi-factor Authentication (MFA) do Azure. Azure MFA ajuda a salvaguardar o acesso a dados e aplicações, fornecendo um processo de início de sessão simples. Fornece autenticação forte com várias opções de verificação (chamada telefónica, mensagem de texto, os smart cards com pin ou a notificação da aplicação móvel). Interativo MFA com o Azure AD pode resultar numa caixa de diálogo de pop-up para validação. **Recomenda-se a autenticação universal**.

Se iniciar sessão no Azure ao utilizar uma conta do Windows e a autenticação Universal não está selecionado ou a disponível (Excel), [serviços de Federação do Active Directory (AD FS)](../active-directory/connect/active-directory-aadconnect-azure-adfs.md) é necessária. Com a Federação, do Azure AD e utilizadores do Office 365 são autenticados utilizando as credenciais no local podem aceder aos recursos do Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Servidores de Analysis Services do Azure suportam ligações a partir de [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e superior, utilizando a autenticação do Windows, a autenticação de palavra-passe do Active Directory e a autenticação de Universal do Active Directory. Em geral, é recomendado que utilize autenticação de Universal do Active Directory porque:

*  Suporta métodos de autenticação interativa e não interativa.

*  Suporta os utilizadores convidados de B2B do Azure convidados para o inquilino AS do Azure. Ao ligar a um servidor, os utilizadores convidados tem de selecionar autenticação de Universal do Active Directory ao ligar ao servidor.

*  Suporta a autenticação Multifator (MFA). Azure MFA ajuda a salvaguardar o acesso a dados e aplicações com uma gama de opções de verificação: chamada telefónica, mensagem de texto, os smart cards com pin ou a notificação da aplicação móvel. Interativo MFA com o Azure AD pode resultar numa caixa de diálogo de pop-up para validação.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
SSDT estabelece ligação ao Analysis Services do Azure utilizando a autenticação de Universal do Active Directory com suporte MFA. Os utilizadores recebem um pedido para iniciar sessão no Azure, na primeira implementação. Os utilizadores tem de iniciar sessão Azure com uma conta com permissões de administrador no servidor que estão a implementar. Quando iniciar sessão no Azure pela primeira vez, é atribuído um token. SSDT coloca em cache a token de memória para futura volta a ligar.

### <a name="power-bi-desktop"></a>Ambiente de trabalho do Power BI
Ambiente de trabalho do Power BI liga-se ao utilizar a autenticação de Universal do Active Directory com suporte MFA do Azure Analysis Services. Os utilizadores recebem um pedido para iniciar sessão no Azure, na primeira ligação. Os utilizadores tem de iniciar sessão Azure com uma conta que está incluída numa função de base de dados ou administrador do servidor.

### <a name="excel"></a>Excel
Os utilizadores do Excel podem ligar a um servidor utilizando uma conta do Windows, um ID de organização (endereço de correio eletrónico) ou um endereço de e-mail externos. Identidades de e-mail externos tem de existir no Azure AD como utilizador convidado.

## <a name="user-permissions"></a>Permissões de utilizador

**Os administradores de servidores** são específicas para uma instância de servidor do Analysis Services do Azure. Se ligam-se com ferramentas como o portal do Azure, o SSMS e o SSDT para efetuar tarefas, como adicionar bases de dados e gerir funções de utilizador. Por predefinição, o utilizador que cria o servidor é automaticamente adicionado como um administrador de servidor do Analysis Services. Outros administradores podem ser adicionados ao utilizar o portal do Azure ou o SSMS. Os administradores de servidores tem de ter uma conta no inquilino do Azure AD na mesma subscrição. Para obter mais informações, consulte [gerir administradores de servidor](analysis-services-server-admins.md). 

**Os utilizadores de base de dados** ligar às bases de dados do modelo através da utilização de aplicações de cliente, como o Excel ou do Power BI. Os utilizadores têm de ser adicionados às funções de base de dados. Funções de base de dados definem permissões de leitura para uma base de dados, de processo ou de administrador. É importante compreender a utilizadores de base de dados numa função com permissões de administrador são diferentes dos administradores de servidor. No entanto, por predefinição, os administradores de servidores também são administradores de base de dados. Para obter mais informações, consulte [gerir funções de base de dados e os utilizadores](analysis-services-database-users.md).

**Os proprietários de recursos do Azure**. Os proprietários de recursos gerir os recursos para uma subscrição do Azure. Os proprietários de recursos podem adicionar identidades de utilizador do Azure AD para proprietário ou contribuinte funções numa subscrição utilizando **controlo de acesso** no portal do Azure, ou com modelos Azure Resource Manager. 

![Controlo de acesso no portal do Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Funções neste nível aplicam-se a utilizadores ou contas que precisam executar tarefas que podem ser executadas no portal ou utilizando os modelos Azure Resource Manager. Para obter mais informações, consulte [controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md). 


## <a name="database-roles"></a>Funções de base de dados

 Funções definidos para um modelo de tabela são funções de base de dados. Ou seja, as funções contenham membros constituídas por utilizadores do Azure AD e grupos de segurança que tenham permissões específicas que definem a ação esses membros podem demorar numa base de dados modelo. Uma função de base de dados é criada como um objeto separado na base de dados e aplica-se apenas à base de dados em que é criada essa função.   
  
 Por predefinição, quando cria um novo projeto de modelo de tabela, o projeto de modelo não tem quaisquer funções. Funções podem ser definidas utilizando a caixa de diálogo Gestor de funções no SSDT. Quando as funções são definidas durante a conceção do projeto de modelo, estas são aplicadas apenas a base de dados da área de trabalho do modelo. Quando o modelo é implementado, as funções do mesmas são aplicadas ao modelo de implementado. Após ter sido implementado um modelo, administradores de base de dados e servidor podem gerir funções e os membros com o SSMS. Para obter mais informações, consulte [gerir funções de base de dados e os utilizadores](analysis-services-database-users.md).
  


## <a name="next-steps"></a>Passos seguintes

[Gerir o acesso a recursos com grupos do Active Directory do Azure](../active-directory/active-directory-manage-groups.md)   
[Gerir utilizadores e funções de base de dados](analysis-services-database-users.md)  
[Gerir administradores de servidor](analysis-services-server-admins.md)  
[Controlo de Acesso Baseado em Funções](../active-directory/role-based-access-control-what-is.md)  
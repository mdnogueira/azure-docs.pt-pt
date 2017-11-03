---
title: "Aplicação inesperada na minha lista de aplicações | Microsoft Docs"
description: "Como ver todas as aplicações no seu inquilino e que compreende como as aplicações são apresentadas na lista de todas as aplicações em aplicações da empresa"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0f8136cb066fa6e3e4a8dd06e34b9f866e3916f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-application-in-my-applications-list"></a>Aplicação inesperada na minha lista de aplicações

Este artigo ajuda-o a compreender a forma como as aplicações são apresentadas no seu **todas as aplicações** lista em **aplicações empresariais**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Como ver todas as aplicações no seu inquilino

Para ver todas as aplicações no seu inquilino, tem de utilizar o **filtro** controlo para mostrar **todas as aplicações** sob o **todas as aplicações** lista. Para tal, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

6.  Clique a utilização de **filtro** controlo na parte superior a **lista de todas as aplicações**.

7.  No **filtro** painel, defina o **mostrar** opção para **todas as aplicações.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Por que motivo é apresentada uma aplicação específica na minha todas as lista de aplicações?

Quando filtrada para **todas as aplicações**, a **todas as aplicações** **lista** mostra todos os objetos de principais de serviço no seu inquilino. Objetos de principais de serviço podem aparecer nesta lista de uma várias formas:

1.  Quando adicionar qualquer aplicação a partir da Galeria de aplicações, incluindo:

   1. **Aplicações de galeria do Azure AD** – uma aplicação que foi previamente integrada para início de sessão com o Azure AD.

   2. **Aplicações de Proxy de aplicação** – uma aplicação em execução no seu ambiente no local que pretende fornecer seguro início de sessão único para externamente.

   3. **Desenvolvido personalizada aplicações** – uma aplicação que a sua organização pretende desenvolver na plataforma de desenvolvimento de aplicações do Azure do AD, mas que não pode ainda existe.

   4. **Aplicações não galeria** – Traga as suas próprias aplicações! Qualquer ligação web que pretende, qualquer aplicação que compõe um campo de nome de utilizador e palavra-passe, suporta os protocolos SAML ou o OpenID Connect ou suporta SCIM que pretende integrar para início de sessão com o Azure AD.

2.  Quando inscrever-se ou iniciar sessão como um 3<sup>rd</sup> aplicações de terceiros integrado com o Azure Active Directory. Um exemplo desta situação é [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Quando inscrever-se ou adicionar uma licença a um utilizador ou um grupo para uma primeira aplicação de terceiros, como [Microsoft Office 365](http://products.office.com/).

4.  Quando adiciona um novo registo de aplicações através da criação de uma aplicação desenvolvida personalizada a utilizar o [registo de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration).

5.  Quando adiciona um novo registo de aplicações através da criação de uma aplicação desenvolvida personalizada a utilizar o [Portal de registo de aplicação v 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal).

6.  Quando adicionar uma aplicação estiver a desenvolver com o Visual Studio [métodos de autenticação ASP.net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) ou [serviços ligados](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx).

7.  Quando cria um objeto principal de serviço utilizando o [módulo Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0).

8.  Quando lhe [consentimento para uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) como um administrador para utilizar dados no seu inquilino.

9.  Quando um [utilizador permitir a uma aplicação](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) utilizar dados no seu inquilino.

10. Quando ativar a determinados serviços que armazenam dados no seu inquilino. Um exemplo desta situação é repor a palavra-passe, que é modelada como política de reposição de um principal de serviço para armazenar a palavra-passe segura.

Para obter mais detalhes sobre como as aplicações são adicionadas ao seu diretório, leia [como e por que razão são adicionadas aplicações para o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Pretender remover um utilizador específico ou do grupo de atribuição para uma aplicação

Para remover um utilizador ou a atribuição de grupo para uma aplicação, siga os passos apresentados no [remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artigo.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quero desativar todos os acessos a uma aplicação para cada utilizador

Para desativar a todos os utilizadores inícios de sessão a uma aplicação, siga os passos apresentados no [desativar utilizador inícios de sessão para uma aplicação empresarial no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) artigo.

## <a name="i-want-to-delete-an-application-entirely"></a>Pretender eliminar uma aplicação totalmente

Para **eliminar uma aplicação**, siga as instruções abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende eliminar.

7.  Quando carrega a aplicação, clique em **eliminar** ícone da aplicação superior **descrição geral** painel.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quero desativar todas as operações de consentimento do utilizador futura para qualquer aplicação

Desativar o consentimento do utilizador para o seu diretório todo impedir que os utilizadores finais consenting para qualquer aplicação. Os administradores podem ainda consentimento no behalves do utilizador. Para saber mais sobre o consentimento de aplicação e, por isso poderá ou poderá pretender fazê-lo, ler [utilizador compreender e administração consentimento](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Para **desativar todas as operações de consentimento do utilizador futuras no seu diretório todo**, siga as instruções abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **as definições de utilizador**.

6.  Desative todas as operações de consentimento do utilizador futuras definindo a **utilizadores podem permitir que as aplicações aceder aos respetivos dados** alternar para **não** e clique em de **guardar** botão.

## <a name="next-steps"></a>Passos seguintes
[Gestão de aplicações com o Azure Active Directory](active-directory-enable-sso-scenario.md)

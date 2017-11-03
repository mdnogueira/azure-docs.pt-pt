---
title: "Como configurar a palavra-passe-início de sessão único para uma aplicação de galeria do Azure AD | Microsoft Docs"
description: "Como configurar uma aplicação para segura baseada em palavra-passe de início de sessão quando já está listado na Galeria de aplicações do Azure AD"
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
ms.openlocfilehash: d4dc110eb25c3e550ac4663d28e626a696b58f62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Como configurar a palavra-passe-início de sessão único para uma aplicação de galeria do Azure AD

Quando adiciona uma aplicação a [Galeria de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery), têm a opção de como pretende que os utilizadores para iniciar sessão para essa aplicação. Pode configurar esta opção em qualquer altura, selecionando o **Single Sign-on** numa aplicação empresarial no item de navegação do [Portal do Azure](https://portal.azure.com/).

Um único início de sessão métodos disponíveis e que é o [baseada em palavra-passe Single Sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) opção. Esta é uma excelente forma de começar a integrar aplicações com o Azure AD rapidamente e permite-lhe:

-   Ativar **-início de sessão único para os seus utilizadores** ao armazenar e replaying nomes de utilizador e palavras-passe para a aplicação de forma segura tiver integrado com o Azure AD

-   **Suporta aplicações que necessitam de vários campos de início de sessão** para aplicações que necessitam de mais do que apenas os campos nome de utilizador e palavra-passe para iniciar sessão

-   **Personalizar as etiquetas** de nome de utilizador e palavra-passe campos de entrada os seus utilizadores verão no [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando ele introduzir as respetivas credenciais

-   Permitir o **utilizadores** para fornecer os seus próprios nomes de utilizador e palavras-passe para as contas de aplicação existentes que são escrever manualmente no [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócio** para especificar os nomes de utilizador e palavras-passe atribuídas a um utilizador utilizando o [Self-Service de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funcionalidade

-   Permitir que um **administrador** para especificar os nomes de utilizador e palavras-passe atribuídas a um utilizador com as credenciais de atualização da funcionalidade quando [atribuir um utilizador a uma aplicação](#assign-a-user-to-an-application-directly)

-   Permitir que um **administrador** para especificar o nome de utilizador partilhado ou a palavra-passe utilizada por um grupo de pessoas com as credenciais de atualização da funcionalidade quando [atribuição de um grupo a uma aplicação](#assign-an-application-to-a-group-directly)

A seguir descreve como pode permitir [baseada em palavra-passe Single Sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) para uma aplicação que já se encontra no [Galeria de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

## <a name="overview-of-steps-required"></a>Descrição geral dos passos necessários
Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação na galeria do Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurar a aplicação para a palavra-passe-início de sessão único](#configure-the-application-for-password-single-sign-on)

-   [Atribuir a aplicação a um utilizador ou um grupo](#assign-the-application-to-a-user-or-a-group)

    -   [Atribuir diretamente um utilizador a uma aplicação](#assign-a-user-to-an-application-directly)

    -   [Atribuir diretamente uma aplicação a um grupo](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Adicionar uma aplicação na galeria do Azure AD

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel

6.  No **introduza um nome** caixa de texto do **adicionar da galeria do** secção, escreva o nome da aplicação

7.  Selecione a aplicação que pretende configurar para o início de sessão único

8.  Antes de adicionar a aplicação, pode alterar o nome do **nome** caixa de texto.

9.  Clique em **adicionar** botão, para adicionar a aplicação.

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe-início de sessão único

Para configurar o início de sessão para uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione o modo **baseada em palavra-passe de início de sessão.**

9.  [Atribuir utilizadores para a aplicação](#assign-a-user-to-an-application-directly).

10. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar no **credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome de utilizadores. Caso contrário, os utilizadores ser pedido que introduza as credenciais próprios após iniciar.

## <a name="assign-a-user-to-an-application-directly"></a>Atribuir diretamente um utilizador a uma aplicação

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de correio eletrónico** do utilizador que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **utilizador** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do utilizador ou logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais do que um utilizador**, tipo noutra **nome completo** ou **endereço de correio eletrónico** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação para os utilizadores selecionados.

## <a name="assign-an-application-to-a-group-directly"></a>Atribuir diretamente uma aplicação a um grupo

Para atribuir um ou mais grupos diretamente a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome do grupo completa** do grupo que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **grupo** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais de um grupo**, tipo noutra **nome do grupo completa** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar grupos, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação em grupos selecionados.

Após um curto período de tempo, os utilizadores que selecionou conseguir iniciar estas aplicações no painel de acesso.

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)

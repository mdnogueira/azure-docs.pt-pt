---
title: "Como configurar a palavra-passe-início de sessão único para um applicationn não Galeria | Microsoft Docs"
description: "Como configurar uma aplicação não Galeria personalizada para segura baseada em palavra-passe de início de sessão quando não estiver listado na Galeria de aplicações do Azure AD"
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
ms.openlocfilehash: f629ec99824199306ebf825901beaa99d83d434d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar a palavra-passe-início de sessão único para uma aplicação não Galeria

Para além das opções de encontrados Galeria de aplicações do Azure AD, também tem a opção para adicionar um **não Galeria aplicação** quando a aplicação que pretende não estiver indicada não existe. Através desta funcionalidade, pode adicionar qualquer aplicação que já existe na sua organização ou as aplicações de terceiros que poderá utilizar de um fornecedor que não está já fazem parte do [Galeria de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

Depois de adicionar uma aplicação não galeria, em seguida, pode configurar a método de início de início de sessão único utiliza esta aplicação, selecionando o **Single Sign-on** numa aplicação empresarial no item de navegação do [Portal do Azure](https://portal.azure.com/).

Um dos Single Sign-on métodos disponíveis e que é o [baseada em palavra-passe Single Sign-on](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) opção. Com o **adicionar uma aplicação não galeria** experiência, pode integrar qualquer aplicação que apresenta um nome de utilizador baseado em HTML e a palavra-passe de entrada no campo, mesmo que não está no nosso conjunto de aplicações previamente integradas.

É a forma como isto funciona por uma página scraping tecnologia que faz parte da extensão do painel de acesso que lhe permite-nos campos de entrada de nome de utilizador e palavra-passe de deteção automática, armazená-las de forma segura para a instância de aplicação específica. Em seguida, em segurança reprodução de nomes de utilizador e palavras-passe para esses campos quando um utilizador navega para essa aplicação no painel de acesso de aplicação.

Esta é uma excelente forma de começar a integrar qualquer tipo de aplicação com o Azure AD rapidamente e permite-lhe:

-   Integrar **qualquer aplicação do mundo** com o seu inquilino do Azure AD, por isso, desde compõe um campo de entrada HTML nome de utilizador e palavra-passe

-   Ativar **-início de sessão único para os seus utilizadores** ao armazenar e replaying nomes de utilizador e palavras-passe para a aplicação de forma segura tiver integrado com o Azure AD

-   **Entrada de deteção automática** campos para qualquer aplicação e permitem-lhe detetar manualmente esses campos através da extensão de Browser do painel de acesso, no caso de deteção automática não encontrá-los

-   **Suporta aplicações que necessitam de vários campos de início de sessão** para aplicações que necessitam de mais do que apenas os campos nome de utilizador e palavra-passe para iniciar sessão

-   **Personalizar as etiquetas** de nome de utilizador e palavra-passe campos de entrada os seus utilizadores verão no [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando ele introduzir as respetivas credenciais

-   Permitir o **utilizadores** para fornecer os seus próprios nomes de utilizador e palavras-passe para as contas de aplicação existentes que são escrever manualmente no [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócio** para especificar os nomes de utilizador e palavras-passe atribuídas a um utilizador utilizando o [Self-Service de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funcionalidade

-   Permitir que um **administrador** para especificar os nomes de utilizador e palavras-passe atribuídas a um utilizador com as credenciais de atualização da funcionalidade quando [atribuir um utilizador a uma aplicação](#_How_to_configure_1)

-   Permitir que um **administrador** para especificar o nome de utilizador partilhado ou a palavra-passe utilizada por um grupo de pessoas com as credenciais de atualização da funcionalidade quando [atribuição de um grupo a uma aplicação](#assign-an-application-to-a-group-directly)

A seguir descreve como pode permitir [baseada em palavra-passe de início de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) para qualquer aplicação que adicionar a utilizar o **adicionar uma aplicação não galeria** experiência.

## <a name="overview-of-steps-required"></a>Descrição geral dos passos necessários

Para configurar uma aplicação na galeria do Azure AD que tem de:

-   [Adicionar uma aplicação não Galeria](#add-a-non-gallery-application)

-   [Configurar a aplicação para a palavra-passe-início de sessão único](#configure-the-application-for-password-single-sign-on)

-   [Atribuir a aplicação a um utilizador ou um grupo](#assign-the-application-to-a-user-or-a-group)

    -   [Atribuir diretamente um utilizador a uma aplicação](#assign-a-user-to-an-application-directly)

    -   [Atribuir diretamente uma aplicação a um grupo](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação não Galeria

Para adicionar uma aplicação na galeria do Azure AD, siga os passos abaixo:

1.  Abra o [Portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em de **adicionar** botão no canto superior direito no **aplicações empresariais** painel

6.  Clique em **aplicação não galeria.**

7.  Introduza o nome da sua aplicação no **nome** caixa de texto. Selecione **adicionar.**

Após um curto período de tempo, poderá ver o painel de configuração da aplicação.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe-início de sessão único

Para configurar o início de sessão para uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione o modo **baseada em palavra-passe de início de sessão.**

9.  Introduza o **URL de início de sessão**. Este é o URL onde os utilizadores introduzem o respetivo nome de utilizador e palavra-passe para iniciar sessão no. Certifique-se de que os campos de início de sessão está visível no URL.

10. Atribua utilizadores para a aplicação.

11. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar no **credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome de utilizadores. Caso contrário, os utilizadores ser pedido que introduza as credenciais próprios após iniciar.

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

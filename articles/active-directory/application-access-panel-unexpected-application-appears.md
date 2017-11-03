---
title: "Como as aplicações são apresentadas no painel de acesso | Microsoft Docs"
description: "Resolver problemas com a razão pela qual uma aplicação é apresentados no painel de acesso"
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
ms.reviewr: japere
ms.openlocfilehash: f8ccf2cf66b49940bc7f2b9f4764020efc04838e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-applications-appear-on-the-access-panel"></a>Como as aplicações são apresentadas no painel de acesso

O painel de acesso é um portal baseado na web que permite que um utilizador com uma conta escolar ou profissional no Azure Active Directory (Azure AD) para ver e iniciar as aplicações baseadas na nuvem que o administrador do Azure AD concedeu-lhes acesso. Estas aplicações estão configuradas em nome do utilizador no portal do Azure AD. O administrador pode aprovisionar diretamente a aplicação para o utilizador ou a um grupo de um utilizador faz parte de resultando na aplicação volte a aparecer no painel de acesso do utilizador.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Se uma aplicação apenas foi removida de um utilizador ou grupo, que o utilizador é membro de, tente voltar a iniciar e terminar no painel de acesso do utilizador após alguns minutos para ver se a aplicação é removida.

-   Se uma licença apenas tiver sido removida de um utilizador ou grupo, o utilizador é que membro deste processo poderá demorar muito tempo, dependendo do tamanho e complexidade do grupo para que as alterações para ser efetuada. Permitir tempo adicional antes de iniciar sessão do painel de acesso.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemas relacionados com a atribuição de aplicações para utilizadores

Um utilizador pode estar a ver uma aplicação no respetivo painel de acesso porque tinha sido anteriormente atribuídos ao mesmo. Seguem-se algumas formas de verificação:

-   [Certifique-se um utilizador atribuído à aplicação](#check-if-a-user-is-assigned-to-the-application)

-   [Verifique se um utilizador com uma licença relacionadas com a aplicação](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Certifique-se um utilizador atribuído à aplicação

Para verificar se um utilizador está atribuído à aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

6.  **Pesquisa** para o nome da aplicação em questão.

7.  Clique em **utilizadores e grupos**.

8.  Verifique se o utilizador está atribuído à aplicação.

  * Se pretender remover o utilizador da aplicação, **clique na linha** do utilizador e selecione **eliminar**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Verifique se um utilizador com uma licença relacionadas com a aplicação

Para verificar as licenças atribuídas de um utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o utilizador tem atribuída.

   * Se o utilizador está atribuído a um Office licenças este ativar primeiro terceiros as aplicações do Office a aparecer no painel de acesso do utilizador.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemas relacionados com a atribuição de aplicações para grupos

Um utilizador pode estar a ver uma aplicação no respetivo painel de acesso porque fazem parte de um grupo que tenha sido atribuído a aplicação. Seguem-se algumas formas de verificação:

-   [Verifique as associações de grupo do utilizador](#check-a-users-group-memberships)

-   [Verifique se um utilizador for um membro de um grupo atribuído a uma licença](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Verifique as associações de grupo do utilizador

Para verificar a associação a um grupo, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **grupos.**

8.  Verifique se o utilizador faz parte de um grupo atribuído à aplicação.

   * Se pretender remover o utilizador do grupo, **clique na linha** de eliminar o grupo e selecione.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Verifique se um utilizador for um membro de um grupo atribuído a uma licença

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **grupos.**

8.  Clique na linha de um grupo específico.

9.  Clique em **licenças** ver as licenças que o grupo foi atribuída.

  * Se o grupo for atribuído a uma licença do Office que isto poderá ativar determinadas aplicações do Office de terceiros primeiro a aparecer no painel de acesso do utilizador.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Se efetue estes passos de resolução de problemas não a resolva o problema

Abra um pedido de suporte com as seguintes informações se disponíveis:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   ID do inquilino

-   Tipo de browser

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios de fiddler

## <a name="next-steps"></a>Passos seguintes
[Gestão de aplicações com o Azure Active Directory](active-directory-enable-sso-scenario.md)

---
title: "Publicar aplicações com o Proxy da Aplicação do Azure AD | Microsoft Docs"
description: "Publique aplicações no local na cloud com o Proxy de Aplicações do Azure AD no portal clássico."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 96490c0d060fe5486a7235a5aa76380c8d9b5d4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicar aplicações com o Proxy da Aplicação do Azure AD

> [!div class="op_single_selector"]
> * [Portal do Azure](application-proxy-publish-azure-portal.md)
> * [Portal Clássico do Azure](active-directory-application-proxy-publish.md)

A Proxy de Aplicação do Azure AD ajuda-o a fornecer suporte a funcionários remotos através da publicação de aplicações no local para que sejam acedidas através da Internet. Nesta altura, já deve ter [ativado a Proxy de Aplicação no portal clássico do Azure](active-directory-application-proxy-enable.md). Este artigo explica os passos para publicar aplicações que estão a ser executadas na sua rede local e fornecem acesso remoto seguro fora da rede. Depois de concluir este artigo, estará pronto para configurar a aplicação com informação personalizada ou requisitos de segurança.

> [!NOTE]
> O Proxy da Aplicação é uma funcionalidade que só estar disponível se tiver efetuado a atualização para a edição Premium ou Basic do Azure Active Directory. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md). Se pretender utilizar a pré-visualização pública do Proxy de Aplicações, pode [Publicar aplicações no portal do Azure](application-proxy-publish-azure-portal.md).

## <a name="publish-an-app-using-the-wizard"></a>Publicar uma aplicação com o assistente
1. Inicie sessão como administrador no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Aceda ao Active Directory e selecione o diretório onde ativou o Proxy da Aplicação.
   
    ![Active Directory – ícone](./media/active-directory-application-proxy-publish/ad_icon.png)
3. Clique no separador **Aplicações** e, em seguida, clique no botão **Adicionar** na parte inferior do ecrã
   
    ![Adicionar aplicação](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
4. Selecione **Publicar uma aplicação acessível fora da rede**.
   
    ![Publicar uma aplicação acessível fora da rede](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)
5. Forneça as seguintes informações sobre a aplicação:
   
   * **Nome**: o nome amigável de utilizador para a aplicação. Tem de ser exclusivo no diretório.
   * **URL interno**: o endereço que o Conector do Proxy da Aplicação utiliza para aceder à aplicação do interior da rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Desta forma, pode publicar sites diferentes no mesmo servidor e atribuir a cada um nome e regras de acesso próprios.
     
     > [!TIP]
     > Se publicar um caminho, certifique-se de que inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Por exemplo, se a aplicação estiver em https://yourapp/app e utiliza as imagens localizadas em https://yourapp/media, deve publicar https://yourapp/ como caminho.
     > 
     > 
   * **Método de Pré-autenticação**: como a Proxy da Aplicação verifica os utilizadores antes de conceder acesso à aplicação. Escolha uma das opções no menu pendente.
     
     * Azure Active Directory: a Proxy da Aplicação redireciona os utilizadores para iniciarem sessão no Azure AD, que autentica as respetivas permissões para o diretório e aplicação.
     * Passth-rough: os utilizadores não têm de autenticar-se para aceder à aplicação.
     
     ![Propriedades da aplicação](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  
6. Para concluir o assistente, clique na marca de verificação na parte inferior do ecrã. A aplicação está agora definida no Azure AD.

## <a name="assign-users-and-groups-to-the-application"></a>Atribuir utilizadores e grupos à aplicação
Para que os utilizadores possam aceder à aplicação que publicou, terá de os atribuir individualmente ou em grupos. (Lembre-se de atribuir também acesso a si.) Cada utilizador que atribuir precisa de uma licença para o Azure Básico ou superior. Pode atribuir licenças individualmente ou a grupos. Para obter mais informações, veja [Assigning users to an application](active-directory-applications-guiding-developers-assigning-users.md) (Atribuir utilizadores a uma aplicação). 

Para aplicações que precisam de pré-autenticação, atribuir um utilizador garante permissão para utilizar a aplicação. Para aplicações que não precisam de pré-autenticação, atribuir um utilizador significa que o utilizador pode aceder à aplicação através do painel de acesso.

1. Depois de concluir o assistente Adicionar Aplicação, será apresentada a página Início Rápido da aplicação. Para gerir quem tem acesso à aplicação, selecione **Utilizadores e grupos**.
   
    ![Início rápido do Proxy da Aplicação para atribuir utilizadores – captura de ecrã](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)
2. Procure grupos específicos no diretório ou veja todos os utilizadores. Para visualizar os resultados de pesquisa, clique na marca de verificação.
   
      ![Procurar grupos ou utilizadores - captura de ecrã](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)
3. Selecione cada utilizador ou grupo que pretende atribuir a esta aplicação e clique em **Atribuir**. Ser-lhe-á pedido para confirmar esta ação.

> [!NOTE]
> Para aplicações de Autenticação Integrada do Windows, apenas pode atribuir utilizadores e grupos que são sincronizados a partir do Active Directory no local. As aplicações publicadas com o Proxy da Aplicação do Azure Active Directory não podem ser atribuídas a utilizadores que iniciam sessão com uma conta Microsoft ou a convidados. Certifique-se de que os utilizadores iniciam sessão com as credenciais que fazem parte do mesmo domínio que a aplicação que está a publicar.
> 
> 

## <a name="test-your-published-application"></a>Testar a aplicação publicada
Assim que tiver publicado a aplicação, pode testá-la navegando para o URL que publicou. Verifique se consegue aceder à aplicação, se compõe corretamente e se tudo funciona conforme esperado. Se tiver dificuldade ou aparecer uma mensagem de erro, experimente o [guia de resolução de problemas](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Configurar a aplicação
Pode modificar as aplicações publicadas ou configurar opções avançadas na página Configurar. Nesta página, pode personalizar a aplicação ao alterar o nome ou ao carregar um logótipo. Pode ainda gerir as regras de acesso, como o método de pré-autenticação ou a autenticação multifator.

![Configuração avançada](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)

Depois de publicar as aplicações com o Proxy da Aplicação do Azure Active Directory, estas aparecem na lista de Aplicações do Azure AD, onde as pode gerir.

Se desativar os serviços do Proxy da Aplicação depois de ter publicado aplicações, as aplicações não serão acessíveis fora da rede privada. Os utilizadores ainda podem aceder a aplicações no local como habitualmente.

Para ver uma aplicação e certificar-se de que é acessível, faça duplo clique no nome da aplicação. Se o serviço do Proxy da Aplicação estiver desativado e a aplicação não estiver disponível, será exibida uma mensagem de aviso na parte superior do ecrã.

Para eliminar uma aplicação, selecione-a na lista e clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes
* [Publicar aplicações com o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
* [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
* [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
* [Trabalhar com aplicações com suporte para afirmações](active-directory-application-proxy-claims-aware-apps.md)

Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](http://blogs.technet.com/b/applicationproxyblog/)


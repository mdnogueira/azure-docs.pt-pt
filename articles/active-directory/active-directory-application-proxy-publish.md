<properties
    pageTitle="Publicar aplicações com o Proxy da Aplicação do Azure AD | Microsoft Azure"
    description="Publicar aplicações no local para a nuvem com o Proxy da Aplicação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# Publicar aplicações com o Proxy da Aplicação do Azure AD

A Proxy de Aplicação do Azure AD ajuda-o a fornecer suporte a funcionários remotos através da publicação de aplicações no local para que sejam acedidas através da Internet. Nesta altura, já deve ter [ativado a Proxy de Aplicação no portal clássico do Azure](active-directory-application-proxy-enable.md). Este artigo explica os passos para publicar aplicações que estão a ser executadas na sua rede local e fornecem acesso remoto seguro fora da rede. Depois de concluir este artigo, estará pronto para configurar a aplicação com informação personalizada ou requisitos de segurança.

> [AZURE.NOTE] O Proxy da Aplicação é uma funcionalidade que só estar disponível se tiver efetuado a atualização para a edição Premium ou Basic do Azure Active Directory. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md).

## Publicar uma aplicação com o assistente

1. Inicie sessão como administrador no [Portal Clássico do Azure](https://manage.windowsazure.com/).
2. Aceda ao Active Directory e selecione o diretório onde ativou o Proxy da Aplicação.

    ![Active Directory – ícone](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Clique no separador **Aplicações** e, em seguida, clique no botão **Adicionar** na parte inferior do ecrã

    ![Adicionar aplicação](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Selecione **Publicar uma aplicação acessível fora da rede**.

    ![Publicar uma aplicação acessível fora da rede](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Forneça as seguintes informações sobre a aplicação:

    - **Nome**: o nome amigável de utilizador para a aplicação. Tem de ser exclusivo no diretório.
    - **URL interno**: o endereço que o Conector do Proxy da Aplicação utiliza para aceder à aplicação do interior da rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Desta forma, pode publicar sites diferentes no mesmo servidor e atribuir a cada um nome e regras de acesso próprios.

        > [AZURE.TIP] Se publicar um caminho, certifique-se de que inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Por exemplo, se a aplicação estiver em https://yourapp/app e utiliza as imagens localizadas em https://yourapp/media, deve publicar https://yourapp/ como caminho.

    - **Método de Pré-autenticação**: como a Proxy da Aplicação verifica os utilizadores antes de conceder acesso à aplicação. Escolha uma das opções no menu pendente.

        - Azure Active Directory: a Proxy da Aplicação redireciona os utilizadores para iniciarem sessão no Azure AD, que autentica as respetivas permissões para o diretório e aplicação.
        - Passth-rough: os utilizadores não têm de autenticar-se para aceder à aplicação.

    ![Propriedades da aplicação](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Para concluir o assistente, clique na marca de verificação na parte inferior do ecrã. A aplicação está agora definida no Azure AD.


## Atribuir utilizadores e grupos à aplicação

Para que os utilizadores possam aceder à aplicação que publicou, terá de os atribuir individualmente ou em grupos. (Lembre-se de atribuir também acesso a si.) Isto requer que cada utilizador tenha uma licença para o Azure básico ou superior. Pode atribuir licenças individualmente ou a grupos. Veja o artigo [Atribuir utilizadores a uma aplicação](active-directory-applications-guiding-developers-assigning-users.md) para obter mais detalhes. 

Para aplicações que requerem pré-autenticação, esta ação garante permissões para utilizar a aplicação. Para aplicações que não necessitam de pré-autenticação, os utilizadores podem continuar a ser atribuídos à aplicação para que esta seja apresentada na lista de aplicações, como MyApps.

1. Depois de concluir o assistente Adicionar Aplicação, será apresentada a página Início Rápido da aplicação. Para gerir quem tem acesso à aplicação, selecione **Utilizadores e grupos**.

    ![Início rápido do Proxy da Aplicação para atribuir utilizadores – captura de ecrã](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Procure grupos específicos no diretório ou veja todos os utilizadores. Para visualizar os resultados de pesquisa, clique na marca de verificação.

    ![Procurar grupos ou utilizadores - captura de ecrã](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Selecione cada utilizador ou grupo que pretende atribuir a esta aplicação e clique em **Atribuir**. Ser-lhe-á pedido para confirmar esta ação.

> [AZURE.NOTE] Para aplicações de Autenticação Integrada do Windows, apenas pode atribuir utilizadores e grupos que são sincronizados a partir do Active Directory no local. As aplicações publicadas com o Proxy da Aplicação do Azure Active Directory não podem ser atribuídas a utilizadores que iniciam sessão com uma conta Microsoft ou a convidados. Certifique-se de que os utilizadores iniciam sessão com as credenciais que fazem parte do mesmo domínio que a aplicação que está a publicar.

## Testar a aplicação publicada

Assim que tiver publicado a aplicação, pode testá-la navegando para o URL que publicou. Certifique-se de que consegue aceder à aplicação, que compõe corretamente e que tudo funciona conforme esperado. Se tiver dificuldade ou aparecer uma mensagem de erro, experimente o [guia de resolução de problemas](active-directory-application-proxy-troubleshoot.md).

## Configurar a aplicação

Pode modificar as aplicações publicadas ou configurar opções avançadas na página Configurar. Nesta página, pode personalizar a aplicação ao alterar o nome ou ao carregar um logótipo. Pode ainda gerir as regras de acesso, como o método de pré-autenticação ou a autenticação multifator.

![Configuração avançada](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Depois de publicar as aplicações com o Proxy da Aplicação do Azure Active Directory, estas aparecem na lista de Aplicações do Azure AD, onde as pode gerir.

Se desativar os serviços da Proxy da Aplicação depois de ter publicado aplicações, estas não serão acessíveis fora da rede privada. Isto não elimina as aplicações.

Para ver uma aplicação e certificar-se de que é acessível, faça duplo clique no nome da aplicação. Se o serviço do Proxy da Aplicação estiver desativado e a aplicação não estiver disponível, será exibida uma mensagem de aviso na parte superior do ecrã.

Para eliminar uma aplicação, selecione-a na lista e clique em **Eliminar**.

## Passos seguintes

- [Publicar aplicações com o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalhar com aplicações com suporte para afirmações](active-directory-application-proxy-claims-aware-apps.md)

Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](http://blogs.technet.com/b/applicationproxyblog/)



<!--HONumber=Aug16_HO1-->



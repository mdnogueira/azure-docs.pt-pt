<properties
    pageTitle="Publicar aplicações com o Proxy da Aplicação do Azure AD | Microsoft Azure"
    description="Publicar aplicações no local para a nuvem com o Proxy da Aplicação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="kgremban"/>


# Publicar aplicações com o Proxy da Aplicação do Azure AD


Depois de ativar o Proxy da Aplicação do Microsoft Azure Active Directory (AD), pode publicar aplicações no local de modo a que os utilizadores remotos possam aceder às referidas aplicações fora da rede privada.

Este artigo explica os passos para publicar aplicações que estão a ser executadas na sua rede local e fornecem acesso remoto seguro fora da rede. Se ainda não tiver configurado o Proxy da Aplicação ou instalado quaisquer Conectores, siga os passos em [Ativar o Proxy da Aplicação no Portal do Azure](active-directory-application-proxy-enable.md) antes de continuar.

Se utilizar o Proxy da Aplicação do Azure AD pela primeira vez, recomendamos que teste o Conector publicando um site da sua rede privada antes de publicar aplicações.

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
    - **URL interno**: o endereço que o Conector do Proxy da Aplicação utiliza para aceder à aplicação do interior da rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Desta forma, pode publicar diferentes sites no mesmo servidor e atribuir a cada um o seu próprio nome e regras de acesso.
    - **Método de pré-autenticação**: a forma como o Proxy da Aplicação verifica os utilizadores antes de conceder acesso à aplicação. Escolha uma das opções no menu pendente.

        - Azure Active Directory: o Proxy da Aplicação redireciona os utilizadores para iniciarem sessão no Azure AD, que autenticam as respetivas permissões para o diretório e a aplicação.
        - Passth-rough: os utilizadores não têm de autenticar-se para aceder à aplicação.

    ![Propriedades da aplicação](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Para concluir o assistente, clique na marca de verificação na parte inferior do ecrã. A aplicação está agora definida no Azure AD.


## Atribuir utilizadores e grupos à aplicação

Para que os utilizadores possam aceder à aplicação que publicou, terá de os atribuir individualmente ou em grupos. Para aplicações que requerem pré-autenticação, esta ação garante permissões para utilizar a aplicação. Para aplicações que não necessitam de pré-autenticação, os utilizadores não precisam de permissões. No entanto, terão de ser atribuídos à aplicação para que esta seja apresentada na lista de aplicações.

1. Depois de concluir o assistente Adicionar Aplicação, será apresentada a página Início Rápido da aplicação. Para gerir quem tem acesso à aplicação, selecione **Utilizadores e grupos**.

    ![Início rápido do Proxy da Aplicação para atribuir utilizadores – captura de ecrã](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Procure grupos específicos no diretório ou veja todos os utilizadores. Clique na marca de verificação para apresentar os resultados.

    ![Procurar grupos ou utilizadores - captura de ecrã](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Selecione cada utilizador ou grupo que pretende atribuir a esta aplicação e clique em **Atribuir**. Ser-lhe-á pedido para confirmar esta ação.

> [AZURE.NOTE] Para aplicações de Autenticação Integrada do Windows, apenas pode atribuir utilizadores e grupos que são sincronizados a partir do Active Directory no local. As aplicações publicadas com o Proxy da Aplicação do Azure Active Directory não podem ser atribuídas a utilizadores que iniciam sessão com uma conta Microsoft ou a convidados. Certifique-se de que os utilizadores iniciam sessão com as credenciais que fazem parte do mesmo domínio que a aplicação que está a publicar.


## Configuração avançada

Pode modificar as aplicações publicadas ou configurar opções avançadas na página Configurar. Nesta página, pode personalizar a aplicação ao alterar o nome ou ao carregar um logótipo. Pode ainda gerir as regras de acesso, como o método de pré-autenticação ou a autenticação multifator.

![Configuração avançada](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Depois de publicar as aplicações com o Proxy da Aplicação do Azure Active Directory, estas aparecem na lista de Aplicações do Azure AD, onde as pode gerir.

Se desativar os serviços do Proxy da Aplicação depois de ter publicado aplicações, estas não serão eliminadas mas deixarão de ser acessíveis fora da rede privada.

Para ver uma aplicação e certificar-se de que é acessível, faça duplo clique no nome da aplicação. Se o serviço do Proxy da Aplicação estiver desativado e a aplicação não estiver disponível, será exibida uma mensagem de aviso na parte superior do ecrã.

Para eliminar uma aplicação, selecione-a na lista e clique em **Eliminar**.

## Passos seguintes

- [Publicar aplicações com o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalhar com aplicações com suporte para afirmações](active-directory-application-proxy-claims-aware-apps.md)

Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](http://blogs.technet.com/b/applicationproxyblog/)



<!--HONumber=Jun16_HO2-->



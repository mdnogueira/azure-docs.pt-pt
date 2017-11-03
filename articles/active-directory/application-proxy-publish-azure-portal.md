---
title: "Publicar aplicações com o Proxy da Aplicação do Azure AD | Microsoft Docs"
description: "Publica aplicações no local para a nuvem com o Proxy de aplicações do Azure AD no portal do Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e00a939f2b20ab8e0a2ddf0ff91e59db440213ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicar aplicações com o Proxy da Aplicação do Azure AD

> [!div class="op_single_selector"]
> * [Portal do Azure](application-proxy-publish-azure-portal.md)
> * [Portal Clássico do Azure](active-directory-application-proxy-publish.md)

Proxy de aplicações do Azure Active Directory (AD) ajuda-o suporte a funcionários remotos através da publicação de aplicações no local para ser acedido através da internet. Pode publicar estas aplicações através do portal do Azure para fornecer acesso remoto seguro fora da rede.

Este artigo explica os passos para publicar uma aplicação no local com o Proxy da aplicação. Depois de concluir este artigo, os seus utilizadores irão conseguir aceder remotamente a sua aplicação. E estará pronto para configurar funcionalidades adicionais para a aplicação, como o início de sessão único, informação personalizada e requisitos de segurança.

Se estiver familiarizado com o Proxy de aplicações, saiba mais sobre esta funcionalidade com o artigo [como fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Publicar aplicações no local para acesso remoto

Siga estes passos para publicar as suas aplicações com o Proxy de aplicações. Se ainda não já transferiu e configurado um conector para a sua organização, aceda a [começar com o Proxy de aplicações e instalar o conector](active-directory-application-proxy-enable.md) primeiro e, em seguida, publicar a aplicação.

> [!TIP]
> Se estiver a testar o Proxy de aplicações pela primeira vez, escolha uma aplicação que está configurada para autenticação baseada em palavra-passe. Proxy de aplicações suporta outros tipos de autenticação, mas a aplicações baseadas em palavra-passe são o facilitar a obter cópias de segurança e em execução rapidamente. 

1. Inicie sessão como administrador no [portal do Azure](https://portal.azure.com/).
2. Selecione **do Azure Active Directory** > **aplicações empresariais** > **nova aplicação**.

  ![Adicionar uma aplicação empresarial](./media/application-proxy-publish-azure-portal/add-app.png)

3. Selecione **todos os**, em seguida, selecione **no local aplicação**.  

  ![Adicionar a sua própria aplicação](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Forneça as seguintes informações sobre a aplicação:

   - **Nome**: O nome da aplicação que será apresentado no painel de acesso e no portal do Azure. 

   - **URL interno**: O URL que utilizar para aceder à aplicação a partir de dentro da sua rede privada. Pode fornecer um caminho específico no servidor de back-end para publicação, enquanto o resto do servidor não é publicado. Desta forma, pode publicar sites diferentes no mesmo servidor que diferentes aplicações e fornecer as suas próprias regras de acesso e o nome de cada um deles.

     > [!TIP]
     > Se publicar um caminho, certifique-se de que inclui todas as imagens, scripts e folhas de estilo necessários para a sua aplicação. Por exemplo, se a aplicação estiver em https://yourapp/app e utiliza as imagens localizadas em https://yourapp/media, deve publicar https://yourapp/ como caminho. Este URL interno não tem de ser os seus utilizadores verão a página de destino. Para obter mais informações, consulte [definir uma página inicial personalizada para as aplicações publicadas](application-proxy-office365-app-launcher.md).

   - **URL externo**: O endereço que os utilizadores que serão enviadas para poder aceder a aplicação a partir de fora da rede. Se não pretender utilizar o domínio de Proxy de aplicações predefinido, leia sobre [domínios personalizados no Proxy de aplicações do Azure AD](active-directory-application-proxy-custom-domains.md).
   - **Pré-autenticação**: como Proxy da aplicação verifica os utilizadores antes de conceder acesso à sua aplicação. 

     - Azure Active Directory: a Proxy da Aplicação redireciona os utilizadores para iniciarem sessão no Azure AD, que autentica as respetivas permissões para o diretório e aplicação. É recomendável manter esta opção como predefinição, para que pode tirar partido das funcionalidades de segurança do Azure AD, como acesso condicional e multi-factor Authentication.
     - Passth-Rough: Os utilizadores não têm de autenticar face a Azure Active Directory para aceder à aplicação. Pode ainda definir os requisitos de autenticação no back-end.
   - **Grupo de conector**: conectores processam o acesso remoto à sua aplicação e ajuda a organizar os conectores e as aplicações por região, a rede ou o objetivo de grupos de conector. Se não tiver quaisquer grupos de conector ainda foi criados, a aplicação é atribuída a **predefinido**.

   ![Configurar a aplicação](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Se necessário, configure definições adicionais. Para a maioria das aplicações, deve manter estas definições nos respetivos Estados de predefinição. 
   - **Tempo limite de aplicação de back-end**: defina este valor como **longo** apenas se a aplicação está lenta autenticar e ligar. 
   - **Traduzir os URLs nos cabeçalhos**: manter este valor como **Sim** , a menos que a aplicação necessária o cabeçalho de anfitrião original no pedido de autenticação.
   - **Tradução de URLs no corpo de aplicação**: manter este valor como **não** a menos que têm ligações de codificado HTML para outras aplicações no local e não utilize domínios personalizados. Para obter mais informações, consulte [tradução com o Proxy da aplicação de ligação](application-proxy-link-translation.md).
   
   ![Configurar a aplicação](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Selecione **Adicionar**.


## <a name="add-a-test-user"></a>Adicionar um utilizador de teste 

Para testar se a aplicação foi publicada corretamente, adicione uma conta de utilizador de teste. Certifique-se de que esta conta já tem permissões para aceder a aplicação a partir de dentro da rede empresarial.

1. Novamente no painel de início rápido, selecione **atribuir um utilizador de teste**.

  ![Atribuir um utilizador de teste](./media/application-proxy-publish-azure-portal/assign-user.png)

2. No painel de grupos e utilizadores, selecione **adicionar**.

  ![Adicionar um utilizador ou grupo](./media/application-proxy-publish-azure-portal/add-user.png)

3. No painel de atribuição de adicionar, selecione **utilizadores e grupos** , em seguida, escolha a conta que pretende adicionar. 
4. Selecione **atribuir**.

## <a name="test-your-published-app"></a>Testar a aplicação publicada

No seu browser, navegue para o URL externo que configurou durante o passo de publicar. Deve ver o ecrã de início e de conseguir iniciar sessão com a conta de teste que configurou.

![Testar a aplicação publicada](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Passos seguintes
- [Transferir conectores](active-directory-application-proxy-enable.md) e [criar grupos de conector](active-directory-application-proxy-connectors-azure-portal.md) a publicação de aplicações em redes separadas e localizações.

- [Configurar o início de sessão único](application-proxy-sso-azure-portal.md) para a sua aplicação recentemente publicada

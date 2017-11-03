---
title: Autorizar a contas de programador, utilizando o Azure Active Directory B2C - API Management do Azure | Microsoft Docs
description: Saiba como autorizar os utilizadores ao utilizar o Azure Active Directory B2C na API Management.
services: api-management
documentationcenter: API Management
author: vladvino
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: d99dbbd834cb8f067b88b765ccddcd7f4eb44a1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
> [!WARNING]
> Integração do Active Directory B2C do Azure está disponível no [programador e Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/) camadas apenas.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Como autorizar contas de programador na API Management do Azure utilizando o Azure Active Directory B2C
## <a name="overview"></a>Descrição geral
O Azure Active Directory B2C é uma solução de gestão de identidades de nuvem para aplicações móveis e web direcionadas para o consumidor. Pode utilizá-lo para gerir o acesso ao portal do programador. Este guia mostra a configuração que é necessária no seu serviço de API de gestão para integrar com o Azure Active Directory B2C. Para obter informações sobre como ativar o acesso ao portal do programador com clássico do Azure Active Directory, consulte [como autorizar contas de programador com o Azure Active Directory].

> [!NOTE]
> Para concluir os passos neste guia, primeiro tem de ter um inquilino do Azure Active Directory B2C para criar uma aplicação no. Além disso, tem de ter políticas de inscrição e de início de sessão prontas. Para obter mais informações, consulte [descrição geral do Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizar a contas de programador, utilizando o Azure Active Directory B2C

1. Para começar, clique em **portal do publicador** no portal do Azure para o seu serviço de API Management. Isto leva-o para o portal do publicador da API Management.

   ![Portal do publicador][api-management-management-console]

   > [!NOTE]
   > Se ainda não criou uma instância de serviço de API Management, consulte o artigo [criar uma instância de serviço de API Management] [ Create an API Management service instance] no [obter uma introdução tutorial de API Management do Azure] [Get started with Azure API Management].

2. No **API Management** menu, clique em **segurança**. No **identidades** separador, escolha **Azure Active Directory B2C**.

  ![Identidades externas 1][api-management-howto-aad-b2c-security-tab]

3. Anote o **URL de redirecionamento** e mude ao longo para o Azure Active Directory B2C no portal do Azure.

  ![Identidades externas 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. Clique em de **aplicações** botão.

  ![Registar uma nova aplicação 1][api-management-howto-aad-b2c-portal-menu]

5. Clique em de **adicionar** botão para criar uma nova aplicação do Azure Active Directory B2C.

  ![Registar uma nova aplicação 2][api-management-howto-aad-b2c-add-button]

6. No **nova aplicação** painel, introduza um nome para a aplicação. Escolha **Sim** em **Web App/Web API**e escolha **Sim** em **permitir fluxo implícito**. Em seguida, copie o **URL de redirecionamento** do **Azure Active Directory B2C** secção do **identidades** separador no portal do publicador e colar no **resposta URL** caixa de texto.

  ![Registar uma nova aplicação de 3][api-management-howto-aad-b2c-app-details]

7. Clique no botão **Criar**. Quando a aplicação é criada, é apresentado no **aplicações** painel. Clique no nome de aplicação para ver os respetivos detalhes.

  ![Registar uma nova aplicação 4][api-management-howto-aad-b2c-app-created]

8. Do **propriedades** painel, copie o **ID da aplicação** para a área de transferência.

  ![ID da aplicação 1][api-management-howto-aad-b2c-app-id]

9. Regresse ao portal do publicador e cole o ID para o **Id de cliente** caixa de texto.

  ![ID da aplicação 2][api-management-howto-aad-b2c-client-id]

10. Regresse ao portal do Azure, clique em de **chaves** botão e, em seguida, clique em **gerar chave**. Clique em **guardar** para guardar a configuração e apresentar o **chave da aplicação**. Copie a chave para a área de transferência.

  ![Chave da aplicação 1][api-management-howto-aad-b2c-app-key]

11. Regresse ao portal do publicador e cole a chave para o **segredo do cliente** caixa de texto.

  ![Chave da aplicação 2][api-management-howto-aad-b2c-client-secret]

12. Especifique o nome de domínio do inquilino do Azure Active Directory B2C no **permitido inquilino**.

  ![Inquilino permitido][api-management-howto-aad-b2c-allowed-tenant]

13. Especifique o **política de inscrição** e **política de início de sessão**. Opcionalmente, também pode fornecer o **política de perfil de edição** e **política de reposição de palavra-passe**.

  ![Políticas][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Para obter mais informações sobre políticas, consulte [Azure Active Directory B2C: estrutura de política extensível].

14. Depois de ter especificado a configuração pretendida, clique em **guardar**.

  Depois das alterações são guardadas, os programadores será capazes de criar novas contas e inicie sessão no portal do programador, utilizando o Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Inscreva-se uma conta de programador, utilizando o Azure Active Directory B2C

1. Para se inscrever para uma conta de programador, utilizando o Azure Active Directory B2C, abra uma nova janela do browser e aceda ao portal do programador. Clique em de **inscrever** botão.

   ![Portal do programador 1][api-management-howto-aad-b2c-dev-portal]

2. Optar por inscrever-se com **Azure Active Directory B2C**.

   ![Portal do programador 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Está a redirecionado para a política de inscrição que configurou na secção anterior. Optar por inscrever-se utilizando o seu endereço de e-mail ou uma das suas contas de redes sociais existentes.

   > [!NOTE]
   > Se o Azure Active Directory B2C é a única opção ativada no **identidades** separador no portal do publicador, irá ser redirecionado para a política de inscrição diretamente.

   ![Portal do programador][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Quando a inscrição estiver concluída, está redirecionado novamente para o portal do programador. Agora tem sessão iniciada portal do programador para a instância de serviço de API Management.

    ![Registo completo][api-management-registration-complete]

## <a name="next-steps"></a>Passos seguintes

*  [descrição geral do Azure Active Directory B2C]
*  [Azure Active Directory B2C: estrutura de política extensível]
*  [Utilizar uma conta Microsoft como um fornecedor de identidade no Azure Active Directory B2C]
*  [Utilizar uma conta do Google como um fornecedor de identidade no Azure Active Directory B2C]
*  [Utilizar uma conta de LinkedIn como um fornecedor de identidade no Azure Active Directory B2C]
*  [Utilizar uma conta do Facebook como um fornecedor de identidade no Azure Active Directory B2C]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[descrição geral do Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[como autorizar contas de programador com o Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: estrutura de política extensível]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Utilizar uma conta Microsoft como um fornecedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Utilizar uma conta do Google como um fornecedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Utilizar uma conta do Facebook como um fornecedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Utilizar uma conta de LinkedIn como um fornecedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

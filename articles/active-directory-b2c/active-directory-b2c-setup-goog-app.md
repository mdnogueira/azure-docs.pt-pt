---
title: "O Azure Active Directory B2C: Google + configuração | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para consumidores com contas Google + nas aplicações que estejam protegidas pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 4dcca66f-29e4-4b4d-8840-50baad736bd7
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 6ab73e5c79742ab548733f5712dee1e28461db9f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com o Google + contas
## <a name="create-a-google-application"></a>Criar uma aplicação do Google +
Para utilizar o Google + como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação do Google + e forneça-lo com os parâmetros corretos. Necessita de uma conta do Google + para efetuar este procedimento. Se não tiver uma, pode obtê-lo em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Vá para o [consola de programadores da Google](https://console.developers.google.com/) e inicie sessão com as credenciais da conta Google +.
2. Clique em **criar projeto**, introduza um **nome do projeto**e, em seguida, clique em **criar**.
   
    ![Google + - introdução](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google + - novo projeto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Clique em **Gestor de API** e, em seguida, clique em **credenciais** no painel de navegação esquerdo.
4. Clique em de **ecrã de consentimento do OAuth** separador no topo.
   
    ![Google + - as credenciais](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Selecione ou especifique uma válida **endereço de correio eletrónico**, forneça um **nome de produto**e clique em **guardar**.
   
    ![Google + - ecrã de consentimento do OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Clique em **novas credenciais** e, em seguida, escolha **ID de cliente OAuth**.
   
    ![Google + - ecrã de consentimento do OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. Em **tipo de aplicação**, selecione **aplicação Web**.
   
    ![Google + - ecrã de consentimento do OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Forneça um **nome** para a sua aplicação, introduza `https://login.microsoftonline.com` no **autorizado JavaScript origens** campo, e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no **autorizados redirecionar os URIs** campo. Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com). O **{inquilino}** valor diferencia maiúsculas de minúsculas. Clique em **Criar**.
   
    ![Google + - criar o ID de cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Copie os valores de **ID de cliente** e **segredo do cliente**. Terá de configurar Google + como um fornecedor de identidade no seu inquilino. **Segredo do cliente** é uma credencial de segurança importantes.
   
    ![Google + - segredo do cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configurar Google + como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "G +".
5. Clique em **tipo de fornecedor de identidade**, selecione **Google**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade** e introduza o ID de cliente e o segredo do cliente da Google + aplicação que criou anteriormente.
7. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração do Google +.


---
title: "Azure Active Directory B2C: Configuração de conta do Microsoft | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para consumidores com contas Microsoft nas aplicações que estejam protegidas pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 06407322-142c-4cb3-9106-a8d752c4c853
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 59879dc0b3fc1d7af3e2a1f67f1701f451de9126
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com contas Microsoft
## <a name="create-a-microsoft-account-application"></a>Criar uma aplicação da conta Microsoft
Para utilizar a conta Microsoft como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação da conta Microsoft e forneça-lo com os parâmetros corretos. Necessita de uma conta Microsoft para efetuar este procedimento. Se não tiver uma, pode obtê-lo em [https://www.live.com/](https://www.live.com/).

1. Vá para o [Portal de registo de aplicações do Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e inicie sessão com as credenciais da conta Microsoft.
2. Clique em **adicionar uma aplicação**.
   
    ![Microsoft conta - adicionar uma nova aplicação](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Forneça um **nome** para a sua aplicação e clique em **Criar aplicação**.
   
    ![Conta Microsoft – nome da aplicação](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Copie o valor da **Id da aplicação**. Precisará dele para configurar a conta Microsoft como um fornecedor de identidade no seu inquilino.
   
    ![Conta Microsoft – Id da aplicação](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Clique em **adicionar plataforma** e escolha **Web**.
   
    ![Microsoft conta – adicionar plataforma](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Conta Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Introduza `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no **redirecionar URIs** campo. Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com).
   
    ![Conta Microsoft - URL de redirecionamento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Clique em **gerar a nova palavra-passe** sob o **aplicação segredos** secção. Copie a nova palavra-passe apresentado no ecrã. Precisará dele para configurar a conta Microsoft como um fornecedor de identidade no seu inquilino. Esta palavra-passe é uma credencial de segurança importantes.
   
    ![Microsoft conta - gerar nova palavra-passe](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Conta Microsoft - nova palavra-passe](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Selecione a caixa que indica que **suporte Live SDK** sob o **opções avançadas** secção. Clique em **Guardar**.
   
    ![Conta Microsoft – suporte Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurar a conta Microsoft como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "MSA".
5. Clique em **tipo de fornecedor de identidade**, selecione **conta Microsoft**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade** e introduza o Id da aplicação e a palavra-passe da aplicação de conta Microsoft que criou anteriormente.
7. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração da conta Microsoft.


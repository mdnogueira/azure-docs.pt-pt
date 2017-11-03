---
title: "Azure Active Directory B2C: Configuração Facebook | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para consumidores com contas do Facebook nas aplicações que estejam protegidas pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: sromeroz
manager: krassk
editor: sromeroz
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/7/2017
ms.author: sromeroz
ms.openlocfilehash: 8c2154fcf33537358b549395d15b4ba937371cd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com contas do Facebook
## <a name="create-a-facebook-application"></a>Criar uma aplicação do Facebook
Para utilizar o Facebook como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação do Facebook e forneça-lo com os parâmetros corretos. Necessita de uma conta do Facebook para efetuar este procedimento. Se não tiver uma, pode obtê-lo em [https://www.facebook.com/](https://www.facebook.com/).

1. Vá para o [Facebook para programadores](https://developers.facebook.com/) credenciais de contas do Web site e inicie sessão com o Facebook.
2. Se ainda não o tiver feito, terá de registar como um programador do Facebook. Para tal, clique em **registar** (no canto superior direito da página), aceite as políticas do Facebook e conclua os passos de registo.
3. Clique em **aplicações My** e, em seguida, clique em **adicionar uma nova aplicação**. 
4. No formulário, forneça um **nome a apresentar** e um ID válido **correio eletrónico de contacto**.
5. Clique em **criar ID da aplicação**. Esta operação pode requerer a aceitar as políticas de plataforma do Facebook e concluir uma verificação de segurança online.
6. Na coluna esquerda, clique em **definições** e, em seguida, selecione **básico** caso não selecionado já.
7. Selecione um **categoria**. 
8. Clique em **+ adicionar plataforma** e selecione **Web site**.
   
    ![Facebook - definições](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Web site do Facebook - definições-](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Introduza `https://login.microsoftonline.com/` no **URL do Site** campo e, em seguida, clique em **guardar alterações** na parte inferior da página.
   
    ![Facebook - URL do Site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Copie o valor da **ID da aplicação**. Clique em **mostrar** e copie o valor da **segredo da aplicação**. Terá de configurar o Facebook como um fornecedor de identidade no seu inquilino. **Segredo da aplicação** é uma credencial de segurança importantes.
   
    ![Facebook - ID de aplicação e o segredo da aplicação](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Clique em **+ adicionar produto** no painel de navegação esquerdo e, em seguida, o **configurar** botão para **início de sessão do Facebook**.
   
    ![Facebook - início de sessão do Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Clique em **definições** no nav direita em **início de sessão do Facebook**

    ![Facebook - definições de início de sessão do Facebook](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Introduza `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no **URIs de redirecionamento OAuth válido** campo no **definições do cliente OAuth** secção. Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com). Clique em **guardar alterações** na parte inferior da página.
    
    ![Facebook - URI de redirecionamento OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Para tornar a sua aplicação do Facebook utilizável pelo Azure AD B2C, tem de tornar publicamente disponíveis. Pode fazê-lo ao clicar em **revisão da aplicação** no painel de navegação esquerdo e ao ativar o comutador na parte superior da página para **Sim** e clicando em **confirmar**.
    
    ![Facebook - público de aplicação](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurar o Facebook como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "Facebook".
5. Clique em **tipo de fornecedor de identidade**, selecione **Facebook**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade** e introduza o ID da aplicação e o segredo da aplicação (da aplicação do Facebook que criou anteriormente) no **ID de cliente** e **segredo do cliente** campos, respetivamente.
7. Clique em **OK**e, em seguida, clique em **criar** para guardar a configuração do Facebook.

> [!NOTE]
> Adicionar um **fornecedor de identidade** para o inquilino não modificar as políticas existentes. Não se esqueça de atualizar as suas políticas, incluindo o fornecedor de identidade que acabou de criar.
>
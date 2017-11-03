---
title: "Azure Active Directory B2C: Configuração WeChat | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para consumidores com contas de WeChat nas aplicações que estejam protegidas pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d2424c66-ba68-4d82-847e-d137683527b0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: a54aec23d951610118246e9f70cdd27752ef39a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com WeChat contas

> [!NOTE]
> Esta funcionalidade está em pré-visualização.
> 

## <a name="create-a-wechat-application"></a>Criar uma aplicação WeChat

Para utilizar WeChat como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação de WeChat e forneça-lo com os parâmetros corretos. Necessita de uma conta de WeChat para efetuar este procedimento. Se não tiver uma, pode obter uma inscrevendo-se através de uma das suas aplicações móveis ou utilizando o seu número QQ. Depois disso, configurar a sua conta registada com o programa de programador WeChat. Pode encontrar mais informações [aqui](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registar uma aplicação WeChat

1. Aceda a [https://open.weixin.qq.com/](https://open.weixin.qq.com/) e iniciar sessão.
2. Clique em**管理中心**(Centro de gestão).
3. Siga os passos necessários para registar uma nova aplicação.
4. Para**授权回调域**(chamada de retorno URL), introduza `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Por exemplo, se sua `tenant_name` é contoso.onmicrosoft.com, definir o URL seja `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Localizar e copiar a **ID da aplicação** e **chave da aplicação**. Irá precisar deles mais tarde.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurar WeChat como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "WeChat".
5. Clique em **tipo de fornecedor de identidade**, selecione **WeChat**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade**
7. Introduza o **chave da aplicação** que copiou anteriormente como a **ID de cliente**.
8. Introduza o **segredo da aplicação** que copiou anteriormente como a **segredo do cliente**.
9. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração de WeChat.


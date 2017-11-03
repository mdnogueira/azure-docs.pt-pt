---
title: "Azure Active Directory B2C: Configuração LinkedIn | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para os consumidores com contas de LinkedIn nas aplicações que estejam protegidas pelo Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: fa51a16b-9ce9-4e27-9eff-0869b4c4f0ef
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 1a6c4b19261aa34e668554ccad2b6340cddf9bf5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com LinkedIn contas
## <a name="create-a-linkedin-application"></a>Criar uma aplicação LinkedIn
Para utilizar LinkedIn como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação de LinkedIn e forneça-lo com os parâmetros corretos. Necessita de uma conta de LinkedIn para efetuar este procedimento. Se não tiver uma, pode obtê-lo em [https://www.linkedin.com/](https://www.linkedin.com/).

1. Vá para o [site LinkedIn programadores](https://www.developer.linkedin.com/) e inicie sessão com as credenciais da conta LinkedIn.
2. Clique em **aplicações My** na barra de menu superior e, em seguida, clique em **Criar aplicação**.
   
    ![LinkedIn - nova aplicação](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. No **criar uma nova aplicação** formulário, preencha as informações relevantes (**nome da empresa**, **nome**, **Descrição**, **do logótipo do URL da aplicação**, **utilização de aplicações**, **URL do site**, **E-Mail profissionais** e **telefone da empresa**).
4. Aceita o **LinkedIn termos de utilização da API** e clique em **submeter**.
   
    ![LinkedIn - registo de aplicação](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Copie os valores de **ID de cliente** e **segredo do cliente**. (Pode encontrá-los em **chaves de autenticação**.) Terá de configurar LinkedIn como um fornecedor de identidade no seu inquilino.
   
   > [!NOTE]
   > **Segredo do cliente** é uma credencial de segurança importantes.
   > 
   > 
6. Introduza `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no **autorizado URLs de redirecionamento** campo (em **OAuth 2.0**). Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contoso.onmicrosoft.com). Clique em **adicionar**e, em seguida, clique em **atualização**. O **{inquilino}** valor diferencia maiúsculas de minúsculas.
   
    ![LinkedIn - configuração de aplicação](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configurar LinkedIn como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "LI".
5. Clique em **tipo de fornecedor de identidade**, selecione **LinkedIn**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade** e introduza o ID de cliente e o segredo do cliente da aplicação LinkedIn que criou anteriormente.
7. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração de LinkedIn.


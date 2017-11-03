---
title: "Azure Active Directory B2C: Configuração Amazon | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para consumidores com contas do Amazon nas aplicações que estejam protegidas pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 77c099bb-a005-4d75-87f9-f61e3de48725
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: dcc97e1b7f6287bd7692c52bf068950065a26572
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com contas do Amazon
## <a name="create-an-amazon-application"></a>Criar uma aplicação da Amazon
Para utilizar o Amazon como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação do Amazon e forneça-lo com os parâmetros corretos. Necessita de uma conta Amazon para efetuar este procedimento. Se não tiver uma, pode obtê-lo em [http://www.amazon.com/](http://www.amazon.com/).

1. Vá para o [Centro para programadores do Amazon](https://login.amazon.com/) e inicie sessão com as credenciais da conta Amazon.
2. Se ainda não o tiver feito, clique em **inscrever-se**, siga os passos de registo do programador e aceite a política.
3. Clique em **registar a nova aplicação**.
   
    ![Registar uma nova aplicação no Web site da Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Fornecer informações sobre a aplicação (**nome**, **Descrição**, e **URL de aviso de privacidade**) e clique em **guardar**.
   
    ![Fornecer informações sobre a aplicação para registar uma nova aplicação em Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. No **definições Web** secção, copie os valores de **ID de cliente** e **segredo do cliente**. (Tem de clicar no **Mostrar segredo** botão para ver isto.) Terá de configurar o Amazon como um fornecedor de identidade no seu inquilino. Clique em **editar** na parte inferior da secção. **Segredo do cliente** é uma credencial de segurança importantes.
   
    ![Fornecer o ID de cliente e o segredo de cliente para a sua nova aplicação em Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Introduza `https://login.microsoftonline.com` no **permitido origens de JavaScript** campo e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no **permitido os URLs de retorno** campo. Substitua **{inquilino}** com o nome do seu inquilino (por exemplo, contoso.onmicrosoft.com). Clique em **Guardar**. O **{inquilino}** valor diferencia maiúsculas de minúsculas.
   
    ![Fornecer URLs de retorno e origens de JavaScript para a sua nova aplicação em Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configurar o Amazon como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "Amzn".
5. Clique em **tipo de fornecedor de identidade**, selecione **Amazon**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade** e introduza o ID de cliente e o segredo do cliente da aplicação Amazon que criou anteriormente.
7. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração da Amazon.


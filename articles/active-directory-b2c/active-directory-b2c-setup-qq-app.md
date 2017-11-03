---
title: "Azure Active Directory B2C: Configuração QQ | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para consumidores com contas QQ nas aplicações que estejam protegidas pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 18c2cf94-8004-4de1-81c2-e45be65ce12d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: b32e81494b8c84799485f154ae43ad30af394caa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com contas QQ

> [!NOTE]
> Esta funcionalidade está em pré-visualização.
> 

## <a name="create-a-qq-application"></a>Criar uma aplicação QQ

Para utilizar QQ como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação de QQ e forneça-lo com os parâmetros corretos. Necessita de uma conta QQ para efetuar este procedimento. Se não tiver uma, pode obter um de cada [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registar-se o programa de programador QQ

1. Vá para o [portal do programador QQ](http://open.qq.com) e inicie sessão com as credenciais da conta QQ.
2. Depois de iniciarem sessão, aceda a [http://open.qq.com/reg](http://open.qq.com/reg) para registar-se como um programador.
3. No menu, selecione**个人**(programadores individuais).
4. Introduza as informações necessárias para o formulário e clique em**下一步**(passo seguinte).
5. Conclua o processo de verificação do e-mail.

> [!NOTE]
> Terá de aguardar alguns dias seja aprovada após o registo como um programador. 

### <a name="register-a-qq-application"></a>Registar uma aplicação QQ

1. Aceda a [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Clique em**应用管理**(gestão de aplicações).
3. Clique em**创建应用**(Criar aplicação).
4. Introduza as informações da aplicação necessários.
5. Clique em**创建应用**(Criar aplicação).
6. Introduza as informações necessárias.
7. Para o**授权回调域**(chamada de retorno URL) campo, introduza `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Por exemplo, se sua `tenant_name` é contoso.onmicrosoft.com, definir o URL seja `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Clique em**创建应用**(Criar aplicação).
9. Na página de confirmação, clique em**应用管理**(gestão de aplicações) para regressar à página de gestão de aplicações.
10. Clique em**查看**(ver) junto a aplicação que acabou de criar.
11. Clique em**修改**(editar).
12. Na parte superior da página, copie o **ID da aplicação** e **chave da aplicação**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Configurar QQ como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "QQ".
5. Clique em **tipo de fornecedor de identidade**, selecione **QQ**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade**
7. Introduza o **chave da aplicação** que copiou anteriormente como a **ID de cliente**.
8. Introduza o **segredo da aplicação** que copiou anteriormente como a **segredo do cliente**.
9. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração de QQ.


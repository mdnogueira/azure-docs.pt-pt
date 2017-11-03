---
title: "Azure Active Directory B2C: Configuração Weibo | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para consumidores com contas de Weibo nas aplicações que estejam protegidas pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1860de34-94cb-4ceb-851e-102f930f7230
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: 00c5d3781455c80b33bdbb4c872ae354531baf3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com Weibo contas

> [!NOTE]
> Esta funcionalidade está em pré-visualização. Não utilize este fornecedor de identidade no seu ambiente de produção.
> 

## <a name="create-a-weibo-application"></a>Criar uma aplicação Weibo

Para utilizar Weibo como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação de Weibo e forneça-lo com os parâmetros corretos. Necessita de uma conta de Weibo para efetuar este procedimento. Se não tiver uma, pode obter um de cada [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Registar-se o programa de programador Weibo

1. Vá para o [portal do programador Weibo](http://open.weibo.com/) e inicie sessão com as credenciais da conta Weibo.
2. Depois de iniciarem sessão, clique em seu nome a apresentar no canto superior direito.
3. Na lista pendente, selecione**编辑开发者信息**(editar informações de programador).
4. Introduza as informações necessárias para o formulário e clique em**提交**(Submeter).
5. Conclua o processo de verificação do e-mail.
6. Vá para o [página verificação de identidade](http://open.weibo.com/developers/identity/edit).
7. Introduza as informações necessárias para o formulário e clique em**提交**(Submeter).

### <a name="register-a-weibo-application"></a>Registar uma aplicação Weibo

1. Vá para o [nova página de registo de aplicação Weibo](http://open.weibo.com/apps/new).
2. Introduza as informações da aplicação necessários.
3. Clique em**创建**(criar).
4. Copie os valores de **chave da aplicação** e **segredo da aplicação**. Irá precisar deste mais tarde.
5. Carregar fotografias necessárias e introduza as informações necessárias.
6. Clique em**保存以上信息**(guardar).
7. Clique em**高级信息**(avançado informações).
8. Clique em**编辑**(editar) junto ao campo para OAuth2.0**授权设置**(redirecionar URL).
9. Introduza `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` para OAuth2.0**授权设置**(redirecionar URL). Por exemplo, se sua `tenant_name` é contoso.onmicrosoft.com, definir o URL seja `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Clique em**提交**(Submeter).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Configurar Weibo como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "Weibo".
5. Clique em **tipo de fornecedor de identidade**, selecione **Weibo**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade**
7. Introduza o **chave da aplicação** que copiou anteriormente como a **ID de cliente**.
8. Introduza o **segredo da aplicação** que copiou anteriormente como a **segredo do cliente**.
9. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração de Weibo.


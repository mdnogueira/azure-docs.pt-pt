---
title: "Azure Active Directory B2C: Configuração Twitter | Microsoft Docs"
description: "Forneça a inscrição e o início de sessão para consumidores com contas do Twitter nas aplicações que estejam protegidas pelo Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 579a6841-9329-45b8-a351-da4315a6634e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/06/2017
ms.author: parakhj
ms.openlocfilehash: 82a001dd53cdddcf3b360090f3250af593c96fbb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>O Azure Active Directory B2C: Proporcionar inscrição e o início de sessão aos consumidores com contas do Twitter

> [!NOTE]
> Esta funcionalidade está em pré-visualização.
> 

## <a name="create-a-twitter-application"></a>Criar uma aplicação do Twitter
Para utilizar o Twitter como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação do Twitter e forneça-lo com os parâmetros corretos. Necessita de uma conta de programador do Twitter para efetuar este procedimento. Se não tiver uma, pode obtê-lo em [https://dev.twitter.com/](https://dev.twitter.com/).

1. Vá para o [Web site de programadores do Twitter](https://dev.twitter.com/) e inicie sessão com as suas credenciais.
2. Clique em **as minhas aplicações** em **ferramentas & suporte** e, em seguida, clique em **criar nova aplicação**. 
3. No formulário, forneça um valor para o **nome**, **Descrição**, e **Web site**.
4. Para o **URL de chamada de retorno**, introduza `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Certifique-se de que substitui **{inquilino}** com o nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com).
5. Selecione a caixa para aceitar o **programador contrato** e clique em **criar a sua aplicação Twitter**.
6. Assim que a aplicação é criada, clique em **chaves e os Tokens de acesso**.
7. Copie o valor da **consumidor chave** e **segredo de consumidor**. Terá de configurar o Twitter como um fornecedor de identidade no seu inquilino.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como um fornecedor de identidade no seu inquilino
1. Siga estes passos para [navegar para o painel de funcionalidades do B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) no portal do Azure.
2. No painel de funcionalidades do B2C, clique em **fornecedores de identidade**.
3. Clique em **+ Adicionar** na parte superior do painel.
4. Forneça um amigável **nome** para a configuração do fornecedor de identidade. Por exemplo, introduza "Twitter".
5. Clique em **tipo de fornecedor de identidade**, selecione **Twitter**e clique em **OK**.
6. Clique em **configurar este fornecedor de identidade** e introduza o Twitter **consumidor chave** para o **id de cliente** e o Twitter **segredo de consumidor** para o **segredo do cliente**.
7. Clique em **OK**e, em seguida, clique em **criar** para guardar a configuração do Twitter.


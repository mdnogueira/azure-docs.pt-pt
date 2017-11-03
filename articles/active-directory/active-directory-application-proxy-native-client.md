---
title: "Publicar aplicações de cliente nativo - do Azure AD | Microsoft Docs"
description: "Aborda como permitir que as aplicações de cliente nativo comunicar com o Azure AD de conector de Proxy de aplicação para fornecer acesso remoto seguro às suas aplicações no local."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e1f992aede3af99fa7c2ffa661bccbcac9f52ba9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Como ativar aplicações de cliente nativo interagir com aplicações de proxy

Para além de aplicações web, o Proxy de aplicações do Azure Active Directory também pode ser utilizado para publicar aplicações de cliente nativo que estão configuradas com o Azure AD Authentication Library (ADAL). Aplicações de cliente nativo diferem das aplicações web, porque estão instalados num dispositivo, enquanto as web apps são acedidas através de um browser. 

Proxy de aplicações suporta aplicações de cliente nativo pelo Azure AD aceitar e enviados no cabeçalho de tokens emitido. O serviço de Proxy de aplicações efetua a autenticação em nome de utilizadores. Esta solução não utiliza os tokens de aplicação para autenticação. 

![Relação entre os utilizadores finais, o Azure Active Directory e as aplicações publicadas](./media/active-directory-application-proxy-native-client/richclientflow.png)

Utilize o Azure AD Authentication Library, que trata da autenticação e suporta vários ambientes de cliente, para publicar aplicações nativas. Proxy de aplicações se ajusta o [aplicação nativa para cenário de Web API](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). 

Este artigo explica os quatro passos para publicar uma aplicação nativa com o Proxy de aplicações e a biblioteca de autenticação do Azure AD. 

## <a name="step-1-publish-your-application"></a>Passo 1: Publicar a aplicação
Publicar a aplicação de proxy, tal como faria com qualquer outra aplicação e atribuir utilizadores a aceder à aplicação. Para obter mais informações, consulte [publicar aplicações com o Proxy de aplicações](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Passo 2: Configurar a sua aplicação
Configure a sua aplicação nativa da seguinte forma:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para **do Azure Active Directory** > **registos de aplicação**.
3. Selecione **novo registo de aplicação**.
4. Especifique um nome para a sua aplicação, selecione **nativo** como o tipo de aplicação e forneça o URI de redirecionamento para a sua aplicação. 

   ![Criar um novo registo de aplicação](./media/active-directory-application-proxy-native-client/create.png)
5. Selecione **Criar**.

Para obter mais informações sobre como criar um novo registo de aplicação, consulte [integrar aplicações com o Azure Active Directory](.//develop/active-directory-integrating-applications.md).


## <a name="step-3-grant-access-to-other-applications"></a>Passo 3: Conceder acesso aos outras aplicações
Ative a aplicação nativa para ser disponibilizado a outras aplicações no seu diretório:

1. Ainda na **registos de aplicação**, selecione a nova aplicação nativa que acabou de criar.
2. Selecione **as permissões necessárias**.
3. Selecione **Adicionar**.
4. Abra o primeiro passo, **selecionar um API**.
5. Utilize a barra de procura para localizar a aplicação de Proxy de aplicações publicadas na primeira secção. Escolha essa aplicação, em seguida, clique em **selecione**. 

   ![Procure a aplicação de proxy](./media/active-directory-application-proxy-native-client/select_api.png)
6. Abra o segundo passo **selecionar permissões**.
7. Utilize a caixa de verificação para conceder o acesso de aplicação nativa para a aplicação de proxy, em seguida, clique em **selecione**.

   ![Conceder acesso à aplicação de proxy](./media/active-directory-application-proxy-native-client/select_perms.png)
8. Selecione **feito**.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>Passo 4: Editar a biblioteca de autenticação do Active Directory
Edite o código de aplicação nativa no contexto de autenticação do Active Directory Authentication Library (ADAL) para incluir o seguinte texto:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = authContext.AcquireToken("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

As variáveis no código de exemplo devem ser substituídas da seguinte forma:

* **ID de inquilino** podem ser encontrados no portal do Azure. Navegue para **do Azure Active Directory** > **propriedades** e copie o ID de diretório. 
* **URL externo** é o URL de front-end introduzido na aplicação de Proxy. Para localizar este valor, navegue para o **proxy de aplicações** secção da aplicação de proxy.
* **ID da aplicação** da aplicação nativa pode ser encontrado no **propriedades** página da aplicação nativa.
* **Redireciona o URI da aplicação nativa** pode ser encontrado no **redirecionar URIs** página da aplicação nativa.

Depois da ADAL é editada com estes parâmetros, os utilizadores devem conseguir autenticar para aplicações de cliente nativo, mesmo quando estão contidos fora da rede empresarial. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o fluxo de aplicação nativa, consulte [aplicação nativa a web API](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)

Saiba mais sobre como configurar [único início de sessão para o Proxy de aplicações](application-proxy-sso-overview.md)

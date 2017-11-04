---
title: Proteger conectores personalizados com o Azure AD - Azure Logic Apps | Microsoft Docs
description: "Adicionar autenticação e segurança à sua API e um conector com o Azure Active Directory (Azure AD)"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: bcee842cb880002daaa3ae9d9110ee1734941b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Adicionar segurança à sua API e um conector com o Azure Active Directory (Azure AD)

Para proteger chamadas entre a API e um conetor personalizado, adicione a autenticação do Azure AD para a API Web e o conector. Para este cenário, crie duas aplicações do Azure Active Directory (Azure AD) – uma aplicação do Azure AD protege a API Web, enquanto a outra aplicação do Azure AD protege o registo do conetor e adiciona o acesso delegado. 

Este tutorial utiliza a API do Gestor de recursos do Azure como um exemplo. O Azure Resource Manager ajuda a gerir os componentes de uma solução que criou no Azure, tais como bases de dados, as máquinas virtuais e aplicações web. Um conetor personalizado para o Azure Resource Manager poderá ser útil quando pretender gerir recursos do Azure a partir do seus fluxos de trabalho. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode começar com uma [conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, inscreva-se um [subscrição pay as you go](https://azure.microsoft.com/pricing/purchase-options/).

* Para este tutorial, o [OpenAPI um ficheiro de exemplo para o Azure Resource Manager](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > O ficheiro de OpenAPI de exemplo não define a todas as operações do Azure Resource Manager e atualmente contém apenas a operação para [lista todas as subscrições](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List). Pode editar este OpenAPI ou criar outro ficheiro, OpenAPI utilizando o [online OpenAPI editor](http://editor.swagger.io/).
  >
  > Pode aplicar este tutorial para qualquer API RESTful em que pretende utilizar a autenticação do Azure AD.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Criar a sua primeira aplicação do Azure AD para proteger a sua API Web

A primeira aplicação do Azure AD efetua a autenticação quando o conector personalizado chama a API, o que é a API do Gestor de recursos do Azure, neste exemplo.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Se tiver mais do que um inquilino do Azure Active Directory, certifique-se de que tem sessão iniciada diretório correto, verificando o diretório em seu nome de utilizador. 

   ![Confirme o seu diretório](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > Altere os diretórios, escolha o seu nome de utilizador, e em seguida, selecione o diretório que pretende.

2. No menu principal do Azure, escolha **do Azure Active Directory** para que possa vê o seu diretório atual.

   ![Escolha "Do Azure Active Directory"](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Se o menu principal do Azure não mostra **do Azure Active Directory**, escolha **mais serviços**. No **filtro** caixa, escreva "Do Azure Active Directory" como o filtro, em seguida, escolha **do Azure Active Directory**.

3. No menu de diretório, sob **gerir**, escolha **registos de aplicação**. Na lista de aplicações registado, escolha **+ novo registo de aplicação**.

   ![Escolha "Registos de aplicação", "+ novo registo de aplicação"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. Em **criar**, forneça os detalhes para a sua aplicação do Azure AD, conforme descrito na tabela, em seguida, escolha **criar**. 

   ![Criar aplicação Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *Web---nome da aplicação api* | O nome do Azure AD aplicação sua API Web | 
   | **Tipo de aplicação** | **Aplicação Web / API** | Tipo da sua aplicação | 
   | **URL de início de sessão** | `https://login.windows.net` | | 
   |||| 

5. Quando regressar ao seu diretório **registos de aplicação** lista, selecione a aplicação do Azure AD.

   ![Selecione a aplicação do Azure AD a partir da lista](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. Quando for apresentada a página de detalhes da aplicação, certifique-se de que a **copie e guarde a aplicação *ID da aplicação* num local seguro**. Este ID é necessário para utilização posterior.

   ![Guardar "ID da aplicação" para utilização posterior](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Fornece um URL de resposta para a sua aplicação do Azure AD. A aplicação **definições** menu, escolha **URLs de resposta**. Introduzir este URL, em seguida, escolha **guardar**.

   ![URLs de resposta](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **URLs de resposta** | Para sua própria API, introduza este URL: </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **Permissões delegadas** | {não necessário} | | 
   | **Chave de cliente** | {não necessário} | | 
   |||| 

   > [!TIP]
   > Se o **definições** menu anteriormente não aparece, escolha **definições** aqui:
   >
   > ![Escolha a "Definições"](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. Criar a sua aplicação do Azure AD segundo para o conetor personalizado

A aplicação do Azure AD segundo protege o registo do conetor personalizado e adiciona o acesso delegado para a API Web protegida pela primeira aplicação do Azure AD. 

> [!IMPORTANT]
> Certifique-se de que ambas as suas aplicações do Azure AD existem no mesmo diretório.

1. Volte à **registos de aplicação** lista e escolha **+ novo registo de aplicação** novamente.

   ![Escolha "Registos de aplicação", "+ novo registo de aplicação"](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. Em **criar**, forneça os detalhes para a sua aplicação do Azure AD segundo, conforme descrito na tabela, em seguida, escolha **criar**. 

   ![Criar aplicação Azure AD](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome do conector* | O nome da aplicação do seu conector do Azure AD | 
   | **Tipo de aplicação** | **Aplicação Web / API** | Tipo da sua aplicação | 
   | **URL de início de sessão** | `https://login.windows.net` | | 
   |||| 

3. Quando regressar ao seu diretório **registos de aplicação** lista, selecione a aplicação do Azure AD segundo.

   ![Selecione a aplicação do Azure AD segundo na lista](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. Quando for apresentada a página de detalhes da aplicação, certifique-se de que é também **copiar e guardar esta aplicação *ID da aplicação* num local seguro demasiado**. Este ID é necessário para utilização posterior.

   ![Guardar "ID da aplicação" para utilização posterior](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Fornece um URL de resposta para a sua aplicação do Azure AD. A aplicação **definições** menu, escolha **URLs de resposta**. Introduzir este URL, em seguida, escolha **guardar**.

   | Definição | Valor sugerido | 
   | ------- | --------------- | 
   | **URLs de resposta** | Para o conector do Azure Resource Manager personalizado, introduza este URL:`https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > Se o **definições** menu anteriormente não aparece, escolha **definições** aqui:
   >
   > ![Escolha a "Definições"](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. Volta a **definições** menu, escolha **as permissões necessárias** > **adicionar**.

   ![As permissões necessárias > Adicionar](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. Quando o **acesso à API adicionar** é aberto o menu, escolha **selecionar um API** > 
**API de gestão de serviços do Windows Azure** > **selecione **.

   ![Selecione uma API](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. No **acesso à API adicionar** menu, escolha **selecionar permissões**. Em **permissões delegadas**, escolha **gestão de serviço de acesso do Azure como os utilizadores da organização** > **selecione**.

   ![Escolha "Delegados permissões" > "acesso gestão de serviços do Azure como os utilizadores da organização"](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   Caso contrário, para outras opções:

   | Opção | Valor sugerido | Descrição | 
   | ------ | --------------- | ----------- | 
   | **Permissões delegadas** | | Selecione as permissões para acesso delegado a sua API Web | 
   |||| 

9. Agora no **acesso à API adicionar** menu, escolha **feito**.

   ![Menu "Adicionar acesso à API" > "Concluído"](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. Agora gerar um *chave cliente*, ou "secreta", para a aplicação do seu conector do Azure AD. 

    1. Reverter o **definições** menu, escolha **chaves**. 
    Forneça um nome para a sua chave com 16 ou menos carateres, selecione um período de expiração e, em seguida, escolha **guardar**.

       ![Criar uma chave de cliente](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. Quando for apresentada a sua chave gerada, **Certifique-se de que copiar e guardar esta chave num local seguro** antes de sair do **chaves** página.
    
       ![Copiar e guardar a chave manualmente](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. Depois de guardar a chave, com segurança pode fechar o **definições** menu.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Adicionar autenticação do Azure AD para a sua aplicação de Web API

Ative agora a autenticação para a API Web com a sua primeira aplicação do Azure AD. Para obter mais informações, saiba [como configurar a sua aplicação de serviço de aplicações para utilizar o início de sessão do Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

1. No [portal do Azure](https://portal.azure.com), localizar a aplicação de Web API que publicou anteriormente no [criar conectores personalizados a partir de APIs da Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md).

2. Em **definições**, escolha **autenticação / autorização**. 

   1. Em **autenticação do serviço de aplicações**, escolha **no**.
   2. Para **ação a tomar quando o pedido não é autenticado**, selecione **iniciar sessão com o Azure Active Directory**.
   3. Para **fornecedores de autenticação**, selecione **do Azure Active Directory**. 

   ![Escolha "autenticação / autorização"](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. No **definições do Azure Active Directory** página:

   1. Em **modo de gestão**, escolha **Express**.

   2. Agora, selecione a aplicação do Azure AD que a aplicação de Web API utiliza para autenticação. 
   Escolha **selecionar aplicação AD existente** > **aplicação Azure AD**.

   3. Em **aplicações do Azure AD**, selecione a aplicação do Azure AD que criou anteriormente para a sua aplicação de Web API. 
   
   4. Escolha **OK** até voltar para o **autenticação / autorização** página.

   Por exemplo:

   ![Escolha a aplicação do Azure AD que representa a sua aplicação de Web API](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. No **autenticação / autorização** página, escolha **guardar**.

   ![Guardar as definições de autenticação](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   Agora a aplicação de Web API pode utilizar o Azure AD para autenticação.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Configurar a autenticação do Azure AD no ficheiro de OpenAPI a API Web

Abra o ficheiro de OpenAPI para a sua aplicação de Web API, para que possa adicionar o `securityDefintions` objeto e a autenticação do Azure AD sob o `host` propriedade. Eis um exemplo que mostra o `host` propriedade e `securityDefinitions` objeto:

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
Agora está pronto para criar e registar o conector personalizado.

## <a name="next-steps"></a>Passos seguintes

* [Registar os seus conectores](../logic-apps/logic-apps-custom-connector-register.md)
* [FAQ sobre o conetor personalizado](../logic-apps/custom-connector-faq.md)

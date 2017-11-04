---
title: "Adicionar autenticação à APIs personalizadas - Azure Logic Apps | Microsoft Docs"
description: "Configurar a autenticação para chamadas para as suas APIs personalizadas a partir das logic apps"
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
ms.openlocfilehash: 2528f4318d92bbfdc1008795876f0240a5e3e4f6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="secure-calls-to-your-custom-apis-from-logic-apps"></a>Chamadas seguras para as suas APIs personalizadas a partir das logic apps

Para proteger as chamadas para as suas APIs, pode configurar a autenticação do Azure Active Directory (Azure AD) através do portal do Azure para que não tem de atualizar o seu código. Em alternativa, pode exigir e impor a autenticação através de código da API.

## <a name="authentication-options-for-your-api"></a>Opções de autenticação para a API

Pode proteger as chamadas para a API personalizada nas seguintes formas:

* [Sem alterações de código](#no-code): proteger a sua API com [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) através do portal do Azure, por isso, não tem de atualizar o seu código ou voltar a implementar a sua API.

  > [!NOTE]
  > Por predefinição, a autenticação do Azure AD que ativar no portal do Azure não fornece autorização detalhada. Por exemplo, esta autenticação bloqueia a API apenas um inquilino específico, não para um utilizador específico ou aplicação. 

* [Atualizar o código da API](#update-code): proteger a sua API através da imposição [autenticação de certificado](#certificate), [autenticação básica](#basic), ou [autenticação do Azure AD](#azure-ad-code) através de código.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Efetuar chamadas à sua API sem alterar o código

Eis os passos gerais para este método:

1. Criar dois identidades de aplicação do Azure Active Directory (Azure AD): uma para a sua aplicação lógica e um para a sua aplicação web (ou a aplicação API).

2. Para efetuar chamadas à sua API, a utilizar as credenciais (ID de cliente e segredo) para o principal de serviço que está associada a identidade da aplicação do Azure AD para a sua aplicação lógica.

3. Incluem os IDs de aplicações na sua definição de aplicação lógica.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>Parte 1: Criar uma identidade de aplicação do Azure AD para a sua aplicação lógica

A aplicação lógica utiliza esta identidade de aplicação do Azure AD para autenticar no Azure AD. Só tem de configurar esta identidade de uma vez para o seu diretório. Por exemplo, pode optar por utilizar a mesma identidade para todas as suas logic apps, mesmo que pode criar identidades únicas de cada aplicação lógica. Pode configurar estas identidades no portal do Azure ou utilizar [PowerShell](#powershell).

**Criar a identidade da aplicação para a sua aplicação lógica no portal do Azure**

1. No [portal do Azure](https://portal.azure.com "https://portal.azure.com"), escolha **do Azure Active Directory**. 

2. Certifique-se de que está no mesmo diretório como a sua aplicação web ou aplicação API.

   > [!TIP]
   > Para mudar diretórios, escolha o seu perfil e selecione outro diretório. Em alternativa, escolha **descrição geral** > **diretório comutador**.

3. No menu de diretório, sob **gerir**, escolha **registos de aplicação** > **novo registo de aplicação**.

   > [!TIP]
   > Por predefinição, a lista de registos de aplicações mostra todos os registos de aplicação no seu diretório. Para ver apenas os registos de aplicações, junto à caixa de pesquisa, selecione **as minhas aplicações**. 

   ![Criar novo registo de aplicação](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Dê um nome a sua identidade de aplicação, deixe **tipo de aplicação** definido como **aplicação Web / API**, forneça um exclusivo cadeia formatada como um domínio para **URL de início de sessão**e escolha **criar**.

   ![Forneça um nome e início de sessão no URL para a identidade da aplicação](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   A identidade da aplicação que criou para a sua aplicação lógica agora é apresentado na lista de registos de aplicações.

   ![Identidade da aplicação para a sua aplicação lógica](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Na lista de registos de aplicações, selecione a sua identidade de aplicação nova. Copie e guarde o **ID da aplicação** para utilizar como o "ID de cliente" para a sua aplicação lógica na parte 3.

   ![Copie e guarde o ID da aplicação para a aplicação lógica](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Se as definições de identidade da aplicação não estão visíveis, escolha **definições** ou **todas as definições**.

7. Em **acesso à API**, escolha **chaves**. Em **Descrição**, forneça um nome para a sua chave. Em **expira**, selecione uma duração para a sua chave.

   A chave que estiver a criar atua como a identidade da aplicação "secreta" ou palavra-passe para a sua aplicação lógica.

   ![Crie uma chave para a identidade de aplicação lógica](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Na barra de ferramentas, escolha **guardar**. Em **valor**, agora é apresentada a sua chave. 
**Certifique-se de que copia e guarda a chave de** para utilização posterior porque a chave está oculto quando deixar o **chaves** página.

   Quando configura a sua aplicação lógica em parte 3, especifique esta chave como o "segredo" ou a palavra-passe.

   ![Copie e guarde a chave para utilizar mais tarde](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Criar a identidade da aplicação para a sua aplicação lógica no PowerShell**

Pode efetuar esta tarefa através do Azure Resource Manager com o PowerShell. No PowerShell, execute estes comandos:

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Certifique-se ao copiar o **ID do inquilino** (GUID para o seu inquilino do Azure AD), o **ID da aplicação**e a palavra-passe que utilizou.

Para obter mais informações, saiba como [criar um principal de serviço com o PowerShell para aceder a recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>Parte 2: Criar uma identidade de aplicação do Azure AD para a sua aplicação web ou aplicação API

Se já estiver implementada a sua aplicação web ou aplicação API, pode ativar a autenticação e criar a identidade da aplicação no portal do Azure. Caso contrário, pode [ativar a autenticação quando implementar com um modelo Azure Resource Manager](#authen-deploy). 

**Criar a identidade da aplicação e ativar a autenticação no portal do Azure para as aplicações implementadas**

1. No [portal do Azure](https://portal.azure.com "https://portal.azure.com"), localize e selecione a sua aplicação web ou aplicação API. 

2. Em **definições**, escolha **autenticação/autorização**. Em **autenticação do serviço de aplicações**, ativar a autenticação **no**. Em **fornecedores de autenticação**, escolha **do Azure Active Directory**.

   ![Ativar a autenticação](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Agora crie uma identidade de aplicação para a sua aplicação web ou aplicação API, conforme mostrado aqui. No **definições do Azure Active Directory** página, defina **modo de gestão** para **Express**. Escolha **criar nova aplicação AD**. Dê um nome a sua identidade da aplicação e escolha **OK**. 

   ![Criar a identidade da aplicação para a sua aplicação web ou aplicação API](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. No **autenticação / autorização** página, escolha **guardar**.

Agora tem de encontrar o cliente ID e o ID de inquilino para a identidade de aplicação que está associada à sua aplicação web ou aplicação API. Utilize estes IDs de parte 3. Para continuar com estes passos para o portal do Azure.

**Localizar o ID de cliente e o ID de inquilino da identidade da aplicação para a sua aplicação web ou aplicação API no portal do Azure**

1. Em **fornecedores de autenticação**, escolha **do Azure Active Directory**. 

   ![Escolha "Do Azure Active Directory"](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. No **definições do Azure Active Directory** página, defina **modo de gestão** para **avançadas**.

3. Copiar o **ID de cliente**e guarde este GUID para utilização na parte 3.

   > [!TIP] 
   > Se **ID de cliente** e **Url do emissor** não aparecer, tente atualizar o portal do Azure e repita o passo 1.

4. Em **Url do emissor**, copie e guarde apenas o GUID para parte 3. Também pode utilizar este GUID na sua aplicação web ou modelo de implementação da aplicação API, se necessário.

   Este GUID é o GUID do seu inquilino específico ("ID do inquilino") e deve aparecer neste URL:`https://sts.windows.net/{GUID}`

5. Sem guardar as alterações, feche o **definições do Azure Active Directory** página.

<a name="authen-deploy"></a>

**Ativar a autenticação quando implementar com um modelo Azure Resource Manager**

Tem ainda de criar uma identidade de aplicação do Azure AD para a sua aplicação web ou aplicação API que difere da identidade de aplicação para a sua aplicação lógica. Para criar a identidade da aplicação, siga os passos anteriores no parte 2 para o portal do Azure. 

Também pode seguir os passos na parte 1, mas certifique-se de que utiliza a sua aplicação web ou da aplicação API real `https://{URL}` para **URL de início de sessão** e **URI de ID de aplicação**. Destes passos, tem de guardar o ID de cliente e o ID do inquilino para utilizar no modelo de implementação da aplicação e também para parte 3.

> [!NOTE]
> Quando cria a identidade da aplicação do Azure AD para a sua aplicação web ou aplicação API, tem de utilizar o portal do Azure, não PowerShell. O commandlet PowerShell não configurar as permissões necessárias para a sessão dos utilizadores para um Web site.

Depois de a instalar o cliente ID e o ID de inquilino, inclua estes IDs como um subresource da sua aplicação web ou aplicação API no seu modelo de implementação:

``` json
"resources": [ {
    "apiVersion": "2015-08-01",
    "name": "web",
    "type": "config",
    "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
    "properties": {
        "siteAuthEnabled": true,
        "siteAuthSettings": {
            "clientId": "{client-ID}",
            "issuer": "https://sts.windows.net/{tenant-ID}/",
        }
    }
} ]
```

Para implementar automaticamente uma aplicação web em branco e uma aplicação lógica, juntamente com a autenticação do Azure Active Directory, [ver o modelo completo aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), ou clique em **implementar no Azure** aqui:

[![Implementar no Azure](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>Parte 3: Preencher a secção de autorização na sua aplicação lógica

O modelo anterior já tem esta secção de autorização, configurar, mas se estiver a criar diretamente a aplicação lógica, tem de incluir a secção de autorização completa.

Abra a definição da aplicação lógica na vista de código, visite o **HTTP** secção de ação, localizar o **autorização** secção e incluir esta linha:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Elemento | Necessário | Descrição | 
| ------- | -------- | ----------- | 
| Inquilino | Sim | O GUID para o inquilino do Azure AD | 
| público-alvo | Sim | O GUID para o recurso de destino que pretende aceder, que é o ID de cliente da identidade da aplicação para a sua aplicação web ou aplicação API | 
| ID de cliente | Sim | O GUID para o cliente que pedem acesso, o que é o ID de cliente da identidade da aplicação para a sua aplicação lógica | 
| segredo | Sim | A chave ou a palavra-passe da identidade da aplicação para o cliente que está a solicitar o token de acesso | 
| tipo | Sim | O tipo de autenticação. Para a autenticação ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`. | 
|||| 

Por exemplo:

``` json
{
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Chamadas de API seguras através de código

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Autenticação de certificados

Para validar pedidos recebidos da sua aplicação lógica à sua aplicação web ou aplicação API, pode utilizar certificados de cliente. Para configurar o seu código, saiba [como configurar a autenticação mútua TLS](../app-service/app-service-web-configure-tls-mutual-auth.md).

No **autorização** secção, incluir esta linha: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Elemento | Necessário | Descrição | 
| ------- | -------- | ----------- | 
| tipo | Sim | O tipo de autenticação. Para certificados de cliente SSL, o valor tem de ser `ClientCertificate`. | 
| palavra-passe | Sim | A palavra-passe para aceder ao certificado de cliente (ficheiro PFX) | 
| PFX | Sim | O conteúdo com codificação base64 do certificado de cliente (ficheiro PFX) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Autenticação básica

Para validar pedidos recebidos da sua aplicação lógica à sua aplicação web ou aplicação API, pode utilizar a autenticação básica, tais como um nome de utilizador e palavra-passe. Autenticação básica é um padrão comum e pode utilizar esta autenticação em qualquer idioma utilizado para criar a sua aplicação web ou aplicação API.

No **autorização** secção, incluir esta linha:

`{"type": "basic", "username": "username", "password": "password"}`.

| Elemento | Necessário | Descrição | 
| ------- | -------- | ----------- | 
| tipo | Sim | O tipo de autenticação que pretende utilizar. Para a autenticação básica, o valor tem de ser `Basic`. | 
| o nome de utilizador | Sim | O nome de utilizador que pretende utilizar para autenticação | 
| palavra-passe | Sim | A palavra-passe que pretende utilizar para autenticação | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Autenticação do Active Directory do Azure através de código

Por predefinição, a autenticação do Azure AD que ativar no portal do Azure não fornece autorização detalhada. Por exemplo, esta autenticação bloqueia a API apenas um inquilino específico, não para um utilizador específico ou aplicação. 

Para restringir o acesso à API para a sua aplicação lógica através de código, extraia o cabeçalho que tenha o token de web JSON (JWT). Verifique a identidade do emissor e rejeitar pedidos que não correspondem.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/app-service-authentication-overview.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Passos seguintes

* [Implementar e chamar APIs personalizadas a partir de lógica de fluxos de trabalho da aplicação](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
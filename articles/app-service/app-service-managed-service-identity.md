---
title: "Identidade do serviço no serviço de aplicações e funções do Azure gerido | Microsoft Docs"
description: "Guia de referência e a configuração conceptual para o suporte de identidade de serviço geridas no App Service do Azure e as funções do Azure"
services: app-service
author: mattchenderson
manager: cfowler
editor: 
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/13/2017
ms.author: mahender
ms.openlocfilehash: 59e6db7caf4988623e6d2f93e986b423db7d7248
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-use-azure-managed-service-identity-public-preview-in-app-service-and-azure-functions"></a>Como utilizar a identidade de serviço geridas (pré-visualização pública) do Azure no serviço de aplicações e funções do Azure

> [!NOTE] 
> Identidade de serviço geridas para o serviço de aplicações e funções do Azure está atualmente em pré-visualização.

Este tópico mostra-lhe como criar uma identidade de aplicação gerida para aplicações de serviço de aplicações e funções do Azure e como utilizá-la para aceder a outros recursos. Uma identidade de serviço gerida do Azure Active Directory permite à aplicação aceder facilmente a outros recursos protegidos do AAD, tais como o Cofre de chaves do Azure. A identidade é gerida pela plataforma do Azure e não necessita de aprovisionar ou rodar os segredos. Para obter mais informações sobre a identidade de serviço geridas, consulte o [descrição geral de identidade de serviço geridas](../active-directory/msi-overview.md).

## <a name="creating-an-app-with-an-identity"></a>Criar uma aplicação com uma identidade

A criação de uma aplicação com uma identidade requer uma propriedade adicional para ser definidas na aplicação.

> [!NOTE] 
> Apenas a ranhura de um site primária, irá receber a identidade. Gerido identidades de serviço para implementação ranhuras ainda não são suportadas.


### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade de serviço geridas no portal, irá criar primeiro uma aplicação como normal e, em seguida, ativar a funcionalidade.

1. Crie uma aplicação no portal como faria normalmente. Navegue para o mesmo no portal.

2. Se utilizar uma aplicação de função, navegue para **funcionalidades da plataforma**. Para outros tipos de aplicação, desloque para baixo até o **definições** grupo no painel de navegação esquerdo.

3. Selecione **identidade do serviço gerido**.

4. Comutador **registar com o Azure Active Directory** para **no**. Clique em **Guardar**.

![Identidade do serviço no serviço de aplicações gerido](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-an-azure-resource-manager-template"></a>Utilizar um modelo Azure Resource Manager

Um modelo Azure Resource Manager pode ser utilizado para automatizar a implementação dos seus recursos Azure. Para saber mais sobre a implementação do serviço de aplicações e funções, consulte [automatizar a implementação de recursos no App Service](../app-service/app-service-deploy-complex-application-predictably.md) e [automatizar a implementação de recursos nas funções do Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` podem ser criadas com uma identidade, incluindo a seguinte propriedade na definição do recurso:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Isto indica do Azure para criar e gerir a identidade para a sua aplicação.

Por exemplo, uma aplicação web poderá o seguinte aspeto:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Quando o site é criado, tem as seguintes propriedades adicionais:
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Onde `<TENANTID>` e `<PRINCIPALID>` são substituídos por GUIDs. A propriedade tenantId identifica que pertence a aplicação ao inquilino do AAD. O principalId é um identificador exclusivo para a identidade da aplicação de novo. Dentro do AAD, a aplicação tem o mesmo nome que deu à sua instância do serviço de aplicações ou funções do Azure.

## <a name="obtaining-tokens-for-azure-resources"></a>Obter tokens para recursos do Azure

Uma aplicação pode utilizar a respetiva identidade para obter os tokens para outros recursos protegidos pelo AAD, como o Cofre de chaves do Azure. Estes tokens representam a aplicação de aceder o recursos e não a qualquer utilizador específico da aplicação. 

> [!IMPORTANT]
> Terá de configurar o recurso de destino para permitir o acesso da sua aplicação. Por exemplo, se pedir um token ao Cofre de chaves, terá de certificar-se de que adicionou uma política de acesso que inclui a identidade da aplicação. Caso contrário, as chamadas para o Cofre de chaves serão rejeitadas, mesmo que incluem o token. Para saber mais sobre os recursos que suportam tokens de identidade de serviço geridas, consulte [que suporte do Azure AD a autenticação de serviços do Azure](../active-directory/msi-overview.md#which-azure-services-support-managed-service-identity).

Não há um protocolo REST simple para obter um token no serviço de aplicações e funções do Azure. Para aplicações de .NET, a biblioteca de Microsoft.Azure.Services.AppAuthentication fornece uma abstração relativamente este protocolo e suporta uma experiência de desenvolvimento local.

### <a name="asal"></a>Utilizar a biblioteca de Microsoft.Azure.Services.AppAuthentication para .NET

Para aplicações de .NET e as funções, a forma mais simples para trabalhar com uma identidade de serviço geridas é através do pacote de Microsoft.Azure.Services.AppAuthentication. Esta biblioteca também permite-lhe testar o seu código localmente no computador de desenvolvimento, utilizando a sua conta de utilizador do Visual Studio, o [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest), ou a autenticação integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte o [Microsoft.Azure.Services.AppAuthentication referência]. Esta secção mostra como começar com a biblioteca do seu código.

1. Adicione as referências a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) pacotes de NuGet à sua aplicação.

2.  Adicione o seguinte código à sua aplicação:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Para saber mais sobre Microsoft.Azure.Services.AppAuthentication e as operações de expõe, consulte o [Microsoft.Azure.Services.AppAuthentication referência] e [do serviço de aplicações e KeyVault com .NET de MSI exemplo](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Utilizando o protocolo REST

Uma aplicação com uma identidade de serviço geridas tem duas variáveis de ambiente definidas:
- MSI_ENDPOINT
- MSI_SECRET

O **MSI_ENDPOINT** é um URL local a partir da qual a aplicação pode pedir tokens. Para obter um token para um recurso, efetue um pedido de HTTP GET para este ponto final, incluindo os seguintes parâmetros:

> [!div class="mx-tdBreakAll"]
> |Nome do parâmetro|No|Descrição|
> |-----|-----|-----|
> |Recurso|Consulta|O URI do recurso de recurso do AAD para que um token deve ser obtido.|
> |versão de API|Consulta|A versão da API token a ser utilizado. "2017-09-01" está atualmente a única versão suportada.|
> |segredo|Cabeçalho|O valor da variável de ambiente de MSI_SECRET.|


Uma resposta com êxito de OK 200 inclui um corpo JSON com as seguintes propriedades:

> [!div class="mx-tdBreakAll"]
> |Nome da propriedade|Descrição|
> |-------------|----------|
> |access_token|O token de pedido de acesso. O serviço web de chamada pode utilizar este token para autenticar para o serviço web de receção.|
> |expires_on|O tempo que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até que a hora de expiração. Este valor é utilizado para determinar a duração de tokens em cache.|
> |Recurso|O URI de ID de aplicação do serviço web do recetor.|
> |token_type|Indica o valor de tipo de token. O único tipo que suporte do Azure AD é portador. Para obter mais informações sobre os tokens de portador, consulte [o Framework de autorização de 2.0 OAuth: utilização de tokens de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Esta resposta é igual a [resposta para o pedido de token de acesso de serviço a serviço AAD](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#service-to-service-access-token-response).

> [!NOTE] 
> As variáveis de ambiente são configurar quando o processo é iniciado pela primeira vez, pelo que, depois de ativar a identidade de serviço geridas para a sua aplicação poderá ter de reiniciar a aplicação ou voltar a implementar o código antes `MSI_ENDPOINT` e `MSI_SECRET` estão disponíveis para o seu código.

### <a name="rest-protocol-examples"></a>Exemplos de protocolo REST
Um exemplo de pedido poderá ter o seguinte aspeto:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
E uma resposta de amostra aspeto que poderá ter o seguinte:
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Exemplos de código
Para efetuar este pedido em c#:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> Linguagens .NET, pode também utilizar [Microsoft.Azure.Services.AppAuthentication](#asal) em vez de composição este pedido por si.

No Node.JS:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

No PowerShell:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```


[Microsoft.Azure.Services.AppAuthentication referência]: https://go.microsoft.com/fwlink/p/?linkid=862452
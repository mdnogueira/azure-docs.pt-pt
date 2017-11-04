---
title: "Como utilizar uma identidade de serviço gerida do Azure VM para a aquisição de início de sessão e token"
description: "Passo a passo de instruções para utilizar um MSI de VM do Azure serviço principal para início de sessão e para adquirir um token de acesso."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 905e7b0d8a0c45c98a86882a8c8f387be0950f9f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>Como utilizar um Azure VM geridos serviço de identidade (MSI) para a aquisição de início de sessão e token 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]Depois de ativar a MSI numa VM do Azure, pode utilizar o MSI para início de sessão e pedir um token de acesso. Este artigo mostra-lhe várias formas de utilizar um MSI [principal de serviço](develop/active-directory-dev-glossary.md#service-principal-object) para início de sessão e adquirir um [token de acesso só de aplicação](develop/active-directory-dev-glossary.md#access-token) para aceder a outros recursos, incluindo:

- Silencioso autónoma início de sessão do PowerShell ou a CLI do Azure
- Aquisição token para vários clientes, incluindo .NET, o PowerShell, Bash/CURL, REST
- Inicie sessão com um SDK do Azure, incluindo .NET, Java, Node.js, Python, Ruby

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Se planeia utilizar os exemplos do PowerShell neste artigo, é necessário instalar [Azure PowerShell versão 4.3.1](https://www.powershellgallery.com/packages/AzureRM) ou superior. Se planeia utilizar os exemplos da CLI do Azure neste artigo, tem três opções:
- Utilize [Shell de nuvem do Azure](../cloud-shell/overview.md) do portal do Azure.
- Utilize a Shell de nuvem do Azure incorporados através de "Tente-" botão, localizado no canto superior direito de blocos de código da CLI do Azure.
- [Instale a versão mais recente do CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir utilizar a CLI numa linha de comandos local. 


> [!IMPORTANT]
> - Todos os código/script de exemplo neste artigo assume que o cliente está em execução numa máquina Virtual ativada de MSI. Para obter mais informações sobre como ativar MSI numa VM, consulte [configurar uma VM geridos serviço de identidade (MSI) no portal do Azure](msi-qs-configure-portal-windows-vm.md), ou um dos artigos variantes (utilizando o PowerShell, CLI, um modelo ou um SDK do Azure). 
> - Para evitar erros de autorização (403/AuthorizationFailed) nos exemplos de código/script, identidade a VM deve ser fornecido acesso de "Leitor" no âmbito de VM para permitir operações do Azure Resource Manager na VM. Consulte [atribuir acesso uma identidade de serviço geridas (MSI) a um recurso no portal do Azure](msi-howto-assign-access-portal.md) para obter mais detalhes.
> - Antes de avançar para uma das seguintes secções, utilize a funcionalidade de "Ligar" de VM no portal do Azure, para ligar remotamente à sua VM ativada de MSI.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>Como iniciar sessão a partir do PowerShell ou a CLI utilizando MSI

Anteriormente, executar um script com a suas próprias identidade objetivo:
- registá-lo como uma aplicação de cliente confidencial/web com o Azure AD
- iniciar sessão utilizando as credenciais de ID/segredo de cliente da aplicação

Com MSI, o cliente de script já não é necessário registar com o Azure AD nem fornecer credenciais. Nos exemplos abaixo, mostramos como utilizar o MSI da VM principal de serviço para início de sessão.

### <a name="azure-powershell"></a>Azure PowerShell

O script seguinte demonstra como:

- adquirir um token de acesso do MSI para uma VM do Azure
- utilizar o token de acesso para iniciar sessão Azure AD sob o principal de serviço MSI correspondente
- Utilize o principal de serviço do MSI para efetuar uma chamada do Azure Resource Manager, para obter o ID de principal de serviço

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>CLI do Azure

O script seguinte demonstra como:

- iniciar sessão Azure AD ao abrigo do serviço MSI da VM principal
- Utilize o principal de serviço do MSI para efetuar uma chamada do Azure Resource Manager, para obter o ID de principal de serviço

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>Como adquirir um token de acesso de MSI

Utilizar MSI, o cliente/script de aplicação já não é necessário registar com o Azure AD, nem apresentar o respetivo cliente ID e o segredo para obter um token de acesso. O cliente pode simplesmente colocar o ponto final MSI local para um token de acesso, sem apresentar credenciais de aplicação. O token de acesso só de aplicação é emitido para o principal de serviço do MSI, adequado para utilização como um token de portador no [que exigem que as credenciais de cliente de chamadas de serviço a serviço](active-directory-protocols-oauth-service-to-service.md).

Nos exemplos abaixo, mostramos como utilizar o MSI da VM para a aquisição do token.

### <a name="net"></a>.NET

> [!IMPORTANT]
> O Azure AD Authentication Library (ADAL) não está integrado MSI. Para obter um token de acesso utilizando MSI, efetue um pedido para o ponto final de MSI local numa VM. Para obter mais informações, consulte [perguntas frequentes do MSI e problemas conhecidos](msi-known-issues.md#frequently-asked-questions-faqs).

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>O Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

Exemplo de pedido:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, com a indicação de que pretende obter dados do ponto final. Neste caso, um OAuth token de acesso ao. | 
| `http://localhost:50342/oauth2/token` | O MSI ponto final, onde 50342 é a porta predefinida e é configurável. |
| `resource` | Um cadeia parâmetro de consulta, que indica que o URI de ID de aplicação do recurso de destino. É também apresentado no `aud` afirmação (público-alvo) do token emitido. Neste exemplo, vamos está a pedir um token de acesso do Azure Resource Manager, que tem um URI de ID de aplicação de https://management.azure.com/. |
| `Metadata` | Um HTTP pedido campo do cabeçalho, necessário para MSI como uma mitigação de ataques de falsificação de pedidos de lado do servidor (SSRF). Este valor tem de ser definido como "true", em todas as letras maiúsculas e minúsculas.

Resposta de amostra:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | Descrição |
| ------- | ----------- |
| `access_token` | O token de pedido de acesso. Quando chamar uma API REST, o token está incorporado a `Authorization` campo de cabeçalho de pedido como um token de "portador", permitindo que a API autenticar o autor da chamada. | 
| `refresh_token` | Não é utilizado pelo MSI. |
| `expires_in` | O número de segundos que o token de acesso continua a ser válida, antes de expirar, a hora de emissão. Tempo de emissão pode ser encontrado no token de `iat` de afirmação. |
| `expires_on` | O período de tempo quando o token de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0:0Z UTC" (corresponde do token `exp` afirmação). |
| `not_before` | O período de tempo quando o token de acesso entra em vigor e pode ser aceites. A data é representada como o número de segundos de "1970-01-01T0:0:0Z UTC" (corresponde do token `nbf` afirmação). |
| `resource` | O recurso foi solicitado o token de acesso para que corresponde à `resource` parâmetro de cadeia do pedido de consulta. |
| `token_type` | O tipo de token, o que é um token de acesso de "Portador", o que significa que o recurso pode conceder acesso ao portador do token. |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>Como utilizar MSI com bibliotecas do SDK do Azure

Azure suporta várias plataformas de programação através de uma série de [Azure SDKs](https://azure.microsoft.com/downloads). Muitos dos mesmos foram atualizados para suportar o início de sessão utilizando um MSI e fornecem exemplos correspondentes para demonstrar a utilização. Esta lista é atualizada como obter suporte adicional for adicionado:

| SDK | Exemplo |
| --- | ------ | 
| .NET | [Implementar um modelo ARM de uma VM do Windows através da identidade de serviço geridas](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core | [Chamar serviços do Azure a partir de uma VM com Linux através da identidade de serviço geridas](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js| [Gerir recursos com a identidade de serviço geridas](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [Utilizar MSI para autenticar simplesmente de dentro de uma VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [Gerir recursos a partir de uma VM MSI-ativado](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>Informações adicionais

### <a name="token-expiration"></a>Expiração do token

O subsistema MSI local coloca em cache tokens. Por conseguinte, pode chamar-as vezes que é que gosta e resulta de uma chamada no-durante a transmissão para o Azure AD apenas se:
- ocorre uma falha de acerto na cache devido a nenhum token na cache
- o token expirou

Se a cache o token no seu código, estará preparado para processar cenários em que o recurso indica que o token expirou.

### <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [que suporte do Azure AD a autenticação de serviços do Azure](msi-overview.md#azure-services-that-support-azure-ad-authentication) para uma lista de serviços que suportam o MSI e os respetivos IDs de recurso.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="sign-in-or-token-acquisition-fails"></a>Falha de aquisição de início de sessão ou token

Certifique-se de que o MSI foi ativado corretamente. Regresse à VM do Azure no [portal do Azure](https://portal.azure.com) e:

- Observe a página "Configuration" e certifique-se MSI ativado = "Yes".
- Observe a página "Extensões" e certifique-se a extensão MSI implementada com êxito.

Se o estiver incorreto, terá de voltar a implementar o MSI no seu recurso ou resolver a falha de implementação.

### <a name="http-request-error-responses"></a>Respostas de erro de pedido HTTP

- *bad_request_102: cabeçalho de metadados necessários não especificado*

  Ambos os `Metadata` campo de cabeçalho do pedido está em falta o pedido ou está formatado incorretamente. O valor tem de ser especificado como `true`, em todas as letras maiúsculas e minúsculas. Consulte o "exemplo de pedido" no [anterior a secção REST](#rest) para obter um exemplo.

- *desconhecido: não foi possível obter o token do Active directory. Para mais informações, consulte os registos no \<caminho do ficheiro\>*

  Certifique-se de que o URI do pedido HTTP GET está formatado corretamente, particularmente o URI especificado na cadeia de consulta do recurso. Consulte o "exemplo de pedido" no [anterior a secção REST](#rest) por exemplo, ou [que suporte do Azure AD a autenticação de serviços do Azure](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de serviços e os respetivos IDs de recurso.

- *unknown_source: fonte desconhecida \<URI\>*

  Certifique-se de que o URI do pedido HTTP GET está formatado corretamente. O `scheme:host/resource-path` parte tem de ser especificada como `http://localhost:50342/oauth2/token`. Consulte o "exemplo de pedido" no [anterior a secção REST](#rest) para obter um exemplo.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma descrição geral do MSI, consulte [descrição geral de identidade de serviço geridas](msi-overview.md).
- Para ativar o MSI numa VM do Azure, consulte [configurar um Azure VM geridos serviço de identidade (MSI) com o PowerShell](msi-qs-configure-powershell-windows-vm.md).

Utilize a seguinte secção de comentários para fornecer comentários e ajudam-nos refinar e formam o nosso conteúdo.


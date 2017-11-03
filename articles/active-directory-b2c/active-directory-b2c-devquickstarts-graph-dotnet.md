---
title: Utilize a Graph API - o Azure AD B2C | Microsoft Docs
description: "Como chamar Graph API para um inquilino do B2C ao utilizar uma identidade de aplicação para automatizar o processo."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: f9904516-d9f7-43b1-ae4f-e4d9eb1c67a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: parakhj
ms.openlocfilehash: ccd8bf902f707390f80e3c377e60dd35d535b4b5
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>O Azure AD B2C: Utilizar o Azure AD Graph API

>[!NOTE]
>Atualmente, tem de utilizar o [AD Graph API do Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview?f=255&MSPPError=-2147217396) para gerir os utilizadores um diretório do Azure AD B2C.

Inquilinos do Azure Active Directory (Azure AD) B2C tendem a ser muito grande. Isto significa que muitas tarefas de gestão comuns do inquilino tem de ser efetuada através de programação. Um exemplo primário é a gestão de utilizadores. Poderá ter de migrar um arquivo de utilizador existente para um inquilino do B2C. Poderá querer alojar o registo de utilizador na sua própria página e criar contas de utilizador no diretório do Azure AD B2C em segundo plano. Estes tipos de tarefas necessitam da capacidade de criar, ler, atualizar e eliminar as contas de utilizador. Pode efetuar estas tarefas, utilizando a Azure AD Graph API.

Para inquilinos do B2C, existem dois modos principais de comunicar com a Graph API.

* Para tarefas interativas e execute-uma vez, deve agir como uma conta de administrador no inquilino do B2C quando efetuar as tarefas. Este modo requer um administrador para iniciar sessão com credenciais para que o administrador possa efetuar quaisquer chamadas para a Graph API.
* Para tarefas automatizadas, contínuas, deve utilizar algum tipo de conta de serviço que fornece com os privilégios necessários para efetuar tarefas de gestão. No Azure AD, pode fazer isto ao registar uma aplicação e autenticar para o Azure AD. Isto é feito utilizando um **ID da aplicação** que utiliza o [conceder credenciais de cliente OAuth 2.0](../active-directory/develop/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Neste caso, a aplicação funciona como o próprio, não como um utilizador, para chamar a API de gráfico.

Neste artigo, iremos falar sobre como efetuar o caso de utilização automatizada. Para demonstrar, iremos irá criar um .NET 4.5 `B2CGraphClient` que efetua o utilizador criar, ler, atualizar e eliminar as operações (CRUD). O cliente terá uma interface de linha de comandos do Windows (CLI) que permite invocar vários métodos. No entanto, o código é escrito comportar-se de forma noninteractive, automatizada.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um inquilino do Azure AD B2C
Antes de poder criar aplicações ou utilizadores ou interagir com o Azure AD de todo, precisa de um inquilino do Azure AD B2C e uma conta de administrador global no inquilino. Se ainda não tiver um inquilino, [introdução ao Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Registar a sua aplicação no seu inquilino
Depois de ter um inquilino do B2C, tem de registar a sua aplicação através do [Portal do Azure](https://portal.azure.com).

> [!IMPORTANT]
> Para utilizar a Graph API com o seu inquilino do B2C, terá de registar uma aplicação dedicada utilizando genérica *registos de aplicação* menu no Portal do Azure, **não** do Azure AD B2C  *Aplicações* menu. Não é possível reutilizar as aplicações de B2C já existentes que registou no Azure AD B2C *aplicações* menu.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Escolha o seu inquilino do Azure AD B2C ao selecionar a sua conta no canto superior direito da página.
3. No painel de navegação da esquerda, escolha **mais serviços**, clique em **registos de aplicação**e clique em **adicionar**.
4. Siga os avisos e crie uma nova aplicação. 
    1. Selecione **aplicação Web / API** como o tipo de aplicação.    
    2. Fornecer **qualquer URI de redirecionamento** (por exemplo, https://B2CGraphAPI) que não é relevante para este exemplo.  
5. A aplicação irá agora aparecem na lista de aplicações, clique nele para obter o **ID da aplicação** (também conhecido como ID de cliente). Copie-o como irá precisar das mesmas numa secção posterior.
6. No menu de definições, clique em **chaves** e adicione uma nova chave (também conhecida como segredo do cliente). Também copiá-lo para utilização numa secção posterior.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Configurar a criar, ler e atualizar as permissões para a sua aplicação
Agora tem de configurar a sua aplicação para obter as permissões necessárias para criar, ler, atualizar e eliminar utilizadores.

1. Continuar no menu de registos de aplicação do portal do Azure, selecione a aplicação.
2. No menu de definições, clique em **as permissões necessárias**.
3. No menu de permissões necessárias, clique em **Windows Azure Active Directory**.
4. No menu de ativar o acesso, selecione o **leitura e escrita de dados de diretório** permissão a partir da **permissões de aplicação** e clique em **guardar**.
5. Por fim, no menu de permissões necessárias, clique no **conceder permissões** botão.

Tem agora uma aplicação que tenha permissão para criar, ler e atualizar utilizadores a partir do seu inquilino do B2C.

> [!NOTE]
> Conceder permissões tornar demorar alguns minutos para processar totalmente.
> 
> 

## <a name="configure-delete-permissions-for-your-application"></a>Configurar permissões de eliminação para a sua aplicação
Atualmente, o *leitura e escrita de dados de diretório* permissão **não** incluem a capacidade de fazer qualquer eliminações como eliminar os utilizadores. Se pretender permitir a aplicação a eliminar utilizadores, terá de executar estes passos adicionais que envolvem o PowerShell, caso contrário, pode avançar para a secção seguinte.

Em primeiro lugar, se ainda não tiver instalado, instale o [módulo do Azure AD PowerShell v1 (MSOnline)](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0):

```powershell
Install-Module MSOnline
```

Depois de instalar o módulo do PowerShell ligar ao seu inquilino do Azure AD B2C.

> [!IMPORTANT]
> Tem de utilizar uma conta de administrador de inquilino B2C é **local** para o inquilino do B2C. Estas contas tem o seguinte aspeto: myusername@myb2ctenant.onmicrosoft.com.

```powershell
Connect-MsolService
```

Agora iremos utilizar o **ID da aplicação** no script abaixo para atribuir a aplicação a função de administrador de conta de utilizador que irão permiti-lhe eliminar utilizadores. Estas funções tem identificadores bem conhecidos, pelo que tudo o que precisa de fazer é de entrada a **ID da aplicação** no script abaixo.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

A aplicação agora também tem permissões para eliminar os utilizadores do seu inquilino do B2C.

## <a name="download-configure-and-build-the-sample-code"></a>Transferir, configurar e criar o código de exemplo
Em primeiro lugar, transferir o código de exemplo e configurá-lo em execução. Em seguida, iremos irá demorar um olhá-lo.  Pode [transferir o código de exemplo como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Também pode clonar para um diretório da sua preferência:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abra o `B2CGraphClient\B2CGraphClient.sln` solução do Visual Studio no Visual Studio. No `B2CGraphClient` do projeto, abra o ficheiro `App.config`. Substitua as definições de aplicação de três com os seus próprios valores:

```
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Em seguida, clique com botão direito no `B2CGraphClient` solução e reconstrução de exemplo. Se for bem sucedida, agora, deve ter um `B2C.exe` ficheiros executáveis localizados em `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Criar as operações CRUD de utilizador, utilizando a API do gráfico
Para utilizar o B2CGraphClient, abra uma `cmd` Windows comando de linha de comandos e altere o diretório para o `Debug` diretório. Em seguida, execute o `B2C Help` comando.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Esta ação apresenta uma descrição breve de cada comando. Sempre que invoca um destes comandos `B2CGraphClient` faz um pedido para a Azure AD Graph API.

### <a name="get-an-access-token"></a>Obter um token de acesso
Qualquer pedido para a Graph API necessita de um token de acesso para a autenticação. `B2CGraphClient`utiliza o Active Directory Authentication Library (ADAL) open source para ajudar a adquirir tokens de acesso. ADAL facilita a aquisição do token ao fornecer uma API simple e tendo cuidado de alguns detalhes importantes, tais como colocação em cache de tokens de acesso. Não tem de utilizar a ADAL para obter os tokens, embora. Também podem obter tokens por pedidos de HTTP de composição.

> [!NOTE]
> Código de exemplo utiliza a ADAL v2 para comunicar com a Graph API.  Tem de utilizar ADAL v2 ou v3 para obter os tokens de acesso que podem ser utilizados com a Azure AD Graph API.
> 
> 

Quando `B2CGraphClient` é executado, cria uma instância do `B2CGraphClient` classe. O construtor para esta classe configura um andaime de autenticação da ADAL:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Utilizaremos o `B2C Get-User` comando como exemplo. Quando `B2C Get-User` é invocado sem quaisquer entradas adicionais, as chamadas CLI o `B2CGraphClient.GetAllUsers(...)` método. Este método chama `B2CGraphClient.SendGraphGetRequest(...)`, que submete um pedido de HTTP GET para a Graph API. Antes de `B2CGraphClient.SendGraphGetRequest(...)` envia pedidos de GET, primeiro obtém acesso token ao utilizar a ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Pode obter acesso token para a Graph API chamando a ADAL `AuthenticationContext.AcquireToken(...)` método. ADAL, em seguida, devolve uma `access_token` que representa a identidade da aplicação.

### <a name="read-users"></a>Utilizadores de leitura
Quando pretender obter uma lista de utilizadores ou obter um utilizador específico da Graph API, pode enviar um HTTP `GET` pedido para o `/users` ponto final. Um pedido para todos os utilizadores de um inquilino tem o seguinte aspeto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver este pedido, execute:

 ```
 > B2C Get-User
 ```

Existem dois aspetos importantes a ter em atenção:

* O token de acesso adquirido através de ADAL é adicionado ao `Authorization` cabeçalho utilizando o `Bearer` esquema.
* Para inquilinos do B2C, tem de utilizar o parâmetro de consulta `api-version=1.6`.

Ambos estes detalhes são processados os `B2CGraphClient.SendGraphGetRequest(...)` método:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Criar contas de utilizador de consumidor
Quando criar contas de utilizador no seu inquilino do B2C, pode enviar um HTTP `POST` pedido para o `/users` ponto final:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

A maioria destas propriedades neste pedido é necessários para criar utilizadores de consumidor. Para obter mais informações, clique em [aqui](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Tenha em atenção que o `//` comentários foram incluídos para fins de ilustração. Não inclua-los num pedido real.

Para ver o pedido, execute um dos seguintes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

O `Create-User` comando assume um ficheiro. JSON como um parâmetro de entrada. Contém uma representação JSON de um objeto de utilizador. Existem dois ficheiros. JSON de exemplo no código de exemplo: `usertemplate-email.json` e `usertemplate-username.json`. Pode modificar estes ficheiros de acordo com as suas necessidades. Para além dos campos obrigatórios acima, estão incluídos vários campos opcionais que pode utilizar estes ficheiros. Detalhes sobre os campos opcionais que podem ser encontrados no [referência de entidade do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Pode ver como o pedido POST é construído numa `B2CGraphClient.SendGraphPostRequest(...)`.

* Anexa um token de acesso para o `Authorization` cabeçalho do pedido.
* Define `api-version=1.6`.
* Inclui o objeto de utilizador JSON no corpo do pedido.

> [!NOTE]
> Se as contas que pretende migrar a partir de um arquivo de utilizador existente tem fortes palavra-passe que o [força de palavra-passe segura imposta pelo Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), pode desativar o requisito de palavra-passe segura utilizando a `DisableStrongPassword` valor o `passwordPolicies` propriedade. Por exemplo, pode modificar o pedido de utilizador de criar fornecido acima da seguinte forma: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.
> 
> 

### <a name="update-consumer-user-accounts"></a>Atualizar contas de utilizador de consumidor
Quando atualizar objetos de utilizador, o processo é semelhante ao que utilizar para criar objetos de utilizador. Mas este processo utiliza o HTTP `PATCH` método:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",                // this request updates only the user's displayName
}
```

Tente atualizar um utilizador, atualizando os ficheiros JSON com novos dados. Em seguida, pode utilizar `B2CGraphClient` para executar um destes comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecione o `B2CGraphClient.SendGraphPatchRequest(...)` método para obter detalhes sobre como enviar este pedido.

### <a name="search-users"></a>Procurar utilizadores
Pode procurar utilizadores no seu inquilino do B2C de algumas formas. Um, o utilizador a utilizar ID ou dois, utilizando iniciar sessão no identificador o utilizador de objeto (ou seja, o `signInNames` propriedade).

Execute um dos seguintes comandos para procurar um utilizador específico:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Seguem-se alguns exemplos:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Eliminar utilizadores
O processo de eliminação de um utilizador é simples. Utilizar o HTTP `DELETE` método e a construção o URL correto com o ID de objeto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver um exemplo, introduza este comando e ver o pedido de eliminação é impresso na consola:

```
> B2C Delete-User <object-id-of-user>
```

Inspecione o `B2CGraphClient.SendGraphDeleteRequest(...)` método para obter detalhes sobre como enviar este pedido.

Pode efetuar muitas outras ações com a Azure AD Graph API para além da gestão de utilizadores. O [referência da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fornece detalhes sobre cada ação, juntamente com pedidos de exemplo.

## <a name="use-custom-attributes"></a>Utilizar atributos personalizados
A maioria das aplicações de consumidor tem de armazenar algum tipo de informações de perfil de utilizador personalizadas. É uma forma de fazê-lo pode definir um atributo personalizado no seu inquilino do B2C. Em seguida, pode tratar esse atributo da mesma forma que trate quaisquer outras propriedades no objecto de utilizador. Pode atualizar o atributo, eliminar o atributo, o atributo de consulta, enviar o atributo como uma afirmação no início de sessão de tokens e muito mais.

Para definir um atributo personalizado no seu inquilino do B2C, consulte o [referência de atributo personalizado de B2C](active-directory-b2c-reference-custom-attr.md).

Pode ver os atributos personalizados definidos no seu inquilino do B2C utilizando `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

O resultado destas funções revela os detalhes de cada atributo personalizado, tal como:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Pode utilizar o nome completo, como `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como uma propriedade no seus objetos de utilizador.  Atualize o ficheiro. JSON com a nova propriedade e um valor para a propriedade e, em seguida, execute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Ao utilizar `B2CGraphClient`, que tem uma aplicação de serviço que pode gerir os utilizadores de inquilino do B2C através de programação. `B2CGraphClient`utiliza a identidade da sua própria aplicação para autenticar para a Azure AD Graph API. Também adquirir tokens utilizando um segredo do cliente. Como incorporar esta funcionalidade na sua aplicação, lembre-se alguns pontos chave para aplicações de B2C:

* Tem de conceder a aplicação as permissões adequadas no inquilino.
* Por agora, tem de utilizar a ADAL (não MSAL) para obter os tokens de acesso. (Também pode enviar mensagens de protocolo diretamente, sem utilizar uma biblioteca.)
* Quando chamar Graph API, utilize `api-version=1.6`.
* Quando criar e atualizar utilizadores de consumidor, algumas propriedades são necessárias, conforme descrito acima.

Se tiver quaisquer dúvidas ou pedidos de ações que pretende efetuar ao utilizar a Graph API no seu inquilino do B2C, deixe um comentário deste artigo ou um problema de ficheiros no repositório de exemplo de código de GitHub.


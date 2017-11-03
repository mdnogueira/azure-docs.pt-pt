---
title: "Enlaces de funções Microsoft Graph do Azure | Microsoft Docs"
description: "Compreenda como utilizar o Microsoft Graph acionadores e enlaces das funções do Azure."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.openlocfilehash: 8cf2e4e9e9007549dbdc931b4485c4230c536479
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-microsoft-graph-bindings"></a>Enlaces de funções Microsoft Graph do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com o Microsoft Graph acionadores e enlaces de funções do Azure.
Com estas, pode utilizar as funções do Azure para trabalhar com dados, insights e eventos a partir de [Microsoft Graph](https://graph.microsoft.io).

A extensão de Microsoft Graph fornece os enlaces do seguintes:
- Um [enlace de entrada do token de autenticação](#token-input) permite-lhe interagir com quaisquer Microsoft Graph API.
- Um [enlace de entrada de tabela de Excel](#excel-input) permite-lhe ler dados a partir do Excel.
- Um [vínculo de saída do tabela do Excel](#excel-output) permite-lhe modificar a dados do Excel.
- Um [enlace de entrada do ficheiro do OneDrive](#onedrive-input) permite-lhe ler os ficheiros do OneDrive.
- Um [vínculo de saída do ficheiro do OneDrive](#onedrive-output) permite-lhe escrever nos ficheiros no OneDrive.
- Um [vínculo de saída de mensagem do Outlook](#outlook-output) permite-lhe enviar correio eletrónico através do Outlook.
- Uma coleção de [Microsoft Graph webhook acionadores e enlaces](#webhooks) permite-lhe reagir a eventos a partir do Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Enlaces de Microsoft Graph estão atualmente em pré-visualização.

## <a name="setting-up-the-extensions"></a>Configurar as extensões

Enlaces de Microsoft Graph estão disponíveis através de _enlace extensões_. Extensões de enlace estão componentes opcionais para o tempo de execução das funções do Azure. Esta secção mostra como configurar o Microsoft Graph e extensões de token de autenticação.

### <a name="enabling-functions-20-preview"></a>Ativar funções 2.0 pré-visualização

Extensões de enlace só estão disponíveis apenas para a pré-visualização do Azure funções 2.0. 

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Para obter mais informações, consulte [como destino a versões de tempo de execução das funções do Azure](functions-versions.md).

### <a name="installing-the-extension"></a>Instalação da extensão

Para instalar uma extensão do portal do Azure, terá de navegar para um modelo ou do enlace que faz referência. Criar uma nova função e, no ecrã de seleção de modelo, escolha o cenário de "Microsoft Graph". Selecione um dos modelos neste cenário. Em alternativa, pode navegar para o separador "Integrar" de uma função existente e selecione uma dos enlaces abrangidos neste tópico.

Em ambos os casos, será apresentado um aviso que especifica a extensão para ser instalada. Clique em **instalar** para obter a extensão.

> [!Note] 
> Cada extensão só tem de ser instalado uma vez por aplicação de função. O processo de instalação no portal pode demorar até 10 minutos num plano de consumo.

Se estiver a utilizar o Visual Studio, pode obter as extensões instalando estes pacotes de NuGet:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>Configurar a autenticação do serviço de aplicações / autorização

Os enlaces descritos neste tópico requerem uma identidade para ser utilizado. Isto permite que o Microsoft Graph impor permissões e as interações de auditoria. A identidade pode ser um utilizador aceder à sua aplicação ou a própria aplicação. Para configurar esta identidade, terá de configurar [aplicação serviço de autenticação / autorização](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) com o Azure Active Directory. Também terá de pedir as permissões do recurso que requerem as suas funções.

> [!Note] 
> A extensão de Microsoft Graph só suporta a autenticação do AAD. Os utilizadores têm de iniciar sessão com uma conta escolar ou profissional.

Se utilizar o portal do Azure, abaixo o pedido para instalar a extensão, irá ver um aviso que pede-lhe para configurar a aplicação serviço de autenticação / autorização e pedido necessita de permissões de modelo ou do enlace. Clique em **AAD configurar agora** ou **adicionar permissões agora** conforme adequado.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>Enlace de entrada de token de autenticação

Este enlace obtém um AAD token para um determinado recurso e fornece-o para o seu código como uma cadeia. O recurso pode ser nenhum para o qual a aplicação tem as permissões. 

### <a name="configuring-an-auth-token-input-binding"></a>Configurar um enlace de entrada de token de autenticação

O enlace em si não requer quaisquer permissões do AAD, mas, dependendo de como o token é utilizado, poderá ser necessário solicitar permissões adicionais. Verifique os requisitos do recurso que pretende aceder com o token.

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para o token de autenticação. Consulte [utilizar um token de autenticação de entrada do enlace a partir do código](#token-input-code).|
|**tipo**|Necessário - tem de ser definido como `token`.|
|**direção**|Necessário - tem de ser definido como `in`.|
|**identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-Só é válido com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code>-Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code>-Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code>-Utiliza a identidade da aplicação de função.</li></ul>|
|**ID de utilizador** |Se necessário e apenas se _identidade_ está definido como `userFromId`. ID principal de utilizador associado um utilizador anteriormente com sessão iniciada.|
|**userToken**|Se necessário e apenas se _identidade_ está definido como `userFromToken`. Um token válido para a aplicação de função. |
|**recursos**|Necessário - um URL de recurso do AAD para o qual está a ser pedido o token.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>Utilizar uma autenticação de enlace de entrada token a partir do código

O token é sempre apresentado para código como uma cadeia.

#### <a name="sample-getting-user-profile-information"></a>Exemplo: A obter as informações do perfil de utilizador

Suponha que tem o seguinte function.json que define um acionador HTTP com um enlace de entrada token:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# utiliza o token para efetuar uma chamada HTTP para o Microsoft Graph e devolve o resultado:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

O exemplo JS seguinte utiliza o token para efetuar uma chamada HTTP para o Microsoft Graph e devolve o resultado. No `function.json` acima, alterar `$return` para `res` primeiro.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Enlace de entrada de tabela do Excel

Este enlace lê os conteúdos de uma tabela de Excel armazenados no OneDrive.

### <a name="configuring-an-excel-table-input-binding"></a>Configurar um enlace de entrada de tabela de Excel

Este enlace requer as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler ficheiros de utilizador|

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para a tabela de Excel. Consulte [utilizando uma tabela de Excel de entrada do enlace a partir do código](#excel-input-code).|
|**tipo**|Necessário - tem de ser definido como `excel`.|
|**direção**|Necessário - tem de ser definido como `in`.|
|**identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-Só é válido com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code>-Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code>-Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code>-Utiliza a identidade da aplicação de função.</li></ul>|
|**ID de utilizador** |Se necessário e apenas se _identidade_ está definido como `userFromId`. ID principal de utilizador associado um utilizador anteriormente com sessão iniciada.|
|**userToken**|Se necessário e apenas se _identidade_ está definido como `userFromToken`. Um token válido para a aplicação de função. |
|**caminho**|Necessário - o caminho no OneDrive para o livro do Excel.|
|**worksheetName**|A folha de cálculo que é possível localizar a tabela.|
|**tableName**|O nome da tabela. Se não for especificado, será utilizado o conteúdo da folha de cálculo.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>Utilizar um enlace de entrada de tabela de Excel a partir do código

O enlace expõe os seguintes tipos de funções de .NET:
- String [] []
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (utilizando o enlace do modelo estruturais)

#### <a name="sample-reading-an-excel-table"></a>Exemplo: Ler uma tabela do Excel

Suponha que tem o seguinte function.json que define um acionador HTTP com um enlace de entrada do Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# adiciona lê o conteúdo da tabela especificada e devolve-os para o utilizador:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

O seguinte exemplo JS adiciona lê o conteúdo da tabela especificada e devolve-os para o utilizador. No `function.json` acima, alterar `$return` para `res` primeiro.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Enlace de saída do tabela do Excel

Este enlace modifica o conteúdo de uma tabela de Excel armazenados no OneDrive.

### <a name="configuring-an-excel-table-output-binding"></a>Configurar uma tabela do Excel vínculo de saída

Este enlace requer as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Tem acesso total aos ficheiros de utilizador|

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para o token de autenticação. Consulte [através de uma tabela do Excel enlace a partir do código de saída](#excel-output-code).|
|**tipo**|Necessário - tem de ser definido como `excel`.|
|**direção**|Necessário - tem de ser definido como `out`.|
|**identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-Só é válido com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code>-Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code>-Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code>-Utiliza a identidade da aplicação de função.</li></ul>|
|**ID de utilizador** |Se necessário e apenas se _identidade_ está definido como `userFromId`. ID principal de utilizador associado um utilizador anteriormente com sessão iniciada.|
|**userToken**|Se necessário e apenas se _identidade_ está definido como `userFromToken`. Um token válido para a aplicação de função. |
|**caminho**|Necessário - o caminho no OneDrive para o livro do Excel.|
|**worksheetName**|A folha de cálculo que é possível localizar a tabela.|
|**tableName**|O nome da tabela. Se não for especificado, será utilizado o conteúdo da folha de cálculo.|
|**Tipodeatualização**|Necessário - o tipo de alteração para tornar à tabela. Pode ser um dos seguintes valores:<ul><li><code>update</code>-Substitui o conteúdo da tabela no OneDrive.</li><li><code>append</code>-Adiciona o payload ao fim da tabela no OneDrive, criando novas linhas.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>Utilizar uma tabela do Excel enlace a partir do código de saída

O enlace expõe os seguintes tipos de funções de .NET:
- String [] []
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Tipos de objetos personalizados (utilizando o enlace do modelo estruturais)

#### <a name="sample-adding-rows-to-an-excel-table"></a>Exemplo: Adicionar as linhas para uma tabela do Excel

Suponha que tem o seguinte function.json que define um acionador HTTP com um Excel vínculo de saída:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


O exemplo seguinte c# adiciona uma nova linha à tabela (pressupõe-se que única coluna) com base na entrada da cadeia de consulta:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

O seguinte exemplo JS adiciona uma nova linha à tabela (pressupõe-se que única coluna) com base na entrada da cadeia de consulta. No `function.json` acima, alterar `$return` para `res` primeiro.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>Enlace de entrada de ficheiro do OneDrive

Este enlace lê os conteúdos de um ficheiro armazenados no OneDrive.

### <a name="configuring-a-onedrive-file-input-binding"></a>Configurar um enlace de entrada de ficheiro do OneDrive

Este enlace requer as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Ler ficheiros de utilizador|

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para o ficheiro. Consulte [utilizando um ficheiro do OneDrive de entrada do enlace a partir do código](#onedrive-input-code).|
|**tipo**|Necessário - tem de ser definido como `onedrive`.|
|**direção**|Necessário - tem de ser definido como `in`.|
|**identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-Só é válido com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code>-Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code>-Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code>-Utiliza a identidade da aplicação de função.</li></ul>|
|**ID de utilizador** |Se necessário e apenas se _identidade_ está definido como `userFromId`. ID principal de utilizador associado um utilizador anteriormente com sessão iniciada.|
|**userToken**|Se necessário e apenas se _identidade_ está definido como `userFromToken`. Um token válido para a aplicação de função. |
|**caminho**|Necessário - o caminho do ficheiro no OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>Utilizar um enlace de entrada do ficheiro do OneDrive a partir do código

O enlace expõe os seguintes tipos de funções de .NET:
- Byte]
- Fluxo
- Cadeia
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>Exemplo: Ler um ficheiro do OneDrive

Suponha que tem o seguinte function.json que define um acionador HTTP com um enlace de entrada do OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# lê o ficheiro especificado na cadeia de consulta e regista o seu comprimento:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

O seguinte exemplo JS lê o ficheiro especificado na cadeia de consulta e devolve o comprimento. No `function.json` acima, alterar `$return` para `res` primeiro.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>Enlace de saída do ficheiro do OneDrive

Este enlace modifica o conteúdo de um ficheiro armazenados no OneDrive.

### <a name="configuring-a-onedrive-file-output-binding"></a>Configurar um OneDrive ficheiro vínculo de saída

Este enlace requer as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Tem acesso total aos ficheiros de utilizador|

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para o ficheiro. Consulte [utilizando um ficheiro do OneDrive enlace a partir do código de saída](#onedrive-output-code).|
|**tipo**|Necessário - tem de ser definido como `onedrive`.|
|**direção**|Necessário - tem de ser definido como `out`.|
|**identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-Só é válido com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code>-Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code>-Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code>-Utiliza a identidade da aplicação de função.</li></ul>|
|**ID de utilizador** |Se necessário e apenas se _identidade_ está definido como `userFromId`. ID principal de utilizador associado um utilizador anteriormente com sessão iniciada.|
|**userToken**|Se necessário e apenas se _identidade_ está definido como `userFromToken`. Um token válido para a aplicação de função. |
|**caminho**|Necessário - o caminho do ficheiro no OneDrive.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>Utilizar um OneDrive ficheiro vínculo de saída a partir do código

O enlace expõe os seguintes tipos de funções de .NET:
- Byte]
- Fluxo
- Cadeia
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>Exemplo: Escrever um ficheiro no OneDrive

Suponha que tem o seguinte function.json que define um acionador HTTP com um OneDrive vínculo de saída:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# obtém o texto da cadeia de consulta e escreve-a para um ficheiro de texto (FunctionsTest.txt como definido na configuração acima) na raiz do OneDrive do emissor:

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
O seguinte exemplo JS obtém o texto da cadeia de consulta e escreve-a para um ficheiro de texto (FunctionsTest.txt conforme definido na configuração acima) na raiz do OneDrive da função invocadora. No `function.json` acima, alterar `$return` para `res` primeiro.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Enlace de saída de mensagem do Outlook

Envia uma mensagem de correio através do Outlook.

### <a name="configuring-an-outlook-message-output-binding"></a>Configurar uma mensagem do Outlook vínculo de saída

Este enlace requer as seguintes permissões do AAD:
|Recurso|Permissão|
|--------|--------|
|Microsoft Graph|Enviar correio eletrónico como utilizador|

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para a mensagem de correio. Consulte [através de uma mensagem do Outlook enlace a partir do código de saída](#outlook-output-code).|
|**tipo**|Necessário - tem de ser definido como `outlook`.|
|**direção**|Necessário - tem de ser definido como `out`.|
|**identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-Só é válido com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code>-Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code>-Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code>-Utiliza a identidade da aplicação de função.</li></ul>|
|**ID de utilizador** |Se necessário e apenas se _identidade_ está definido como `userFromId`. ID principal de utilizador associado um utilizador anteriormente com sessão iniciada.|
|**userToken**|Se necessário e apenas se _identidade_ está definido como `userFromToken`. Um token válido para a aplicação de função. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>Utilizar uma mensagem Outlook enlace a partir do código de saída

O enlace expõe os seguintes tipos de funções de .NET:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- Cadeia
- Tipos de objetos personalizados (utilizando o enlace do modelo estruturais)

#### <a name="sample-sending-an-email-through-outlook"></a>Exemplo: Enviar uma mensagem de e-mail através do Outlook

Suponha que tem o seguinte function.json que define um acionador HTTP com uma mensagem do Outlook vínculo de saída:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# envia uma mensagem do autor da chamada para um destinatário especificado na cadeia de consulta:

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

O seguinte exemplo JS envia uma mensagem do autor da chamada para um destinatário especificado na cadeia de consulta:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Enlaces de webhook Microsoft Graph

Webhooks permitem-lhe reagir a eventos na Microsoft Graph. Para suportar webhooks, as funções são necessárias para criar, atualizar e reagir a _subscrições de webhook_. Uma solução completa de webhook exigirá uma combinação dos enlaces seguintes:
- A [acionador de webhook Microsoft Graph](#webhook-trigger) permite-lhe reagir a um webhook de entrada.
- A [enlace de entrada de subscrição do Microsoft Graph webhook](#webhook-input) permite-lhe a lista de subscrições existentes e, opcionalmente, atualize-los.
- A [vínculo de saída de subscrição do Microsoft Graph webhook](#webhook-output) permite-lhe criar ou eliminar subscrições de webhook.

Os enlaces próprios não necessitam de quaisquer permissões do AAD, mas terá de solicitar permissões relevantes para o tipo de recurso que pretende reagir a. Para obter uma lista que são necessárias permissões para cada tipo de recurso, consulte [permissões de subscrição](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Para mais informações sobre webhooks, consulte [trabalhar com webhooks no Microsoft Graph].





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Acionador de webhook Microsoft Graph

Este acionador permite que uma função de reagir a um webhook de entrada da Microsoft Graph. Cada instância deste acionador possam reagir a um tipo de recurso do Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Configurar um acionador de webhook Microsoft Graph

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para a mensagem de correio. Consulte [através de uma mensagem do Outlook enlace a partir do código de saída](#outlook-output-code).|
|**tipo**|Necessário - tem de ser definido como `graphWebhook`.|
|**direção**|Necessário - tem de ser definido como `trigger`.|
|**resourceType**|Necessário - o recurso de gráfico para os quais esta função deve responder para webhooks. Pode ser um dos seguintes valores:<ul><li><code>#Microsoft.Graph.Message</code>-as alterações efetuadas às mensagens do Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>-as alterações efetuadas aos itens de raiz do OneDrive.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> Uma aplicação de função só pode ter uma função que está registada em relação a um determinado `resourceType` valor.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>Utilizar um acionador de webhook Microsoft Graph a partir do código

O enlace expõe os seguintes tipos de funções de .NET:
- Tipos de SDK do Microsoft Graph relevantes para o recurso de tipo, por exemplo, Microsoft.Graph.Message, Microsoft.Graph.DriveItem
- Tipos de objetos personalizados (utilizando o enlace do modelo estruturais)

Para exemplos de como utilizar este enlace no código, consulte [amostras de webhook Microsoft Graph](#webhook-samples).



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Enlace de entrada de subscrição do Microsoft Graph webhook

Este enlace permite-lhe obter a lista de subscrições geridos por esta aplicação de função. O enlace lê a partir do armazenamento de aplicação de função e não reflete outras subscrições criadas a partir de fora da aplicação.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Configurar um enlace de entrada de subscrição do Microsoft Graph webhook

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para a mensagem de correio. Consulte [através de uma mensagem do Outlook enlace a partir do código de saída](#outlook-output-code).|
|**tipo**|Necessário - tem de ser definido como `graphWebhookSubscription`.|
|**direção**|Necessário - tem de ser definido como `in`.|
|**filtro**| Se definido como `userFromRequest`, em seguida, o enlace só irá obter subscrições pertencentes ao utilizador chamado (apenas válida com [acionador HTTP]).| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>Utilizar um Microsoft Graph webhook subscrição enlace de entrada a partir do código

O enlace expõe os seguintes tipos de funções de .NET:
- String]
- Matrizes do tipo de objeto personalizado
- Newtonsoft.Json.Linq.JObject []
- Microsoft.Graph.Subscription []

Para exemplos de como utilizar este enlace no código, consulte [amostras de webhook Microsoft Graph](#webhook-samples).


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Subscrição do Microsoft Graph webhook vínculo de saída

Este enlace permite-lhe criar, eliminar e atualizar subscrições de webhook no Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Configurar um webhook de Microsoft Graph subscrição vínculo de saída

O enlace suporta as seguintes propriedades:

|Propriedade|Descrição|
|--------|--------|
|**nome**|Necessário - o nome da variável utilizado no código de função para a mensagem de correio. Consulte [através de uma mensagem do Outlook enlace a partir do código de saída](#outlook-output-code).|
|**tipo**|Necessário - tem de ser definido como `graphWebhookSubscription`.|
|**direção**|Necessário - tem de ser definido como `out`.|
|**identidade**|Necessário - a identidade que será utilizada para executar a ação. Pode ser um dos seguintes valores:<ul><li><code>userFromRequest</code>-Só é válido com [acionador HTTP]. Utiliza a identidade do utilizador chamada.</li><li><code>userFromId</code>-Utiliza a identidade de um utilizador com sessão iniciada no anteriormente com o ID especificado. Consulte o <code>userId</code> propriedade.</li><li><code>userFromToken</code>-Utiliza a identidade representada pelo token especificado. Consulte o <code>userToken</code> propriedade.</li><li><code>clientCredentials</code>-Utiliza a identidade da aplicação de função.</li></ul>|
|**ID de utilizador** |Se necessário e apenas se _identidade_ está definido como `userFromId`. ID principal de utilizador associado um utilizador anteriormente com sessão iniciada.|
|**userToken**|Se necessário e apenas se _identidade_ está definido como `userFromToken`. Um token válido para a aplicação de função. |
|**ação**|É necessário - Especifica a ação de enlace deve efetuar. Pode ser um dos seguintes valores:<ul><li><code>create</code>-Regista uma nova subscrição.</li><li><code>delete</code>-Elimina uma subscrição especificada.</li><li><code>refresh</code>-Atualiza uma subscrição especificada para mantê-los de expirar.</li></ul>|
|**subscriptionResource**|Se necessário e apenas se o _ação_ está definido como `create`. Especifica o recurso de Microsoft Graph que será monitorizado para as alterações. Consulte [trabalhar com webhooks no Microsoft Graph]. |
|**changeType**|Se necessário e apenas se o _ação_ está definido como `create`. Indica o tipo de alteração no recurso subscrito que irá emitir uma notificação. Os valores suportados são: `created`, `updated`, `deleted`. Podem ser combinados vários valores utilizando uma lista separada por vírgulas.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>Utilizar um webhook de Microsoft Graph subscrição vínculo de saída a partir do código

O enlace expõe os seguintes tipos de funções de .NET:
- Cadeia
- Microsoft.Graph.Subscription

Para exemplos de como utilizar este enlace no código, consulte [amostras de webhook Microsoft Graph](#webhook-samples).
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Exemplos de webhook Microsoft Graph

#### <a name="sample-creating-a-subscription"></a>Exemplo: Criar uma subscrição

Suponha que tem o seguinte function.json que define um acionador HTTP com uma saída de subscrição utilizando a ação de criação de enlace:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# regista um webhook que irá notificar esta aplicação de função quando o utilizador chamado recebe uma mensagem do Outlook:

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

O seguinte exemplo JS regista um webhook que irá notificar esta aplicação de função quando o utilizador chamado recebe uma mensagem do Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>Exemplo: Processamento de notificações

Suponha que tem o seguinte function.json que define o acionador de webhook gráfico para processar mensagens do Outlook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# reage a receber mensagens de correio e regista o corpo desses enviados pelo destinatário e que contêm "Das funções do Azure" no assunto:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

O seguinte exemplo JS reage a receber mensagens de correio e regista o corpo desses enviados pelo destinatário e que contêm "Das funções do Azure" no assunto:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### <a name="sample-deleting-subscriptions"></a>Exemplo: Eliminar subscrições

Suponha que tem o seguinte function.json que define um acionador HTTP com um enlace de entrada de subscrição e uma saída de subscrição utilizando a ação de eliminação de enlace:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# obtém todas as subscrições para o utilizador chamada e, em seguida, elimina-los:

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

O seguinte exemplo JS obtém todas as subscrições para o utilizador chamada e, em seguida, elimina-los:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### <a name="sample-refreshing-subscriptions"></a>Exemplo: Atualizar subscrições

Existem duas abordagens para atualizar as subscrições:
- Utilize a identidade da aplicação para lidar com todas as subscrições. Esta opção requer consentimento um administrador do Azure Active Directory. Isto pode ser utilizado por todos os idiomas suportados pelas funções do Azure.
- Utilizar a identidade associada a cada subscrição pelo enlace manualmente cada ID de utilizador. Isto irá exigir algum código personalizado para efetuar o enlace. Só pode ser utilizado pelas funções de .NET.

Ambas as opções são mostradas abaixo.

**Utilizar a identidade da aplicação**

Suponha que tem o seguinte function.json que define um acionador de temporizador com uma saída de subscrição de enlace de entrada de subscrição de enlace, utilizando a identidade da aplicação:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# atualiza as subscrições um temporizador, utilizando a identidade da aplicação:

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

O seguinte exemplo JS atualiza as subscrições um temporizador, utilizando a identidade da aplicação:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**Utilizar as identidades de utilizador dinâmica**

Para a opção alternativa, suponha que tem o seguinte function.json que define um acionador de temporizador, diferir o enlace para a subscrição de entrada do enlace para o código de função:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O exemplo seguinte c# atualiza as subscrições um temporizador, utilizando a identidade de cada utilizador ao criar o enlace de saída no código:
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[acionador HTTP]: functions-bindings-http-webhook.md
[trabalhar com webhooks no Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks

---
title: "Azure funções F # de referência para programadores | Microsoft Docs"
description: "Compreenda como desenvolver as funções do Azure utilizando o F #."
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
editor: 
tags: 
keywords: "Azure funções, funções, processamento, webhooks, computação dinâmica, arquitetura sem servidor, F # de eventos"
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: functions
ms.devlang: fsharp
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/09/2016
ms.author: syclebsc
ms.openlocfilehash: 314f528a1fcef2c7afb0eedba012023f3bc9502b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-f-developer-reference"></a>Referência F # para programadores das funções do Azure
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

O F # para as funções do Azure é uma solução para uma fácil execução pequenos blocos de código, ou "funções", na nuvem. Fluxos de dados para a sua função F # através de argumentos da função. Argumento nomes estão especificados na `function.json`, e existem nomes predefinidos para aceder ao coisas como os tokens de registo e o cancelamento da função.

Este artigo pressupõe que já leu o [referência para programadores de funções do Azure](functions-reference.md).

## <a name="how-fsx-works"></a>Como funciona o .fsx
Um `.fsx` ficheiro é um script F #. -Pode considerar como um projeto F # está contido num único ficheiro. O ficheiro contém tanto o código para o seu programa (neste caso, a sua função do Azure) e as diretivas para a gestão de dependências.

Quando utiliza um `.fsx` para uma função do Azure, geralmente necessário assemblagens são incluídas automaticamente para si, permitindo-lhe focar-se sobre o código de função em vez de "automático".

## <a name="binding-to-arguments"></a>A associação à argumentos
Cada enlace suporta algumas conjunto de argumentos, conforme detalhado no [referência de para programadores de acionadores e enlaces das funções do Azure](functions-triggers-bindings.md). Por exemplo, uma dos enlaces argumento que suporta um acionador de blob é um POCO, que pode ser expressas utilizando um registo F #. Por exemplo:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

A função de Azure F # irá demorar um ou mais argumentos. Quando abordadas argumentos de funções do Azure, vamos referir-se a *entrada* argumentos e *saída* argumentos. Um argumento de entrada é exatamente-SOA, como: de entrada para a função de Azure F #. Um *saída* argumento é mutável dados ou um `byref<>` argumento que funciona como uma forma para transmitir dados novamente *saída* da sua função.

No exemplo acima, `blob` um argumento de entrada, e `output` um argumento de saída. Tenha em atenção que utilizámos `byref<>` para `output` (não é necessário para adicionar o `[<Out>]` anotação). Utilizar um `byref<>` tipo permite que a função alterar o registo ou o objeto o argumento refere-se a.

Quando um registo F # é utilizado como um tipo de entrada, a definição de registo deve ser marcada com `[<CLIMutable>]` para permitir que a estrutura das funções do Azure definir os campos corretamente antes de passar o registo para a sua função. Os bastidores, `[<CLIMutable>]` gera setters para as propriedades de registo. Por exemplo:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Uma classe F # também pode ser utilizada para entrada e saída argumentos. Para uma classe, propriedades, normalmente, serão necessário getters e setters. Por exemplo:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registo
Iniciar a saída para a sua [registos de transmissão em fluxo](../app-service/web-sites-enable-diagnostic-log.md) no F #, a função deve ter um argumento de tipo `TraceWriter`. Para consistência, recomendamos que este argumento é denominado `log`. Por exemplo:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
O `async` pode ser utilizado o fluxo de trabalho, mas o resultado tem de devolver um `Task`. Isto pode ser feito com `Async.StartAsTask`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token de cancelamento
Se a função tem de lidar graciosamente com encerramento, que pode dar um [ `CancellationToken` ](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argumento. Isto pode ser combinado com `async`, por exemplo:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importação de espaços de nomes
Espaços de nomes podem ser abertos como habitualmente:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Os seguintes espaços de nomes estão abertos automaticamente:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referência a assemblagens externas
Da mesma forma, as referências de assemblagem do framework podem ser adicionadas com a `#r "AssemblyName"` diretiva.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

As assemblagens seguintes são adicionadas automaticamente as funções do Azure, ambiente de alojamento:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Além disso, as assemblagens seguintes são especiais cased e pode ser referenciada pelo simplename (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se precisar de fazer referência a uma assemblagem privada, pode carregar o ficheiro de assemblagem para um `bin` pasta relativo à sua função de referência e -lo utilizando o ficheiro (por exemplo, o nome  `#r "MyAssembly.dll"`). Para obter informações sobre como carregar ficheiros para a pasta de função, consulte a secção seguinte na gestão de pacotes.

## <a name="editor-prelude"></a>Editor de Prelude
Um editor que suporte serviços de compilador F # não saberão dos espaços de nomes e as assemblagens que inclui automaticamente as funções do Azure. Como tal, pode ser útil para incluir um prelude que ajuda a localizar as assemblagens que está a utilizar o editor de e para abrir explicitamente espaços de nomes. Por exemplo:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Quando as funções do Azure executa o código, processa a origem com `COMPILED` definido, pelo que o prelude editor será ignorada.

<a name="package"></a>

## <a name="package-management"></a>Gestão de pacotes
Para utilizar pacotes de NuGet numa função F #, adicione um `project.json` do ficheiro para a pasta da função no sistema de ficheiros da aplicação de função. Eis um exemplo `project.json` ficheiro que adiciona uma referência de pacote NuGet para `Microsoft.ProjectOxford.Face` versão 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Apenas o .NET Framework 4.6 é suportada, por isso, certifique-se de que o `project.json` ficheiro Especifica `net46` conforme mostrado aqui.

Quando carregar um `project.json` obtém os pacotes de ficheiros, o tempo de execução e adiciona automaticamente referências para as assemblagens de pacote. Não tem de adicionar `#r "AssemblyName"` diretivas. Basta adicionar necessários `open` instruções para sua `.fsx` ficheiro.

Pode pretender colocar assemblagens de referências de automaticamente no seu prelude editor, para melhorar a interação do seu editor com os serviços de compilação do F #.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Como adicionar um `project.json` ficheiro para a função do Azure
1. Comece por certificar-se de que a aplicação de função está em execução, que pode fazer, abrindo a função no portal do Azure. Isto também proporciona acesso para os registos de transmissão em fluxo em que será apresentada saída de instalação do pacote.
2. Para carregar um `project.json` de ficheiros, utilize um dos métodos descritos em [como atualizar os ficheiros de aplicação de função](functions-reference.md#fileupdate). Se estiver a utilizar [a implementação contínua para as funções do Azure](functions-continuous-deployment.md), pode adicionar um `project.json` ficheiro para o ramo de transição para experimentar antes de adicioná-lo para o ramo de implementação.
3. Depois do `project.json` ficheiro seja adicionado, irá ver o resultado semelhante ao exemplo seguinte na sua função de transmissão em fluxo de registo:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variáveis de ambiente
Para obter uma variável de ambiente ou uma valor de definição de aplicação, utilize `System.Environment.GetEnvironmentVariable`, por exemplo:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Reutilizar o código de .fsx
Pode utilizar o código de outros `.fsx` ficheiros utilizando um `#load` diretiva. Por exemplo:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Caminhos fornece para o `#load` tem directiva relativo à localização de sua `.fsx` ficheiro.

* `#load "logger.fsx"`carrega um ficheiro localizado na pasta de função.
* `#load "package\logger.fsx"`carrega um ficheiro localizado no `package` a pasta de função.
* `#load "..\shared\mylogger.fsx"`carrega um ficheiro localizado no `shared` pasta com o mesmo nível, como a pasta de função, ou seja, diretamente em `wwwroot`.

O `#load` diretiva só funciona com `.fsx` ficheiros (F # script) e não com `.fs` ficheiros.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos:

* [O F # guia](/dotnet/articles/fsharp/index)
* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Acionadores de funções do Azure e os enlaces](functions-triggers-bindings.md)
* [Funções do Azure de teste](functions-test-a-function.md)
* [Dimensionamento das funções do Azure](functions-scale.md)


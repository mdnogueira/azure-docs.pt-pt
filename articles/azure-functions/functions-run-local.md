---
title: "Desenvolver e executar as funções do Azure localmente | Microsoft Docs"
description: "Saiba como code e testar as funções do Azure no seu computador local antes de executar em funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 31142fa2ab06b7479aa93f9ece14f4dd51fa28ef
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="code-and-test-azure-functions-locally"></a>Código e teste das funções do Azure localmente

Enquanto o [portal do Azure] fornece um completo conjunto de ferramentas para o desenvolvimento e teste das funções do Azure, os programadores muitos preferir uma experiência de desenvolvimento local. As funções do Azure torna mais fácil de utilizar o seu editor favorito de código e ferramentas de desenvolvimento local para desenvolver e testar as suas funções no seu computador local. As suas funções podem acionar eventos no Azure e pode depurar o c# e JavaScript funções no seu computador local. 

Se for um programador Visual Studio c#, as funções do Azure também [integra-se com o Visual Studio 2017](functions-develop-vs.md).

>[!IMPORTANT]  
> Não misture desenvolvimento local com o desenvolvimento portal na mesma aplicação de função. Quando criar e publicar as funções de um projeto local, não deve tentar manter ou modificar o código de projeto no portal.

## <a name="install-the-azure-functions-core-tools"></a>Instalar as ferramentas de núcleos de funções do Azure

[Ferramentas de núcleos de funções do Azure] é uma versão local do tempo de execução das funções do Azure que pode executar no seu computador de desenvolvimento local. Não é um emulador ou simulador. É o mesmo runtime powers funciona no Azure. Existem duas versões do Azure funções principais ferramentas, uma versão 1. x de tempo de execução e outra para a versão 2. x. Ambas as versões são fornecidas como um [pacote npm](https://docs.npmjs.com/getting-started/what-is-npm).

>[!NOTE]  
> Antes de instalar uma das versões, tem [instalar NodeJS](https://docs.npmjs.com/getting-started/installing-node), que inclui npm. Para a versão 2. x das ferramentas, apenas Node.js 8.5 e versões posteriores são suportados. 

### <a name="version-1x-runtime"></a>Tempo de execução do versão 1. x

A versão das ferramentas original utiliza o tempo de execução de 1. x de funções. Esta versão utiliza o .NET Framework e só é suportada em computadores Windows. Utilize o seguinte comando para instalar as ferramentas de 1. x da versão:

```bash
npm install -g azure-functions-core-tools
```

### <a name="version-2x-runtime"></a>Tempo de execução do versão 2. x

Versão 2. x das ferramentas utiliza o tempo de execução das funções do Azure 2 que está incorporada no .NET Core. Esta versão é suportada em todas as plataformas .NET Core suporta 2. x. Utilizar esta versão para o desenvolvimento de plataforma e quando o tempo de execução de funções 2 é necessário. 

>[!IMPORTANT]   
> Antes de instalar ferramentas de núcleos de funções do Azure, [instale o .NET Core 2.0](https://www.microsoft.com/net/core).  
>
> Tempo de execução funções do Azure 2.0 está em pré-visualização e, atualmente nem todas as funcionalidades das funções do Azure são suportadas. Para obter mais informações, consulte [problemas de tempo de execução 2.0 conhecido das funções do Azure](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues) 

 Utilize o seguinte comando para instalar as ferramentas de versão 2.0:

```bash
npm install -g azure-functions-core-tools@core
```

Ao instalar na utilização de Ubuntu `sudo`, da seguinte forma:

```bash
sudo npm install -g azure-functions-core-tools@core
```

Ao instalar no macOS e Linux, poderá ter de incluir o `unsafe-perm` sinalizador, da seguinte forma:

```bash
sudo npm install -g azure-functions-core-tools@core --unsafe-perm true
```

## <a name="run-azure-functions-core-tools"></a>Executar as ferramentas de núcleos de funções do Azure
 
Ferramentas de núcleos de funções do Azure adiciona os aliases de comando seguinte:
* **Func**
* **azfun**
* **azurefunctions**

Qualquer uma destes aliases podem ser utilizadas onde `func` é ilustrado nos exemplos.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Ao executar localmente, um projeto de funções é um diretório com os ficheiros [host.json](functions-host-json.md) e [local.settings.json](#local-settings). Este diretório é o equivalente a uma aplicação de função no Azure. Para saber mais sobre a estrutura de pastas de funções do Azure, consulte o [guia de programadores das funções do Azure](functions-reference.md#folder-structure).

Na janela de terminal ou numa linha de comandos, execute o seguinte comando para criar o projeto e o repositório de Git local:

```
func init MyFunctionProj
```

Procura o resultado semelhante ao seguinte exemplo:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Para criar o projeto sem um repositório de Git local, utilize o `--no-source-control [-n]` opção.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>Ficheiro de definições do local

O ficheiro local.settings.json armazena as definições de aplicação, cadeias de ligação e definições de ferramentas de núcleos de funções do Azure. Tem a estrutura seguinte:

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Definição      | Descrição                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Quando definido como **verdadeiro**, todos os valores são encriptados utilizando uma chave de computador local. Utilizado com `func settings` comandos. Valor predefinido é **falso**. |
| **Valores** | Coleção de definições da aplicação utilizada ao executar localmente. **AzureWebJobsStorage** e **AzureWebJobsDashboard** são exemplos; para obter uma lista completa, consulte [referência de definições de aplicação](functions-app-settings.md).  |
| **Anfitrião** | As definições nesta secção personalizar o processo de anfitrião de funções ao executar localmente. | 
| **LocalHttpPort** | Define a porta predefinida utilizada ao executar o anfitrião de funções local (`func host start` e `func run`). O `--port` opção da linha de comandos tem precedência sobre este valor. |
| **CORS** | Define as origens permitidas para [recursos de várias origens (CORS) de partilha](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Origens são fornecidas como uma lista de valores separados por vírgulas de mensagens em fila sem espaços. O valor de caráter universal (**\***) é suportada, que permite que os pedidos de qualquer origem. |
| **ConnectionStrings** | Contém as cadeias de ligação de base de dados para as suas funções. Cadeias de ligação neste objeto são adicionadas para o ambiente com o tipo de fornecedor de **SqlClient**.  | 

A maioria dos acionadores e enlaces têm um **ligação** propriedade mapeia para o nome de uma definição de aplicação ou variável de ambiente. Para cada propriedade de ligação, deve ser definida no ficheiro local.settings.json de definição de aplicação. 

Estas definições também podem ser lidos no seu código como variáveis de ambiente. Em c#, utilize [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) ou [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). Em JavaScript, utilize `process.env`. As definições especificadas como uma variável de ambiente de sistema têm precedência sobre valores no ficheiro local.settings.json. 

As definições no ficheiro local.settings.json apenas são utilizadas pelas ferramentas de funções ao executar localmente. Por predefinição, estas definições não são migradas automaticamente quando o projeto é publicado para o Azure. Utilize o `--publish-local-settings` comutador [quando publica](#publish) para se certificar de que estas definições são adicionadas para a aplicação de função no Azure.

Quando nenhuma cadeia de ligação de armazenamento válido está definida para **AzureWebJobsStorage**, é apresentada a seguinte mensagem de erro:  

>Falta o valor para AzureWebJobsStorage no local.settings.json. Isto é necessário para todos os acionadores diferentes de HTTP. Pode executar ' func azure functionapp obtenção--as definições de aplicação <functionAppName>' ou especifique uma cadeia de ligação na local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Configurar as definições da aplicação

Para definir um valor para cadeias de ligação, pode efetuar uma das seguintes opções:
* Introduza a cadeia de ligação de [Explorador de armazenamento do Azure](http://storageexplorer.com/).
* Utilize um dos seguintes comandos:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Ambos os comandos exigem que o primeiro início de sessão para o Azure.

<a name="create-func"></a>
## <a name="create-a-function"></a>Criar uma função

Para criar uma função, execute o seguinte comando:

```
func new
``` 
`func new`suporta os seguintes argumentos opcionais:

| Argumento     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | O modelo de programação idioma, como c#, F # ou JavaScript. |
| **`--template -t`** | O nome do modelo. |
| **`--name -n`** | O nome da função. |

Por exemplo, para criar um acionador de HTTP de JavaScript, execute:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Para criar uma função acionada pela fila, execute:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>Executar funções localmente

Para executar um projeto de funções, execute o anfitrião de funções. O anfitrião permite acionadores para todas as funções no projeto:

```
func host start
```

`func host start`suporta as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | A porta local a escutar. Valor predefinido: 7071. |
| **`--debug <type>`** | As opções são `VSCode` e `VS`. |
| **`--cors`** | Uma lista separada por vírgulas de origens CORS, sem espaços. |
| **`--nodeDebugPort -n`** | A porta para o depurador do nó utilizar. : Um valor predefinido De launch.json ou 5858. |
| **`--debugLevel -d`** | O nível de rastreio de consola (desativado, verboso, info, warning ou error). Predefinição: as informações.|
| **`--timeout -t`** | O tempo limite para o anfitrião de funções iniciar, em segundos. Predefinição: 20 segundos.|
| **`--useHttps`** | Vincular ao https://localhost: {porta}, em vez de para http://localhost: {porta}. Por predefinição, esta opção cria um certificado fidedigno no computador.|
| **`--pause-on-error`** | Colocar em pausa para a entrada adicional antes de sair do processo. Útil quando iniciar ferramentas de núcleos de funções do Azure a partir de um ambiente de desenvolvimento integrado (IDE).|

Quando o anfitrião de funções é iniciado, produz as funções acionadas por URL de HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Depurar no VS Code ou no Visual Studio

Para anexar um depurador, transmitir o `--debug` argumento. Para depurar funções JavaScript, utilize o Visual Studio Code. C# funciona, para utilizar o Visual Studio.

Para depurar as funções de c#, utilize `--debug vs`. Também pode utilizar [Azure funções Visual Studio 2017 ferramentas](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Para iniciar o anfitrião e configurar a depuração de JavaScript, execute:

```
func host start --debug vscode
```

Em seguida, no Visual Studio Code, no **depurar** visualizar, selecione **anexar para as funções do Azure**. Pode ligar a pontos de interrupção, Inspecione variáveis e siga os passos código.

![JavaScript depuração com o Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Transmissão de dados de teste para uma função

Para testar as suas funções localmente, [iniciar o anfitrião de funções](#start) e chame pontos finais no servidor local através de pedidos de HTTP. O ponto final que tem de chamar depende da função. 

>[!NOTE]  
> Os exemplos neste tópico utilizam a ferramenta de cURL para enviar pedidos de HTTP de terminal ou uma linha de comandos. Pode utilizar uma ferramenta de sua escolha para enviar pedidos HTTP para o servidor local. A ferramenta de cURL está disponível por predefinição em sistemas baseados em Linux. No Windows, tem primeiro de transferir e instalar o [cURL ferramenta](https://curl.haxx.se/).

Para obter informações mais gerais sobre testar as funções, consulte [estratégias para testar o seu código das funções do Azure](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>HTTP e webhook acionada funções

Chamar o seguinte ponto final para executar localmente HTTP e webhook acionada funções:

    http://localhost:{port}/api/{function_name}

Certifique-se utilizar o mesmo nome de servidor e a porta que o anfitrião de funções está a escutar. Pode ver na saída gerada ao iniciar o anfitrião de função. Pode ligar a este URL com qualquer método HTTP suportado pelo acionador. 

O seguinte cURL acionadores de comando de `MyHttpTrigger` função de início rápido de um pedido GET com o _nome_ parâmetro transmitido na cadeia de consulta. 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
O exemplo seguinte é a mesma função chamada a partir de um pedido POST transmitir _nome_ no corpo do pedido:

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Tenha em atenção que pode efetuar pedidos de obtenção de um browser passar dados na cadeia de consulta. Para todos os outros métodos HTTP, tem de utilizar cURL, Fiddler, Postman ou uma ferramenta de teste HTTP semelhante.  

#### <a name="non-http-triggered-functions"></a>Não HTTP acionada funções
Para todos os tipos de funções que não sejam acionadores HTTP e webhooks, pode testar as suas funções localmente ao chamar um ponto final de administração. Este ponto final com um pedido POST de HTTP ao chamar no servidor local aciona a função. Opcionalmente, pode passar dados de teste para a execução no corpo do pedido POST. Esta funcionalidade é semelhante para o **teste** separador no portal do Azure.  

Chamar o seguinte ponto final do administrador para acionar não HTTP funções:

    http://localhost:{port}/admin/functions/{function_name}

Para transmitir dados de teste para o ponto final de administrador de uma função, tem de fornecer os dados no corpo de uma mensagem de pedido POST. O corpo da mensagem é necessário ter o seguinte formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```` 
O `<trigger_input>` valor contém dados num formato esperado pela função. O seguinte exemplo de cURL é um pedido POST para um `QueueTriggerJS` função. Neste caso, a entrada é uma cadeia que é equivalente à mensagem deve ser encontrada na fila.      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Utilizar o `func run` comando versão 1. x

>[!IMPORTANT]  
> O `func run` comando não é suportado na versão 2. x das ferramentas. Para obter mais informações, consulte o tópico [como destino a versões de tempo de execução das funções do Azure](functions-versions.md).

Pode também invocar uma função utilizando diretamente `func run <FunctionName>` e fornecer dados de entrada para a função. Este comando é semelhante à execução de uma função utilizando o **teste** separador no portal do Azure. 

`func run`suporta as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Conteúdo de inline. |
| **`--debug -d`** | Anexe um depurador para o processo de anfitrião antes de executar a função.|
| **`--timeout -t`** | Tempo de espera (em segundos) até que o anfitrião de funções local está pronto.|
| **`--file -f`** | O nome de ficheiro a utilizar como o conteúdo.|
| **`--no-interactive`** | Não solicita de entrada. É útil para cenários de automatização.|

Por exemplo, para chamar uma função acionada por HTTP e passar o corpo de conteúdo, execute o seguinte comando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publicar no Azure

Para publicar um projeto de funções para uma aplicação de função no Azure, utilize o `publish` comando:

```
func azure functionapp publish <FunctionAppName>
```

Pode utilizar as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Definições de publicação local.settings.json para o Azure, que lhe pede para substituir se a definição já existe.|
| **`--overwrite-settings -y`** | Tem de ser utilizado com `-i`. Substitui AppSettings no Azure com o valor local, se for diferente. Predefinição é de linha de comandos.|

Este comando publica uma aplicação de função existente no Azure. Ocorre um erro quando o `<FunctionAppName>` não existe na sua subscrição. Para saber como criar uma aplicação de função a partir da linha de comandos ou janela de terminal utilizando a CLI do Azure, consulte [criar uma aplicação de função para execução sem servidor](./scripts/functions-cli-create-serverless.md).

O `publish` comando carrega o conteúdo do diretório do projeto de funções. Se eliminar os ficheiros localmente, a `publish` comando não eliminá-los a partir do Azure. Pode eliminar ficheiros no Azure utilizando o [ferramenta Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) no [portal do Azure].  

>[!IMPORTANT]  
> Quando cria uma aplicação de função no Azure, este utilizará a versão 1. x do tempo de execução de função, por predefinição. Para tornar a versão de utilização da aplicação de função 2 do tempo de execução, adicione a definição de aplicação `FUNCTIONS_EXTENSION_VERSION=beta`.  
Utilize o seguinte código para a CLI do Azure para adicionar esta definição para a sua aplicação de função: 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Passos seguintes

Ferramentas de núcleos de funções do Azure é [abrir a origem e está lojado no GitHub](https://github.com/azure/azure-functions-cli).  
No ficheiro de um pedido de erros ou funcionalidade, [abrir um problema no GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Ferramentas de núcleos de funções do Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[portal do Azure]: https://portal.azure.com 

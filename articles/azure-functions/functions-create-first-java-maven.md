---
title: "Crie a sua primeira função no Azure com Java e Maven | Microsoft Docs"
description: "Criar e publicar uma função de acionada de HTTP simple no Azure com Java e Maven."
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: "funções do azure, funções, processamento de eventos, computação, arquitetura sem servidor"
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 50fd59c288312c7aa5ffe6abf1318a5ec2f406e6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Criar a sua primeira função com o Java e Maven (pré-visualização)

Este guia de introdução orienta ao longo da criação de um [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) projeto de função com o Maven, testá-lo localmente e implementar a política para as funções do Azure. Quando tiver terminado, terá uma aplicação de função de acionada por HTTP em execução no Azure.

 ![Aceder a uma função de Olá, mundo na linha de comandos com cURL](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para desenvolver aplicações funções com o Java, tem de ter o seguinte instalado:

-  [.NET core](https://www.microsoft.com/net/core), versão mais recente.
-  [Java Development Kit](https://www.azul.com/downloads/zulu/), versão 1.8.
-  [CLI do Azure](https://docs.microsoft.com/cli/azure)
-  [Apache Maven](https://maven.apache.org), versão 3.0 ou superior.
-  [NODE.js](https://nodejs.org/download/), versão 8.6 ou superior.

> [!IMPORTANT] 
> A variável de ambiente de JAVA_HOME tem de ser definida para a localização de instalação do JDK para concluir este guia de introdução.

## <a name="install-the-azure-functions-core-tools"></a>Instalar as ferramentas de núcleos de funções do Azure

O [Azure funções principais ferramentas 2.0](https://www.npmjs.com/package/azure-functions-core-tools) fornecer um ambiente de desenvolvimento local para escrever, executar e depuração das funções do Azure. Instalar as ferramentas com [npm](https://www.npmjs.com/), incluído com [Node.js](https://nodejs.org/).

```
npm install -g azure-functions-core-tools@core
```

> [!NOTE]
> Se tiver dificuldades em instalar ferramentas de núcleos de funções do Azure versão 2.0, consulte [versão 2. x runtime](/azure/azure-functions/functions-run-local#version-2x-runtime).

## <a name="generate-a-new-functions-project"></a>Gerar um novo projeto de funções

Numa pasta vazia, execute o seguinte comando para gerar o projeto de funções de um [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven pede-lhe os valores necessários para concluir a gerar o projeto. Para _groupId_, _artifactId_, e _versão_ valores, consulte o [convenções de nomenclatura Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html) referência. O _appName_ valor tem de ser exclusivo em todo o Azure, para que o Maven gera um nome de aplicação com base no anteriormente introduzido _artifactId_ por predefinição. O _packageName_ valor determina o pacote de Java para o código de função gerado.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven cria os ficheiros de projeto de uma nova pasta com um nome de _artifactId_. O código gerado no projeto é uma simples [HTTP acionada](/azure/azure-functions/functions-bindings-http-webhook) função echoes o corpo do pedido:

```java
public class Function {
    @FunctionName("hello")
    public String hello(@HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req,
                        ExecutionContext context) {
        return String.format("Hello, %s!", req);
    }
}
```

## <a name="run-the-function-locally"></a>Executar a função localmente

Altere o diretório para a pasta do projeto recém-criado e compilar e executar a função com o Maven:

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

Consulte este resultado quando a função está em execução:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Acione a função na linha de comandos utilizando curl num novo terminal:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

Utilize `Ctrl-C` no terminal para parar o código de função.

## <a name="deploy-the-function-to-azure"></a>Implementar a função no Azure

O processo de implementar para as funções do Azure utiliza credenciais de conta a partir da CLI do Azure. [Iniciar sessão com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) e, em seguida, implementar o código para uma nova função aplicação utilizando o `azure-functions:deploy` destino Maven.

```
az login
mvn azure-functions:deploy
```

Quando o implementar estiver concluída, consulte o URL que pode utilizar para aceder à sua aplicação de função do Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Teste a aplicação de função em execução no Azure utilizando curl:

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>Passos seguintes

Criou uma aplicação de função de Java com um acionador HTTP simple e implementado para as funções do Azure.

- Reveja o [guia para programadores do Java funções](functions-reference-java.md) para obter mais informações sobre o desenvolvimento das funções de Java.
- Adicionar funções adicionais com diferentes acionadores ao seu projeto com o `azure-functions:add` destino Maven.
- As funções localmente com o Visual Studio Code de depuração. Com o [pacote de extensão de Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) instalado e com o projeto de funções abrir no Visual Studio Code, [anexar o depurador](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations) à porta 5005. Em seguida, defina um ponto de interrupção no editor e acionar a função enquanto está a ser executado localmente: ![depurar funções no Visual Studio Code](media/functions-create-java-maven/vscode-debug.png)




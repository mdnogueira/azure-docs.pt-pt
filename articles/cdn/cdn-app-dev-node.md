---
title: "Introdução ao SDK da CDN do Azure para Node.js | Microsoft Docs"
description: "Saiba mais sobre como escrever aplicações Node.js para gerir a CDN do Azure."
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 46ae8cd9775432d126cbde856c1fb06ea319297e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-cdn-development"></a>Introdução à programação do CDN do Azure
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Pode utilizar o [Azure CDN SDK para Node.js](https://www.npmjs.com/package/azure-arm-cdn) para automatizar a criação e gestão de perfis da CDN e os pontos finais.  Este tutorial explica a criação de uma aplicação de consola do Node.js simples que demonstra várias as operações disponíveis.  Este tutorial destina-se não descrever todos os aspetos do Azure CDN SDK para Node.js em detalhe.

Para concluir este tutorial, já deverá ter [Node.js](http://www.nodejs.org) **4.x.x** ou superior instalado e configurado.  Pode utilizar qualquer editor de texto que pretende criar a sua aplicação Node.js.  Para escrever este tutorial, utilizei [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> O [projeto conclusão deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) está disponível para transferência no MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Criar o projeto e adicione as dependências NPM
Agora que tiver criado um grupo de recursos para o nosso perfis da CDN e obtém nosso permissão de aplicação do Azure AD para gerir perfis da CDN e os pontos finais dentro desse grupo, podemos começar a criar a nossa aplicação.

Crie uma pasta para armazenar a sua aplicação.  A partir de uma consola com as ferramentas de Node.js no seu caminho atual, definir a sua localização atual para esta nova pasta e inicializar o projeto executando:

    npm init

Em seguida, será apresentada uma série de perguntas para inicializar o seu projeto.  Para **ponto de entrada**, este tutorial utiliza *app.js*.  Pode ver as minhas outras opções no exemplo seguinte.

![Saída de init NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Nosso projeto agora é inicializado com um *packages.json* ficheiro.  Nosso projeto vai utilizar algumas bibliotecas do Azure contidas nos pacotes NPM.  Utilizaremos o tempo de execução do cliente do Azure para Node.js (ms-rest-azure) e a biblioteca de clientes de CDN do Azure para Node.js (azure-arm-cd).  Vamos adicionar os para o projeto como dependências.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Depois de terminado os pacotes de instalação, o *Package. JSON* ficheiro deve ter um aspeto semelhante para este exemplo (versão números podem divergir):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Por fim, utilizando o editor de texto, crie um ficheiro de texto em branco e guardá-lo na raiz da nossa pasta do projeto como *app.js*.  Iremos agora está prontos começar a escrever código.

## <a name="requires-constants-authentication-and-structure"></a>Requer, constantes, autenticação e estrutura
Com *app.js* abrir no nosso editor, vamos abordar a estrutura básica do nosso programa escrito.

1. Adicione o "requer" para os nossos pacotes NPM na parte superior com o seguinte:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. É necessário definir algumas constantes que irão utilizar o nosso métodos.  Adicione o seguinte.  Não se esqueça de substituir os marcadores de posição, incluindo o  **&lt;angulares&gt;**, com os seus próprios valores conforme necessário.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Em seguida, vamos instanciar o cliente de gestão da CDN e atribua-lhe nosso credenciais.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Se estiver a utilizar a autenticação de utilizador individuais, estas duas linhas terá um aspeto ligeiramente diferentes.
   
   > [!IMPORTANT]
   > Utilize apenas código de exemplo, se escolher a autenticação de utilizador individuais em vez de um principal de serviço.  Tenha o cuidado de proteger as suas credenciais de utilizador individuais e mantê-las secreta.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Não se esqueça de substituir os itens no  **&lt;angulares&gt;**  com as informações corretas.  Para `<redirect URI>`, utilize o URI que introduziu quando se registou a aplicação no Azure AD de redirecionamento.
4. A nossa aplicação de consola do Node.js vai demorar alguns parâmetros da linha de comandos.  Vamos validar que, pelo menos, um parâmetro transmitido.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. -Nos que traz para a parte principal do nosso programa, onde iremos sucursal outras funções com base no que parâmetros foram transmitidos.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. Em vários locais no nosso programa, temos de certificar-se de que o número correto de parâmetros foram transmitido e apresenta a ajuda se não aparecerem corretos.  Vamos criar funções para o fazer.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Por fim, as funções que iremos utilizar o cliente de gestão de CDN são assíncronas, pelo que precisam de um método para chamar novamente quando estes terminado.  Certifiquemo-de que pode apresentar os resultados do cliente de gestão CDN (se aplicável) e sair do programa de transições.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Agora que a estrutura básica do nosso programa é escrita, deve criamos as funções de chamada com base nos nossos parâmetros.

## <a name="list-cdn-profiles-and-endpoints"></a>Perfis da CDN lista e os pontos finais
Vamos começar com o código para listar os nossos perfis existentes e pontos finais.  Os meus comentários de código fornecem a sintaxe esperada para que possamos saber em que cada parâmetro passa.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis da CDN e os pontos finais
Em seguida, iremos irá escrever as funções para criar perfis e os pontos finais.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Remover um ponto final
Pressupondo que foi criado o ponto final, uma tarefa comuns poderá querer efetuar no nosso programa é remoção de conteúdo no nosso ponto final.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar perfis da CDN e os pontos finais
A última função que será incluímos elimina pontos finais e perfis.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Executar o programa
Agora podemos executar programa nosso Node.js utilizando a nossa depurador favorito ou na consola.

> [!TIP]
> Se estiver a utilizar o Visual Studio Code como o depurador, terá de configurar o ambiente para passar os parâmetros da linha de comandos.  Visual Studio Code efetua este procedimento **lanuch.json** ficheiro.  Procurar uma propriedade chamada **args** e adicionar uma matriz de valores de cadeia para os parâmetros, de modo a que tenha um aspeto semelhante a isto: `"args": ["list", "profiles"]`.
> 
> 

Vamos começar por listar nosso perfis.

![Lista de perfis](./media/cdn-app-dev-node/cdn-list-profiles.png)

Obtivemos novamente uma matriz vazia.  Uma vez que não temos quaisquer perfis no nosso grupo de recursos, que é esperado.  Agora, Criemos um perfil.

![Criar perfil](./media/cdn-app-dev-node/cdn-create-profile.png)

Agora, vamos adicionar um ponto final.

![Criar o ponto final](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Por fim, vamos eliminar nosso perfil.

![Eliminar perfil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Passos Seguintes
Para ver o projeto de conclusão nestas instruções, [transferir o exemplo](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Para ver a referência para o CDN do Azure SDK para Node.js, ver o [referência](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Para obter documentação adicional sobre o Azure SDK para Node.js, ver o [completa referência](http://azure.github.io/azure-sdk-for-node/).

Gerir os recursos CDN com [PowerShell](cdn-manage-powershell.md).


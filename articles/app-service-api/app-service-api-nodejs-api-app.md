---
title: "Aplicação API Node.js no Serviço de Aplicações do Azure | Microsoft Docs"
description: "Saiba como criar uma API RESTful Node.js e implementá-la numa aplicação API no App Service do Azure."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 8a5d0c60e101f4038dff6f76c8f23dbb2b44661c
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017


---
<a id="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure" class="xliff"></a>

# Criar uma API RESTful Node.js e implementá-la numa aplicação API no Azure
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este guia de início rápido mostra como criar um [Express](http://expressjs.com/)framework Node.js REST API a partir de uma definição de [Swagger](http://swagger.io/) e implementá-lo como uma [aplicação de API](app-service-api-apps-why-best-platform.md) no Azure. Crie a aplicação através das ferramentas de linha de comando, configure os recursos com a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)e implemente a aplicação com o Git.  Quando tiver terminado, terá uma API de REST de amostra de trabalho em execução no Azure.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

* [Git](https://git-scm.com/)
* [ Node.js e NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

<a id="prepare-your-environment" class="xliff"></a>

## Preparar o ambiente

1. Numa janela do terminal, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. Mude para o diretório que contém o código de exemplo.

    ```bash
    cd app-service-api-node-contact-list
    ```

3. Instalar [Swaggerize](https://www.npmjs.com/package/swaggerize-express) no seu computador local. Swaggerize é uma ferramenta que gera código Node. js para a API REST de uma definição de Swagger.

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

<a id="generate-nodejs-code" class="xliff"></a>

## Gerar código Node. js 

Esta secção do tutorial exemplifica o fluxo de trabalho de desenvolvimento de uma API na qual cria primeiro metadados Swagger e utiliza para estruturar (gerar automaticamente) o código do servidor para a API. 

Altere o diretório para a pasta *iniciar*, em seguida, execute `yo swaggerize`. Swaggerize cria um projeto de Node. js para sua API da definição de Swagger na *api.json*.

    ```bash
    cd start
    yo swaggerize --apiPath api.json --framework express
    ```

     When Swaggerize asks for a project name, use *ContactList*.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
<a id="customize-the-project-code" class="xliff"></a>

## Personalizar o código do projeto

1. Copie a pasta *lib*para a pasta *ContactList* criada por `yo swaggerize` e mude para o diretório *ContactList*.

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. Instalar os módulos `jsonpath` e `swaggerize-ui` NPM. 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. Substitua o código no ficheiro *handlers/contacts.js* pelo código seguinte. 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    Este código utiliza os dados JSON armazenados no ficheiro *lib/contacts.json* que é servido pelo *lib/contactRepository.js*. O novo código *contacts.js* devolve todos os contactos no repositório como uma carga JSON. 

4. Substitua o código no ficheiro **handlers/contacts/{id}.js** pelo código seguinte:

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    Esse código permite que use uma variável de caminho para devolver apenas o contacto com uma ID especificada.

5. Substitua o código no **server.js** pelo código seguinte:

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    Este código faz algumas pequenas alterações para deixá-lo a trabalhar com o serviço de aplicações do Azure e expõe uma interface web interativa para sua API.

<a id="test-the-api-locally" class="xliff"></a>

### Testar a API localmente

1. Iniciar a aplicação de Node.js
    ```bash
    npm start
    ```
    
2. Navegue até http://localhost:8000/contacts para exibir o JSON para toda a lista de contactos.
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. Navegue até http://localhost:8000/contacts/2 para exibir o contacto com um `id` de dois.
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. Teste a API com a interface da web de Swagger em interface at http://localhost:8000/docs.
   
    ![Interface da web swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> Criar uma aplicação de API

Nesta secção, pode utilizar a CLI 2.0 do Azure para criar os recursos para alojar a API do Azure App Service. 

1.  Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/#login) e siga as instruções no ecrã.

    ```azurecli-interactive
    az login
    ```

2. Se tiver várias subscrições do Azure, altere a subscrição predefinida para a desejada.

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


<a id="deploy-the-api-with-git" class="xliff"></a>

## Implementar a API com Git

Irá implementar o código para a aplicação API ao emitir consolidações para um repositório do Git no Azure App Service.

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. Inicializar um novo repositório no diretório *ContactList*. 

    ```bash
    git init .
    ```

3. Exclua o diretório *node_modules* criado pelo npm numa etapa anterior no tutorial do Git. Crie um novo ficheiro `.gitignore` no diretório atual e adicione o seguinte texto numa nova linha em qualquer lugar no ficheiro.

    ```
    node_modules/
    ```
    Confirme se a pasta `node_modules` está a ser ignorada com `git status`.

4. Confirme as alterações ao repositório.
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
<a id="test-the-api--in-azure" class="xliff"></a>

## Testar a API no Azure

1. Abra um browser em http://app_name.azurewebsites.net/contacts. Verá o mesmo JSON devolvido como quando fez a solicitação localmente no tutorial.

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. Num browser, aceda ao ponto final `http://app_name.azurewebsites.net/docs` para experimentar a IU do Swagger enquanto é executada no Azure.

    ![Ii do Swagger](media/app-service-api-nodejs-api-app/swagger-azure-ui.png)

    Agora pode implementar atualizações para a API de exemplo para o Azure simplesmente enviando confirmações para o repositório Git do Azure.

<a id="clean-up" class="xliff"></a>

## Limpeza

Para remover todos os recursos criados por este Manual de Início Rápido, execute o seguinte comando da CLI do Azure:

```azurecli-interactive
az group delete --name myResourceGroup
```

<a id="next-step" class="xliff"></a>

## Passo seguinte 
> [!div class="nextstepaction"]
> [Consumir aplicalções de API de clientes JavaScript com CORS](app-service-api-cors-consume-javascript.md)



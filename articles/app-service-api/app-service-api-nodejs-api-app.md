---
title: "Aplicação API Node.js no Serviço de Aplicações do Azure | Microsoft Docs"
description: "Saiba como criar uma API RESTful Node.js e implementá-la numa aplicação API no App Service do Azure."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: wpickett
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: get-started-article
ms.date: 05/26/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e8a1ac3df5225fdcfe2717c2cf50bfc5b7cfda36


---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Criar uma API RESTful Node.js e implementá-la numa aplicação API no Azure
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial mostra como criar uma simples API [Node.js](http://nodejs.org) e implementá-la numa [aplicação API](app-service-api-apps-why-best-platform.md) no [App Service do Azure](../app-service/app-service-value-prop-what-is.md) utilizando o [Git](http://git-scm.com). Pode utilizar qualquer sistema operativo que execute Node.js e irá fazer todo o seu trabalho através de ferramentas de linha de comandos como cmd.exe ou bash.

## <a name="prerequisites"></a>Pré-requisitos
1. Conta Microsoft Azure ([abrir uma conta gratuita aqui](https://azure.microsoft.com/pricing/free-trial/))
2. [Node.js](http://nodejs.org) instalado (este exemplo pressupõe que tem a versão 4.2.2 do Node.js)
3. [Git](https://git-scm.com/) instalado
4. Conta [GitHub](https://github.com/)

Embora o App Service suporte várias formas de implementar o seu código numa aplicação API, este tutorial mostra o método Git e pressupõe que tem conhecimentos básicos para trabalhar com o Git. Para obter informações sobre outros métodos de implementação, consulte o artigo [Implementar a aplicação no App Service do Azure](../app-service-web/web-sites-deploy.md).

## <a name="get-the-sample-code"></a>Obter o código de exemplo
1. Abra uma interface de linha de comandos que execute comandos Node.js e Git.
2. Navegue para uma pasta que possa utilizar para um repositório local do Git e clone o [repositório GitHub que contém o código de exemplo](https://github.com/Azure-Samples/app-service-api-node-contact-list).
   
        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
   
    A API de amostra fornece dois pontos finais: um pedido Get para `/contacts` devolve uma lista de nomes e endereços de correio eletrónico no formato JSON, enquanto `/contacts/{id}` devolve apenas o contacto selecionado.

## <a name="scaffold-autogenerate-nodejs-code-based-on-swagger-metadata"></a>Estruturar (gerar automaticamente) o código Node.js com base nos metadados Swagger
[Swagger](http://swagger.io/) é um formato de ficheiro de metadados que descreve uma API RESTful. O App Service do Azure possui [suporte incorporado para metadados Swagger](app-service-api-metadata.md). Esta secção do tutorial exemplifica o fluxo de trabalho de desenvolvimento de uma API na qual cria primeiro metadados Swagger e utiliza para estruturar (gerar automaticamente) o código do servidor para a API. 

> [!NOTE]
> Pode ignorar esta secção se não quiser saber como estruturar o código do Node.js a partir de um ficheiro de metadados Swagger. Se pretender implementar apenas código de exemplo numa nova aplicação API, aceda diretamente à secção [Criar uma aplicação API no Azure](#createapiapp).
> 
> 

### <a name="install-and-execute-swaggerize"></a>Instalar e executar o Swaggerize
1. Execute os seguintes comandos para instalar os módulos NPM **yo** e **generator-swaggerize** globalmente.
   
        npm install -g yo
        npm install -g generator-swaggerize
   
    O Swaggerize é uma ferramenta que gera o código do servidor para uma API descrita através de um ficheiro de metadados Swagger. O ficheiro Swagger que irá utilizar tem o nome *api.json* e está localizado na pasta *Iniciar* do repositório que clonou.
2. Navegue para a pasta *Iniciar* pasta e, em seguida, execute o comando `yo swaggerize`. O Swaggerize irá fazer uma série de perguntas.  Em **o que chamar a este projeto**, introduza “ContactList”, em **caminho para o documento swagger**, introduza “api.json” e em **Express, Hapi ou Restify**, introduza “express”.
   
        yo swaggerize
   
    ![Linha de comandos do Swaggerize](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
   
    **Nota**: se encontrar um erro neste passo, o passo seguinte explica como o corrigir.
   
    O Swaggerize cria uma pasta de aplicação, estrutura processadores e ficheiros de configuração e gera um ficheiro **package.json**. O motor de vista express é utilizado para gerar a página de ajuda Swagger.  
3. Se o comando `swaggerize` falha com um "token inesperado" ou um erro de "sequência de escape inválida", corrija a causa do erro editando o ficheiro *package.json* gerado. Na linha `regenerate` em `scripts`, altere a barra invertida que precede *api.json* para uma barra, para que a linha tenha um aspeto semelhante ao seguinte exemplo:
   
         "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"
4. Navegue até à pasta que contém o código estruturado (neste caso, a subpasta */start/ContactList*).
5. Execute `npm install`.
   
        npm install
6. Instale o módulo NPM **jsonpath**. 
   
        npm install --save jsonpath
   
    ![Instalação do Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)
7. Instale o módulo NPM**swaggerize-ui**. 
   
        npm install --save swaggerize-ui
   
    ![Instalação da IU do Swaggerize](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### <a name="customize-the-scaffolded-code"></a>Personalizar o código estruturado
1. Copie a pasta **lib** a partir da pasta **Iniciar** para a pasta **ContactList** criada pelo estruturador. 
2. Substitua o código no ficheiro **handlers/contacts.js** pelo código seguinte. 
   
    Este código utiliza os dados JSON armazenados no ficheiro **lib/contacts.json** que é servido pelo **lib/contactRepository.js**. O novo código contacts.js responde a pedidos de HTTP para obter todos os contactos e devolvê-los como um payload JSON. 
   
        'use strict';
   
        var repository = require('../lib/contactRepository');
   
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };
3. Substitua o código no ficheiro **handlers/contacts/{id}.js** pelo código seguinte. 
   
        'use strict';
   
        var repository = require('../../lib/contactRepository');
   
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };
4. Substitua o código no **server.js** pelo código seguinte. 
   
    As alterações efetuadas ao ficheiro server.js são realçadas através da utilização de comentários para que possa ver as alterações efetuadas. 
   
        'use strict';
   
        var port = process.env.PORT || 8000; // first change
   
        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');
   
        var app = express();
   
        var server = http.createServer(app);
   
        app.use(bodyParser.json());
   
        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));
   
        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));
   
        server.listen(port, function () { // fifth and final change
        });

### <a name="test-with-the-api-running-locally"></a>Testar com a API em execução localmente
1. Ative o servidor utilizando o executável da linha de comandos Node.js. 
   
        node server.js
2. Ao navegar para **http://localhost:8000/contacts**, verá a saída JSON da lista de contactos (ou será avisado para transferi-la, dependendo do seu browser). 
   
    ![Chamada à Api Todos os Contactos](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)
3. Ao navegar para **http://localhost:8000/contacts/2**, verá o contacto representado por esse valor de id.
   
    ![Chamada à Api Contacto Específico](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)
4. Os dados Swagger JSON são servidos através do ponto final **/swagger**:
   
    ![Contactos Swagger Json](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)
5. A IU do Swagger é servida através do ponto final **/docs**. Na IU do Swagger, pode utilizar as funcionalidades de cliente HTML avançadas para testar a sua API.
   
    ![IU do Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a name="a-idcreateapiappa-create-a-new-api-app"></a><a id="createapiapp"></a> Criar uma nova Aplicação API
Nesta secção irá utilizar o Portal do Azure para criar uma nova Aplicação API no Azure. Esta aplicação API representa os recursos de computação que o Azure irá fornecer para executar o código. Nas secções posteriores vai implementar o código na nova aplicação API.

1. Navegue para o [Portal do Azure](https://portal.azure.com/). 
2. Clique em **Novo > Web + Móvel > Aplicação API**. 
   
    ![Nova aplicação API no portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)
3. Introduza um **Nome de aplicação** que seja exclusivo no domínio *azurewebsites.net*, tal como NodejsAPIApp e um número para torná-lo único. 
   
    Por exemplo, se o nome for `NodejsAPIApp`, o URL será `nodejsapiapp.azurewebsites.net`.
   
    Se introduzir um nome que outra pessoa já tenha utilizado, verá um ponto de exclamação vermelho à direita.
4. No menu pendente **Grupo de Recursos**, clique em **Novo** e, em seguida, em **Novo nome do grupo de recursos** introduza "NodejsAPIAppGroup" ou outro nome se preferir. 
   
    Um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) é uma coleção de recursos do Azure, tais como aplicações API, bases de dados e VMs. Para este tutorial, é melhor criar um novo grupo de recursos porque torna mais fácil eliminar num único passo todos os recursos do Azure que criar para o tutorial.
5. Clique em **Plano do App Service/Localização** e, em seguida, clique em **Criar Novo**.
   
    ![Criar plano do App Service](./media/app-service-api-nodejs-api-app/newappserviceplan.png)
   
    Nos passos seguintes, pode criar um plano do App Service para o novo grupo de recursos. Um plano do App Service especifica os recursos de computação em que é executada a aplicação API. Por exemplo, se escolher o escalão gratuito, a aplicação API é executada em VMs partilhadas, enquanto para alguns escalões pagos, é executada em VMs dedicadas. Para obter informações sobre os planos do App Service, consulte [Descrição geral dos planos do App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
6. No painel **Plano do App Service**, introduza "NodejsAPIAppPlan" ou outro nome, se preferir.
7. Na lista pendente **Localização**, escolha a localização que esteja mais perto de si.
   
    Esta definição especifica em que datacenter do Azure a sua aplicação será executada. Para este tutorial, pode selecionar qualquer região pois não terá uma diferença percetível. Mas para uma aplicação de produção, pretende que o servidor esteja o mais perto possível dos clientes que estão a aceder ao mesmo para minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).
8. Clique em **Escalão de preço > Ver Tudo > F1 Gratuito**.
   
    Para este tutorial, o escalão de preço gratuito irá fornecer o desempenho suficiente.
   
    ![Selecionar o Escalão de preço gratuito](./media/app-service-api-nodejs-api-app/selectfreetier.png)
9. No painel **Plano do App Service**, clique em **OK**.
10. No painel **Aplicação API**, clique em **Criar**.

## <a name="set-up-your-new-api-app-for-git-deployment"></a>Configurar a nova aplicação API para a implementação do Git
Irá implementar o código para a aplicação API ao emitir consolidações para um repositório do Git no App Service do Azure. Nesta secção do tutorial, vai criar crie as credenciais e o repositório do Git no Azure que irá utilizar para a implementação.  

1. Depois de criada a aplicação API, clique em **Serviços de Aplicações > {a aplicação API}** a partir da home page do portal. 
   
    O portal apresenta os painéis **Aplicação API** e **Definições**.
   
    ![Painéis Aplicação API e Definições do Portal](media/app-service-api-nodejs-api-app/portalapiappblade.png)
2. No painel **Definições**, desloque-se para baixo para a secção **Publicação** e, em seguida, clique em **Credenciais de implementação**.
3. No painel **Definir credenciais de implementação**, introduza um nome de utilizador e uma palavra-passe e, em seguida, clique em **Guardar**.
   
    Irá utilizar estas credenciais para publicar o seu código Node.js para a sua aplicação API. 
   
    ![Credenciais de Implementação](media/app-service-api-nodejs-api-app/deployment-credentials.png)
4. No painel **Definições**, clique em **Origem da implementação > Escolher Origem > Repositório Local do Git** e, em seguida, clique em **OK**.
   
    ![Criar Repositório do Git](media/app-service-api-nodejs-api-app/create-git-repo.png)
5. Quando o repositório do Git tiver sido criado, o painel muda para mostrar as implementações ativas. Uma vez que o repositório é novo, não tem implementações ativas na lista. 
   
    ![Sem Implementações Ativas](media/app-service-api-nodejs-api-app/no-active-deployments.png)
6. Copie o URL do repositório do Git. Para tal, navegue para o painel da nova aplicação API e observe a secção **Princípios Básicos** do painel. Repare no **URL de clone do Git ** na secção **Princípios Básicos**. Quando paira o rato sobre este URL, verá um ícone à direita que vai copiar o URL para a área de transferência. Clique neste ícone para copiar o URL.
   
    ![Obter o Url do Git a partir do Portal](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)
   
    **Nota**: irá precisar do URL de clone do Git na secção seguinte, por isso certifique-se de que o guarda em algum lado.

Agora que tem uma Aplicação API com um repositório de Git guardado como cópia de segurança, pode emitir o código para o repositório para implementar o código na aplicação API. 

## <a name="deploy-your-api-code-to-azure"></a>Implementar o código API no Azure
Nesta secção, vai criar um repositório de Git local que contém o código de servidor para a API e, em seguida, emitir o código desse repositório para o repositório no Azure que criou anteriormente.

1. Copie a pasta `ContactList` para uma localização que pode utilizar para um novo repositório de Git local. Se realizou a primeira parte do tutorial, copie `ContactList` a partir da pasta `start`; caso contrário, copie `ContactList` a partir da pasta `end`.
2. Na ferramenta de linha de comandos, navegue para a nova pasta e, em seguida, execute o seguinte comando para criar um novo repositório de Git local. 
   
        git init
   
     ![Novo Repositório de Git Local](media/app-service-api-nodejs-api-app/new-local-git-repo.png)
3. Execute o comando seguinte para adicionar um Git remoto para o repositório da sua Aplicação API. 
   
        git remote add azure YOUR_GIT_CLONE_URL_HERE
   
    **Nota**: substitua a cadeia "YOUR_GIT_CLONE_URL_HERE" pelo seu URL de clone de Git que copiou anteriormente. 
4. Execute os seguintes comandos para criar uma consolidação que contenha todo o conteúdo do código. 
   
        git add .
        git commit -m "initial revision"
   
    ![Saída de Consolidação de Git](media/app-service-api-nodejs-api-app/git-commit-output.png)
5. Execute o comando para emitir o código para o Azure. Quando lhe for pedida uma palavra-passe, introduza aquela que criou previamente no Portal do Azure.
   
        git push azure master
   
    Isto aciona uma implementação para a sua aplicação API.  
6. No seu browser, navegue de volta para o painel **Implementações** da aplicação API e irá verificar que a implementação está a ocorrer. 
   
    ![Implementação a Decorrer](media/app-service-api-nodejs-api-app/deployment-happening.png)
   
    Em simultâneo, a interface de linha de comandos reflete o estado da implementação enquanto está a decorrer. 
   
    ![Implementação do Node.js a Decorrer](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)
   
    Depois de concluída a implementação, o painel **Implementações** reflete a implementação com êxito das alterações de código para a sua aplicação API. 

## <a name="test-with-the-api-running-in-azure"></a>Testar com a API em execução no Azure
1. Copie o **URL** na secção **Princípios Básicos** do painel Aplicação API. 
   
    ![Implementação Concluída](media/app-service-api-nodejs-api-app/deployment-completed.png)
2. Se utilizar um cliente API REST como o Postman ou Fiddler (ou o seu browser), forneça o URL da chamada API dos seus contactos, que é o ponto final `/contacts` da sua aplicação API. O URL será `https://{your API app name}.azurewebsites.net/contacts`
   
    Quando emite um pedido GET para este ponto final, obterá a saída JSON da sua aplicação API.
   
    ![API de Alcance do Postman](media/app-service-api-nodejs-api-app/postman-hitting-api.png)
3. Num browser, aceda ao ponto final `/docs` para experimentar a IU do Swagger enquanto é executada no Azure.

Agora que tem a distribuição contínua a funcionar, pode fazer alterações ao código e implementá-las no Azure, bastando emitir consolidações para o repositório de Git do Azure.

## <a name="next-steps"></a>Passos seguintes
Neste momento, já criou com êxito uma aplicação API e implementou o código API do Node.js à mesma. O próximo tutorial mostra como [consumir API Apps a partir de clientes JavaScript, utilizando a CORS](app-service-api-cors-consume-javascript.md).




<!--HONumber=Nov16_HO2-->



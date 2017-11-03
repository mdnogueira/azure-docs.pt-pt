---
title: "Criar uma aplicação web Node.js para a base de dados do Azure Cosmos | Microsoft Docs"
description: "Este tutorial de Node.js explicar como utilizar a base de dados do Microsoft Azure Cosmos para armazenar e aceder a dados a partir de uma aplicação de web de Node.js Express alojada em Web sites do Azure."
keywords: "Desenvolvimento de aplicações, tutorial de base de dados, saiba node.js, node.js tutorial"
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/14/2017
ms.author: mimig
ms.openlocfilehash: 32d4b4a7db134975158e53fc964c24b52b14a1e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="_Toc395783175"></a>Build a Node.js web application using Azure Cosmos DB (Criar uma aplicação Web Node.js com o Azure Cosmos DB)
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [python](documentdb-python-application.md)
> 
> 

Este tutorial Node.js mostra-lhe como utilizar a base de dados do Azure Cosmos e a API do DocumentDB para armazenar e aceder a dados a partir de uma aplicação Node.js Express alojada em sites Web do Azure. Crie uma aplicação de gestão de tarefas simples baseada na Web, uma aplicação ToDo que lhe permite criar, obter e concluir tarefas. As tarefas são armazenadas como documentos JSON no Azure Cosmos DB. Este tutorial orienta-o durante a criação e a implementação da aplicação e explica o que acontece em cada fragmento.

![Faça uma captura de ecrã da aplicação My Todo List criada neste tutorial Node.js](./media/documentdb-nodejs-application/cosmos-db-node-js-mytodo.png)

Não tem tempo para concluir o tutorial e apenas pretende ver a solução completa? Não é um problema, pode obter a solução de exemplo completa a partir do [GitHub][GitHub]. Ler apenas o ficheiro [Leia-me](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) para obter instruções sobre como executar a aplicação.

## <a name="_Toc395783176"></a>Pré-requisitos
> [!TIP]
> Este tutorial Node.js parte do pressuposto de que tem alguma experiência anterior na utilização do Node.js e de sites Web do Azure.
> 
> 

Antes de seguir as instruções deste artigo, deve certificar-se de que tem o seguinte:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Versão [Node.js][Node.js] v0.10.29 ou superior.
* [Express generator](http://www.expressjs.com/starter/generator.html) (pode instalá-lo através de `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Passo 1: Criar uma conta de base de dados do Azure Cosmos DB
Comecemos por criar uma conta do Azure Cosmos DB. Se já tiver uma conta ou se estiver a utilizar o Emulador do Azure Cosmos DB para este tutorial, pode avançar para o [Passo 2: Criar uma aplicação Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Passo 2: Criar uma nova aplicação Node.js
Agora, vamos aprender a criar um projeto básico Olá, Mundo Node.js com a arquitetura [Express](http://expressjs.com/).

1. Abra o seu terminal favorito, como a linha de comandos Node.js.
2. Navegue para o diretório no qual pretende armazenar a nova aplicação.
3. Utilizar o Express generator para gerar uma nova aplicação designada **todo**.
   
        express todo
4. Abra o seu novo diretório **todo** e instale as dependências.
   
        cd todo
        npm install
5. Execute a sua nova aplicação.
   
        npm start
6. Pode ver a sua aplicação nova ao navegar para [http://localhost:3000](http://localhost:3000) no browser.
   
    ![Saiba Node.js - Captura de ecrã da aplicação Olá, Mundo numa janela do browser](./media/documentdb-nodejs-application/cosmos-db-node-js-express.png)

    Em seguida, para parar a aplicação, prima CTRL+C na janela do terminal e, em seguida, clique em **y** para terminar a tarefa de lote.

## <a name="_Toc395783179"></a>Passo 3: instalar módulos adicionais
O ficheiro **package.json** é um dos ficheiros criados na raiz do projeto. Este ficheiro contém uma lista de módulos adicionais que são necessários para a sua aplicação Node.js. Mais tarde, quando irá implementar esta aplicação para Web sites do Azure, este ficheiro é utilizado para determinar quais os módulos que têm de ser instalados no Azure para suportar a sua aplicação. Ainda temos de instalar mais dois pacotes para este tutorial.

1. De regresso ao terminal, instale o módulo **async** através do npm.
   
        npm install async --save
2. Instalar o módulo **documentdb** através do npm. Este é o módulo onde acontece a todos os a magia de BD do Cosmos do Azure.
   
        npm install documentdb --save
3. Uma verificação rápida do ficheiro **package.json** deverá mostrar os módulos adicionais. Este ficheiro irá indicar ao Auzre quais os pacotes a transferir e instalar aquando da execução da sua aplicação. Deverá assemelhar-se ao exemplo abaixo.
   
        {
          "name": "todo",
          "version": "0.0.0",
          "private": true,
          "scripts": {
            "start": "node ./bin/www"
          },
          "dependencies": {
            "async": "^2.1.4",
            "body-parser": "~1.15.2",
            "cookie-parser": "~1.4.3",
            "debug": "~2.2.0",
            "documentdb": "^1.10.0",
            "express": "~4.14.0",
            "jade": "~1.11.0",
            "morgan": "~1.7.0",
            "serve-favicon": "~2.3.0"
          }
        }
   
    Isto indica ao nó (e mais tarde Azure) que a aplicação depende destes módulos adicionais.

## <a name="_Toc395783180"></a>Passo 4: utilizar o serviço do Azure Cosmos DB numa aplicação de nó
Esta ação toma conta de toda a definição e configuração iniciais. Passemos agora ao que interessa, isto é, escrever alguns códigos utilizando o Azure Cosmos DB.

### <a name="create-the-model"></a>Criar o modelo
1. O diretório do projeto, criar um novo diretório nomeado **modelos** no mesmo diretório do ficheiro package.json.
2. No diretório **modelos**, crie um novo ficheiro designado **taskDao.js**. Este ficheiro irá conter o modelo para as tarefas criadas pelo nossa aplicação.
3. No referido diretório **modelos**, crie um novo ficheiro designado **docdbUtils.js**. Este ficheiro irá conter alguns códigos úteis e reutilizáveis que iremos utilizar para a nossa aplicação. 
4. Copie o seguinte código para **docdbUtils.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
   
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
   
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
   
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
   
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };               
   
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
   
                            client.createCollection(databaseLink, collectionSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
   
        module.exports = DocDBUtils;
   
5. Guarde e feche o ficheiro **docdbUtils.js**.
6. No início do ficheiro **taskDao.js**, adicione o seguinte código para referenciar o **DocumentDBClient** e o **docdbUtils.js** anteriormente criado:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');
7. Em seguida, irá adicionar um código para definir e exportar o objeto Tarefa. Esta ação é responsável pela inicialização do nosso objeto Tarefa e pela configuração da Base de dados e da Coleção de Documentos que iremos utilizar.
   
        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
   
          this.database = null;
          this.collection = null;
        }
   
        module.exports = TaskDao;
8. Em seguida, adicione o seguinte código para definir métodos adicionais no objeto Tarefa, o que permite interações com os dados armazenados no Azure Cosmos DB.
   
        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
   
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
   
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
   
            find: function (querySpec, callback) {
                var self = this;
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results);
                    }
                });
            },
   
            addItem: function (item, callback) {
                var self = this;
   
                item.date = Date.now();
                item.completed = false;
   
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, doc);
                    }
                });
            },
   
            updateItem: function (itemId, callback) {
                var self = this;
   
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        doc.completed = true;
   
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
   
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
   
            getItem: function (itemId, callback) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };
9. Guarde e feche o ficheiro **taskDao.js**. 

### <a name="create-the-controller"></a>Criar o controlador
1. No diretório **rotas** do seu projeto, crie um novo ficheiro designado **tasklist.js**. 
2. Adicione o seguinte código ao **tasklist.js**. Esta ação irá carregar os módulos DocumentDBClient e async, utilizados pelo **tasklist.js**. Tal também define a função **TaskList**, que é transmitida a uma instância do objeto **Tarefa** definido anteriormente:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
   
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
   
        module.exports = TaskList;
3. Continue a adicionar ao ficheiro **tasklist.js** adicionando os métodos utilizados para **showTasks, addTask** e **completeTasks**:
   
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
   
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
   
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
   
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
   
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
   
                    res.redirect('/');
                });
            },
   
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
   
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };
4. Guarde e feche o ficheiro **tasklist.js**.

### <a name="add-configjs"></a>Adicionar config.js
1. No diretório do seu projeto, crie um novo ficheiro designado **config.js**.
2. Adicione o seguinte ao **config.js**. Esta ação define os parâmetros e valores da configuração necessários para a nossa aplicação.
   
        var config = {}
   
        config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
   
        module.exports = config;
3. No ficheiro **config.js**, atualize os valores ANFITRIÃO e AUTH_KEY utilizando os valores encontrados no painel Chaves da sua conta do Azure Cosmos DB no [portal do Microsoft Azure](https://portal.azure.com).
4. Guarde e feche o ficheiro **config.js**.

### <a name="modify-appjs"></a>Modificar app.js
1. No diretório do projeto, abra o ficheiro **app.js**. Este ficheiro foi criado anteriormente, aquando da criação da aplicação Web Express.
2. Adicione o seguinte código na parte superior do **app.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');
3. Este código define o ficheiro de configuração a utilizar e prossegue para a leitura de valores deste ficheiro em certas variáveis que iremos brevemente utilizar.
4. Substitua as duas linhas seguintes no ficheiro **app.js**:
   
        app.use('/', index);
        app.use('/users', users); 
   
      pelo seguinte fragmento:
   
        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');
5. Estas linhas definem uma nova instância do nosso objeto **TaskDao**, com uma nova ligação ao Azure Cosmos DB (utilizando os valores ler a partir do **config.js**), inicializam o objeto de tarefa e, em seguida, vinculam ações de formulário a métodos do nosso controlador **TaskList**. 
6. Por fim, guarde e feche o ficheiro **app.js** já que estamos quase a acabar.

## <a name="_Toc395783181"></a>Passo 5: criar uma interface de utilizador
Agora, vamos concentrar a nossa atenção na criação de uma interface de utilizador para que um utilizador possa interagir com a nossa aplicação. A aplicação Express que criámos utiliza o **Jade** como motor de vista. Para obter mais informações sobre o Jade, consulte [http://jade-lang.com/](http://jade-lang.com/).

1. O ficheiro **layout.jade** no diretório **vistas** é utilizado como um modelo global para outros ficheiros **.jade**. Neste passo, irá modificá-lo para utilizar o [Twitter Bootstrap](https://github.com/twbs/bootstrap), que é um conjunto de ferramentas que facilita a criação de um site Web agradável. 
2. Abra o ficheiro **layout.jade** encontrado na pasta **vistas** e substitua o conteúdo seguindo os seguintes procedimentos:

    ```
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body
        nav.navbar.navbar-inverse.navbar-fixed-top
          div.navbar-header
            a.navbar-brand(href='#') My Tasks
        block content
        script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
        script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
    ```

    Esta ação indica eficazmente ao motor **Jade** para compor algumas HTML para a nossa aplicação e cria um **bloco** denominado **conteúdo** onde podemos fornecer o modelo para as nossas páginas de conteúdo.

    Guarde e feche esse ficheiro **layout.jade**.

3. Agora, abra o ficheiro **index.jade**, a vista que será utilizada pela nossa aplicação e substitua o conteúdo do ficheiro pelo seguinte:
   
        extends layout
        block content
           h1 #{title}
           br
        
           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               if (typeof tasks === "undefined")
                 tr
                   td
               else
                 each task in tasks
                   tr
                     td #{task.name}
                     td #{task.category}
                     - var date  = new Date(task.date);
                     - var day   = date.getDate();
                     - var month = date.getMonth() + 1;
                     - var year  = date.getFullYear();
                     td #{month + "/" + day + "/" + year}
                     td
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

Esta ação expande o modelo e fornece o conteúdo para o marcador de posição **conteúdo** que vimos anteriormente no ficheiro **layout.jade**.
   
Nesse modelo, criámos dois formulários HTML.

O primeiro formulário contém uma tabela para os nossos dados e um botão que nos permite atualizar os itens, publicando no método **/completetask** do nosso controlador.
    
O segundo formulário contém dois campos de entrada e um botão que nos permite criar um novo item, publicando no método **/addtask** do nosso controlador.

Tal deverá ser o suficiente para que a nossa aplicação funcione.

## <a name="_Toc395783181"></a>Passo 6: executar a sua aplicação localmente
1. Para testar a aplicação no computador local, execute `npm start` no terminal para iniciar a aplicação e, em seguida, atualize a página do browser [http://localhost:3000](http://localhost:3000). A página agora deverá ser semelhante à imagem abaixo:
   
    ![Captura de ecrã da aplicação MyTodo List numa janela do browser](./media/documentdb-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Se receber um erro sobre o avanço no ficheiro layout.jade ou no ficheiro index.jade, certifique-se de que as primeiras duas linhas em ambos os ficheiros é justificada à esquerda, sem espaços. Se existirem espaços antes das primeiras duas linhas, remova-os, guarde ambos os ficheiros e, em seguida, atualize a janela do browser. 

2. Utilize o Item, Nome do Item e campos de Categoria para introduzir uma nova tarefa e, em seguida, clique em **Adicionar Item**. Esta ação cria um documento no Azure Cosmos DB com essas propriedades. 
3. A página deverá ser atualizada para mostrar o item criado recentemente na ToDo List.
   
    ![Captura de ecrã da aplicação com um novo item na ToDo List](./media/documentdb-nodejs-application/cosmos-db-node-js-added-task.png)
4. Para concluir uma tarefa, basta marcar a caixa de verificação na coluna Concluir e, em seguida, clicar em **Atualizar tarefas**. Este procedimento atualiza o documento já criado.

5. Para parar a aplicação, prima CTRL+C na janela do terminal e, em seguida, clique em **Y** para terminar a tarefa de lote.

## <a name="_Toc395783182"></a>Passo 7: implementar o projeto de desenvolvimento da sua aplicação em Web sites do Azure
1. Se ainda não o fez, ative um repositório de git para o seu site Web do Azure. Pode encontrar instruções sobre como efetuar esta ação no tópico [Implementação de Git Local para o Serviço de Aplicações do Azure](../app-service/app-service-deploy-local-git.md).
2. Adicione o seu site do Azure como um git remoto.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Implemente ao enviar para remoto.
   
        git push azure master
4. Em alguns segundos, git irá concluir a publicação da sua aplicação web e iniciar um browser, onde poderá ver o seu handiwork em execução no Azure!

    Parabéns! Acabou de criar a sua primeira aplicação Web Node.js Express com o Azure Cosmos DB, que foi publicada em sites do Azure.

    Se pretende transferir ou referenciar a aplicação de referência completa para este tutorial, pode ser transferido a partir do [GitHub][GitHub].

## <a name="_Toc395637775"></a>Passos seguintes

* Pretende testar o dimensionamento e desempenho com o Azure Cosmos DB? Consulte o artigo [Performance and Scale Testing with Azure Cosmos DB](performance-testing.md) (Testar o Desempenho e o Dimensionamento com o Azure Cosmos DB)
* Saiba como [monitorizar uma conta do Azure Cosmos DB](monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Explore a [Documentação do Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app


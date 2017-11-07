---
title: "Table storage do Azure: criar uma aplicação web Node.js | Microsoft Docs"
description: "Um tutorial que se baseia-se a aplicação Web com o tutorial rápida através da adição de serviços de armazenamento do Azure e o módulo do Azure."
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 2e931f0457511963b87c1b14fe873593bcb000c7
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-table-storage-nodejs-web-application"></a>Table storage do Azure: aplicação Web Node.js
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial, a aplicação que criou no [aplicação Web Node.js utilizando rápida] tutorial é expandido com as bibliotecas de cliente do Microsoft Azure para Node.js para trabalhar com os serviços de gestão de dados. Expandir a sua aplicação através da criação de uma aplicação de lista de tarefas baseada na web que pode implementar no Azure. A lista de tarefas permite que um utilizador obter tarefas, adicionar novas tarefas e marcar tarefas como concluído.

Os itens de tarefas são armazenados no armazenamento do Azure. Storage do Azure fornece armazenamento de dados não estruturados com tolerância a falhas e altamente disponível. Armazenamento do Azure inclui várias estruturas de dados, onde pode armazenar e aceder a dados. Pode utilizar os serviços de armazenamento a partir de APIs incluído no SDK do Azure para Node.js ou através de REST APIs. Para obter mais informações, consulte [armazenamento e aceder a dados no Azure].

Este tutorial parte do princípio de que concluiu o [aplicação Web Node.js] e [Node.js rápidas][aplicação Web Node.js utilizando rápida] tutoriais.

Contém as seguintes informações:

* Como trabalhar com o motor de modelo Jade
* Como trabalhar com os serviços de gestão de dados do Azure

A seguinte captura de ecrã mostra a aplicação concluída:

![A página web foi concluída no internet explorer](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Definição de credenciais do armazenamento na Web. config
Tem de passar armazenamento as credenciais para aceder ao armazenamento do Azure. Isto é feito utilizando as definições da aplicação Web. config.
As definições da Web. config são transmitidas como variáveis de ambiente para o nó, que, em seguida, são lidas pelo SDK do Azure.

> [!NOTE]
> Credenciais de armazenamento apenas são utilizadas quando a aplicação for implementada para o Azure. Quando em execução no emulador, a aplicação utiliza o emulador de armazenamento.
>
>

Execute os seguintes passos para obter as credenciais da conta de armazenamento e adicioná-los para as definições da Web. config:

1. Se ainda não estiver aberto, inicie o Azure PowerShell a partir de **iniciar** menu expandindo **todos os programas, Azure**, com o botão direito **Azure PowerShell**e, em seguida, selecione **executar como administrador**.
2. Altere os diretórios para a pasta que contém a aplicação. Por exemplo, c:\\nó\\tasklist\\WebRole1.
3. Na janela do Azure Powershell, introduza o seguinte cmdlet para obter as informações de conta de armazenamento:

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts
    ```

   O cmdlet anterior obtém a lista de contas de armazenamento e a conta chaves com o serviço alojado.

   > [!NOTE]
   > Uma vez que o SDK do Azure cria uma conta de armazenamento, quando implementar um serviço, uma conta de armazenamento já deve existir da implementação da aplicação nos guias anteriores.
   >
   >
4. Abra o **servicedefinition. Csdef** ficheiro que contém as definições de ambiente que são utilizadas quando a aplicação for implementada para o Azure:

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. Insira o seguinte bloco em **ambiente** elemento, substituindo {conta de armazenamento} e {chave de acesso de armazenamento} com o nome de conta e a chave primária para a conta de armazenamento que pretende utilizar para a implementação:

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![O conteúdo do ficheiro web.cloud.config](./media/table-storage-cloud-service-nodejs/node37.png)

6. Guarde o ficheiro e feche o bloco de notas.

### <a name="install-additional-modules"></a>Instalar módulos adicionais
1. Utilize o seguinte comando para instalar o [azure], [uuid nó], [nconf] e [async] módulos localmente, bem como para guardar uma entrada para as mesmas para o **Package. JSON** ficheiro:

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  O resultado deste comando deve ser semelhante ao seguinte:

  ```
  node-uuid@1.4.1 node_modules\node-uuid

  nconf@0.6.9 node_modules\nconf
  ├── ini@1.1.0
  ├── async@0.2.9
  └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

  azure-storage@0.1.0 node_modules\azure-storage
  ├── extend@1.2.1
  ├── xmlbuilder@0.4.3
  ├── mime@1.2.11
  ├── underscore@1.4.4
  ├── validator@3.1.0
  ├── node-uuid@1.4.1
  ├── xml2js@0.2.7 (sax@0.5.2)
  └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)
  ```

## <a name="using-the-table-service-in-a-node-application"></a>Utilizar o serviço de tabela numa aplicação node
Nesta secção, a aplicação básica criados pelo **rápida** comando é expandido adicionando um **task.js** ficheiro que contém o modelo para as suas tarefas. Modificar o existente **app.js** do ficheiro e crie um novo **tasklist.js** ficheiro que utiliza o modelo.

### <a name="create-the-model"></a>Criar o modelo
1. No **WebRole1** directory, criar um novo diretório designado **modelos**.
2. No **modelos** directory, crie um novo ficheiro designado **task.js**. Este ficheiro contém o modelo para as tarefas criadas pela sua aplicação.
3. No início do **task.js** ficheiro, adicione o seguinte código para fazer referência a bibliotecas necessárias:

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. Em seguida, adicione o código para definir e exportar o objeto de tarefa. O objeto de tarefa é responsável por ligar para a tabela.

    ```nodejs
    module.exports = Task;

    function Task(storageClient, tableName, partitionKey) {
      this.storageClient = storageClient;
      this.tableName = tableName;
      this.partitionKey = partitionKey;
      this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
        if(error) {
          throw error;
        }
      });
    };
    ```

5. Em seguida, adicione o seguinte código para definir métodos adicionais no objeto tarefa, que permite interações com os dados armazenados na tabela:

    ```nodejs
    Task.prototype = {
      find: function(query, callback) {
        self = this;
        self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
          if(error) {
            callback(error);
          } else {
            callback(null, result.entries);
          }
        });
      },

      addItem: function(item, callback) {
        self = this;
        // use entityGenerator to set types
        // NOTE: RowKey must be a string type, even though
        // it contains a GUID in this example.
        var itemDescriptor = {
          PartitionKey: entityGen.String(self.partitionKey),
          RowKey: entityGen.String(uuid()),
          name: entityGen.String(item.name),
          category: entityGen.String(item.category),
          completed: entityGen.Boolean(false)
        };

        self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
          if(error){
            callback(error);
          }
          callback(null);
        });
      },

      updateItem: function(rKey, callback) {
        self = this;
        self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
          if(error) {
            callback(error);
          }
          entity.completed._ = true;
          self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
            if(error) {
              callback(error);
            }
            callback(null);
          });
        });
      }
    }
    ```

6. Guarde e feche o **task.js** ficheiro.

### <a name="create-the-controller"></a>Criar o controlador
1. No **WebRole1/rotas** directory, crie um novo ficheiro designado **tasklist.js** e abri-lo no editor de texto.
2. Adicione o seguinte código ao **tasklist.js**. Este código carrega os módulos do azure e async, que são utilizados pelo **tasklist.js** e define o **TaskList** função, o que é transmitida uma instância do **tarefas** objeto iremos definido anteriormente:

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. Continuar a adicionar ao **tasklist.js** ficheiro adicionando os métodos utilizados para **showTasks**, **addTask**, e **completeTasks**:

    ```nodejs
    TaskList.prototype = {
      showTasks: function(req, res) {
        self = this;
        var query = azure.TableQuery()
          .where('completed eq ?', false);
        self.task.find(query, function itemsFound(error, items) {
          res.render('index',{title: 'My ToDo List ', tasks: items});
        });
      },

      addTask: function(req,res) {
        var self = this
        var item = req.body.item;
        self.task.addItem(item, function itemAdded(error) {
          if(error) {
            throw error;
          }
          res.redirect('/');
        });
      },

      completeTask: function(req,res) {
        var self = this;
        var completedTasks = Object.keys(req.body);
        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
          self.task.updateItem(completedTask, function itemsUpdated(error) {
            if(error){
              callback(error);
            } else {
              callback(null);
            }
          });
        }, function goHome(error){
          if(error) {
            throw error;
          } else {
            res.redirect('/');
          }
        });
      }
    }
    ```

4. Guardar o **tasklist.js** ficheiro.

### <a name="modify-appjs"></a>Modificar app.js
1. No **WebRole1** diretório, abra o **app.js** ficheiro num editor de texto.
2. No início do ficheiro, adicione o seguinte ao carregar o módulo do azure e defina a chave de partição e o nome de tabela:

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. No ficheiro app.js, desloque para baixo para onde vir a seguinte linha:

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    Substitua as linhas anteriores com o seguinte código. Este código inicializa uma instância de <strong>tarefas</strong> com uma ligação à sua conta do storage. O <strong>tarefas</strong> é passado para o <strong>TaskList</strong>, que utiliza-o para comunicar com o serviço de tabela:

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. Guardar o **app.js** ficheiro.

### <a name="modify-the-index-view"></a>Modificar a vista de índice
1. Altere os diretórios para o **vistas** diretório e abra o **Index. jade** ficheiro num editor de texto.
2. Substitua os conteúdos do **Index. jade** ficheiro com o seguinte código. Este código define a vista para apresentar tarefas existentes e define um formulário para adicionar novas tarefas e marcar existentes como concluído.

    ```
    extends layout

    block content
      h1= title
      br

      form(action="/completetask", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Date
            td Complete
          if tasks != []
            tr
              td
          else
            each task in tasks
              tr
                td #{task.name._}
                td #{task.category._}
                - var day   = task.Timestamp._.getDate();
                - var month = task.Timestamp._.getMonth() + 1;
                - var year  = task.Timestamp._.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
        button.btn(type="submit") Update tasks
      hr
      form.well(action="/addtask", method="post")
        label Item Name:
        input(name="item[name]", type="textbox")
        label Item Category:
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item
    ```

3. Guarde e feche **Index. jade** ficheiro.

### <a name="modify-the-global-layout"></a>Modificar o esquema global
O ficheiro **layout.jade** no diretório **vistas** é utilizado como um modelo global para outros ficheiros **.jade**. Neste passo, modifique o **jade** ficheiro a utilizar [Twitter Bootstrap](https://github.com/twbs/bootstrap), que é um conjunto de ferramentas que facilita a conceber um site Web agradável.

1. Transferir e extrair os ficheiros para [Twitter Bootstrap](http://getbootstrap.com/). Copiar o **bootstrap.min.css** de ficheiros do **arranque de configuração\\dist\\css** pasta para o **pública\\tramas** diretório da sua aplicação tasklist.
2. Do **vistas** pasta, abra o **jade** de ficheiros no seu editor de texto e substitua o conteúdo com o seguinte:

    título do doctype html html head = ligação título (rel = "folha de estilos', href='/stylesheets/bootstrap.min.css) ligação (rel = 'folha de estilos', href='/stylesheets/style.css) body.app nav.navbar.navbar predefinido div.navbar cabeçalho a.navbar-brand(href='/') tarefas My bloco conteúdo

3. Guardar o **jade** ficheiro.

### <a name="running-the-application-in-the-emulator"></a>Executar a aplicação no emulador
Utilize o seguinte comando para iniciar a aplicação no emulador do.

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

O browser abre e apresenta a seguinte página:

![Uma web bloco paginada intitulada My lista de tarefas com uma tabela que contém os campos para adicionar uma nova tarefa e tarefas.](./media/table-storage-cloud-service-nodejs/node44.png)

Utilize o formato para adicionar itens, ou remover itens existentes, marcar como concluída.

## <a name="publishing-the-application-to-azure"></a>Publicar a aplicação no Azure
Na janela do Windows PowerShell, chame o cmdlet seguinte para voltar a implementar o serviço alojado no Azure.

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

Substitua **myuniquename** com um nome exclusivo para esta aplicação. Substitua **datacentername** com o nome de um centro de dados do Azure, tal como **EUA oeste**.

Depois de concluída a implementação, deverá ver uma resposta semelhante ao seguinte:

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
  WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
  WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
  WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
  WARNING: 2:19:01 PM - Connecting...
  WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
  WARNING: 2:19:40 PM - Upgrading...
  WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
  WARNING: 2:22:48 PM - Initializing...
  WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
  WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.
```

Ao especificar o **-iniciar** opção no cmdlet anterior, o browser abre e apresenta a aplicação em execução no Azure aquando da conclusão da publicação.

![Uma janela do browser apresentar a página da minha lista de tarefas. O URL indica que a página está agora a ser alojada no Azure.](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Parar e eliminar a aplicação
Depois de implementar a aplicação, poderá querer desativá-la para que possa evitar custos ou criar e implementar outras aplicações num período de tempo de avaliação gratuita.

O Azure cobra as instâncias de função da Web por hora de tempo do servidor consumido.
O tempo do servidor é consumido logo que a aplicação é implementada, mesmo que as instâncias não estejam em execução e estejam paradas.

Os passos seguintes mostram como parar e eliminar a sua aplicação.

1. Na janela do Windows PowerShell, pare a implementação do serviço criada na secção anterior com o seguinte cmdlet:

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   A paragem do serviço pode demorar vários minutos. Quando o serviço estiver parado, receberá uma mensagem a indicar que foi parado.

2. Para eliminar o serviço, chame o cmdlet seguinte:

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   Quando lhe for pedido, introduza **S** para eliminar o serviço.

   A eliminação do serviço pode demorar vários minutos. Depois do serviço é eliminado, receberá uma mensagem a indicar que o serviço foi eliminado.

[aplicação Web Node.js utilizando rápida]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[armazenamento e aceder a dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[aplicação Web Node.js]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/



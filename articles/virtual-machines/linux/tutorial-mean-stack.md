---
title: "Criar uma média da pilha de uma VM com Linux no Azure | Microsoft Docs"
description: "Saiba como criar uma pilha de MongoDB, Express, AngularJS e Node.js (média) numa VM com Linux no Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 1d74ead08dfb63276afb08bdcb7f4e3e3db5bfd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-vm-in-azure"></a>Criar uma pilha de MongoDB, Express, AngularJS e Node.js (média) numa VM com Linux no Azure

Este tutorial mostra como implementar uma pilha de MongoDB, Express, AngularJS e Node.js (média) numa VM com Linux no Azure. A pilha de média que criar permite adicionar, eliminar e listar books numa base de dados. Saiba como:

> [!div class="checklist"]
> * Criar uma VM do Linux
> * Instalar o Node.js
> * Instalar MongoDB e configurar o servidor
> * Instalar rápida e configurar as rotas ao servidor
> * As rotas com AngularJS de acesso
> * Executar a aplicação

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Criar uma VM do Linux

Criar um grupo de recursos com o [criar grupo az](https://docs.microsoft.com/cli/azure/group#az_group_create) de comandos e criar uma VM com Linux com o [az vm criar](https://docs.microsoft.com/cli/azure/vm#az_vm_create) comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo seguinte utiliza a CLI do Azure para criar um grupo de recursos denominado *myResourceGroupMEAN* no *eastus* localização. É criada uma VM com o nome *myVM* com chaves SSH se estes ainda não existir numa localização chave predefinido. Para utilizar um conjunto específico de chaves, utilize o - opção de ssh-chave-valor.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Quando a VM foi criada, a CLI do Azure mostra as informações semelhante ao seguinte exemplo: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.

Utilize o seguinte comando para criar uma sessão SSH com a VM. Certifique-se de que utiliza o endereço IP público correto. No nosso exemplo acima nosso IP endereço era 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Instalar o Node.js

[NODE.js](https://nodejs.org/en/) é um tempo de execução de JavaScript incorporado no motor de V8 JavaScript do Chrome. Para configurar as rotas de rápida e controladores AngularJS, node.js é utilizado neste tutorial.

Na VM, utilizando a shell de deteção que tiver aberto com SSH, instale o Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Instalar MongoDB e configurar o servidor
[MongoDB](http://www.mongodb.com) armazena dados em documentos flexíveis, como o JSON. Campos numa base de dados podem variar a partir de um documento para o documento e estrutura de dados pode ser alterada ao longo do tempo. Para a nossa aplicação de exemplo que estamos a adicionar registos do livro para MongoDB que contêm o nome do livro, número de isbn, autor e número de páginas. 

1. Na VM, utilizando a shell de deteção que tiver aberto com SSH, defina a chave do MongoDB.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Atualize o Gestor de pacotes com a chave.
  
    ```bash
    sudo apt-get update
    ```

3. Instale MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Inicie o servidor.

    ```bash
    sudo service mongodb start
    ```

5. Também é necessário instalar o [corpo parser](https://www.npmjs.com/package/body-parser-json) pacote para o ajudar-na processar o JSON transmitido pedidos para o servidor.

    Instale o Gestor de pacote npm.

    ```bash
    sudo apt-get install npm
    ```

    Instale o pacote do analisador de corpo.
    
    ```bash
    sudo npm install body-parser
    ```

6. Crie uma pasta denominada *Books* e adicione um ficheiro para o mesmo nome *server.js* que contém a configuração para o servidor web.

    ```node.js
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Instalar rápida e configurar as rotas ao servidor

[Express](https://expressjs.com) é uma mínima e flexível web estrutura de aplicação Node.js que fornece funcionalidades de aplicações móveis e web. Express é utilizado neste tutorial para passar informações para e da nossa base de dados de MongoDB de livro. [O mongoose](http://mongoosejs.com) fornece uma solução simples, com base no esquema para modelar os seus dados de aplicação. O mongoose é utilizado neste tutorial para fornecer um esquema de livro para a base de dados.

1. Instale o Mongoose e o Express.

    ```bash
    sudo npm install express mongoose
    ```

2. No *Books* pasta, crie uma pasta denominada *aplicações* e adicione um ficheiro denominado *routes.js* com as rotas rápidas definidas.

    ```node.js
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. No *aplicações* pasta, crie uma pasta denominada *modelos* e adicione um ficheiro denominado *book.js* com a configuração de modelo do livro definida.  

    ```node.js
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>As rotas com AngularJS de acesso

[AngularJS](https://angularjs.org) proporciona uma arquitetura de web para criar vistas dinâmicas nas suas aplicações web. Neste tutorial, utilizamos AngularJS para ligar a nossa página web com rápida e executar ações no nossa base de dados do livro.

1. Altere o diretório de criar uma cópia de segurança *Books* (`cd ../..`) e, em seguida, crie uma pasta denominada *pública* e adicione um ficheiro denominado *script.js* com a configuração de controlador definida.

    ```node.js
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. No *pública* pasta, crie um ficheiro denominado *index.html* com a página web definida.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Executar a aplicação

1. Altere o diretório de criar uma cópia de segurança *Books* (`cd ..`) e iniciar o servidor ao executar o comando:

    ```bash
    nodejs server.js
    ```

2. Abra um browser para o endereço que registadas para a VM. Por exemplo, *http://13.72.77.9:3300*. Deverá ver algo semelhante a seguinte página:

    ![Registo de arranque](media/tutorial-mean/meanstack-init.png)

3. Introduzir dados de caixas de texto e clique em **adicionar**. Por exemplo:

    ![Adicionar o registo de arranque](media/tutorial-mean/meanstack-add.png)

4. Depois de atualizar a página, deverá ver algo semelhante a esta página:

    ![Registos do livro de lista](media/tutorial-mean/meanstack-list.png)

5. Pode clicar em **eliminar** e remover o registo de arranque da base de dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação web que mantém um registo dos registos de livro utilizando uma média da pilha de uma VM com Linux. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM do Linux
> * Instalar o Node.js
> * Instalar MongoDB e configurar o servidor
> * Instalar rápida e configurar as rotas ao servidor
> * As rotas com AngularJS de acesso
> * Executar a aplicação

Avançar para o próximo tutorial para saber como proteger servidores web com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor web com SSL](tutorial-secure-web-server.md)

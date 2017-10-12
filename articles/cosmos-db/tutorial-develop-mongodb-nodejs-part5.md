---
title: Tutorial de MongoDB, Angular e Node para o Azure - Parte 5 | Microsoft Docs
description: "Parte 5 da série do tutorial sobre como criar uma aplicação MongoDB com Angular e Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.openlocfilehash: e752e18f6d579633c0cf553224ae7617b774ad0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>Criar uma aplicação MongoDB com Angular e Azure Cosmos DB - parte 5: Utilizar o Mongoose para ligar ao Azure Cosmos DB

Este tutorial com várias parte demonstra como criar uma nova aplicação [API de MongoDB](mongodb-introduction.md) escrita em Node.js com Express, Angular e a sua base de dados do Azure Cosmos DB.

A Parte 5 do tutorial é a continuação da [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) e abrange as seguintes tarefas:

> [!div class="checklist"]
> * Utilizar o Mongoose para ligar ao Azure Cosmos DB
> * Obter as informações da cadeia de ligação a partir do Azure Cosmos DB
> * Criar o modelo de hero
> * Criar o serviço de hero para obter dados de hero
> * Executar a aplicação localmente

## <a name="video-walkthrough"></a>Instruções de vídeo

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, certifique-se de que concluiu os passos na [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) do tutorial.

> [!TIP]
> Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>Utilizar o Mongoose para ligar ao Azure Cosmos DB

1. Instale o módulo de npm do mongoose, que é uma API normalmente utilizada para comunicar com o MongoDB.

    ```bash
    npm i mongoose --save
    ```

2. Agora, crie um novo ficheiro na pasta de **servidor** com o nome **mongo.js**. Neste ficheiro, adicione todas as informações da ligação à base de dados do Azure Cosmos DB.

3. Copie o código seguinte para **mongo.js**. Este código:
    * Requer o Mongoose.
    * Substitui a promessa do Mongo de utilizar a promessa básica que está incorporada nas versões ES6/ES2015 e superiores.
    * Chama um ficheiro env que lhe permite configurar determinadas coisas consoante esteja em teste, produção ou desenvolvimento. Vamos criar este ficheiro em breve.
    * Inclui a nossa cadeia de ligação do MongoDB, que vai ser definida no ficheiro env.
    * Cria uma função connect que chama o Mongoose.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.dbName}:${env.key}@${env.dbName}.documents.azure.com:${env.cosmosPort}/?ssl=true`; //&replicaSet=globaldb`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. No painel do Explorador, crie uma pasta em **servidor** com o nome **ambiente** e, na pasta **ambiente**, crie um ficheiro novo chamado **environment.js** .

5. Com base no ficheiro mongo.js, sabemos que temos de incluir `dbName`, `key` e `cosmosPort`, por isso, copie o código seguinte em **environment.js**.

    ```javascript
    const cosmosPort = 1234; // replace with your port
    const dbName = 'your-cosmos-db-name-goes-here';
    const key = 'your-key-goes-here';

    module.exports = {
      dbName,
      key,
      cosmosPort
    };
    ```

## <a name="get-the-connection-string-information"></a>Obter as informações da cadeia ligação

1. Em **environment.js**, altere o valor de `cosmosPort` para 10255. (Pode encontrar a porta do Cosmos DB no portal do Azure)

    ```javascript
    const cosmosPort = 10255;
    ```

2. Em **environment.js**, altere o valor de `dbName` para o nome da conta do Azure Cosmos DB que criou no [Passo 4](tutorial-develop-mongodb-nodejs-part4.md). 

3. Obtenha a chave primária da conta do Azure Cosmos DB ao introduzir o comando da CLI seguinte na janela do terminal: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>` é o nome da conta do Azure Cosmos DB que criou no [Passo 4](tutorial-develop-mongodb-nodejs-part4.md).

4. Copie a chave primária para o ficheiro environment.js como o valor `key`.

    A sua aplicação tem agora todas as informações de que precisa para se ligar ao Azure Cosmos DB. Estas informações também podem ser obtidas no portal. Para obter mais informações, veja [Get the MongoDB connection string to customize](connect-mongodb-account.md#GetCustomConnection) (Obter a cadeia de ligação do MongoDB para personalizar). O Nome de Utilizador no portal equivale a dbName em environments.js. 

## <a name="create-a-hero-model"></a>Criar um modelo de hero

1.  No painel do Explorer, crie o ficheiro **hero.model.js** na pasta **servidor**.

2. Copie o código seguinte para **hero.model.js**. Este código:
   * Requer o Mongoose.
   * Cria um esquema novo com ID, nome e mensagem.
   * Utiliza o esquema para criar um modelo.
   * Exporta o modelo. 
   * Dê à coleção o nome Heroes (em vez de Heros, que seria o nome predefinido da coleção com base nas regras de nomenclatura no plural do Mongoose).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Criar um modelo de hero

1.  No painel do Explorer, crie o ficheiro **hero.service.js** na pasta **servidor**.

2. Copie o código seguinte para **hero.service.js**. Este código:
   * Obtém o modelo que acabou de criar
   * Liga à base de dados
   * Cria uma variável docquery que utiliza o método hero.find para definir uma consulta que devolve todos os heroes.
   * Executa uma consulta com o docquery.exec com uma promessa de obter uma lista de todos os heroes, onde o estado de resposta é 200. 
   * Se o estado é 500, devolve a mensagem de erro
   * Uma vez que estamos a utilizar módulos, obtém os heroes. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>Adicionar o serviço de hero a routes.js

1. No Visual Studio Code, em **routes.js**, comente a função `res.send` que envia os dados de hero de exemplo e adicione uma linha para, em alternativa, chamar a função `heroService.getHeroes`.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. Em **routes.js**, requerer o serviço de hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. Em **hero.service.js**, atualize a função getHeroes para receber os parâmetros `req` e `res` da seguinte forma:

    ```javascript
    function getHeroes(req, res) {
    ```

    Vamos dispensar um minuto para rever e orientar ao longo da cadeia de chamada. Primeiro, temos `index.js`, que configura o servidor de nós e repare que configura e define as nossas rotas. Em seguida, o ficheiro routes.js comunica com o serviço de hero e diz-lhe para obter as nossas funções, como getHeroes, e transmitir o pedido e a resposta. Aqui, o hero.service.js agarra o modelo e liga ao Mongo e, em seguida, executa getHeroes quando o chamamos e devolve 200 como resposta. Em seguida, é enviado novamente através da cadeia. 

## <a name="run-the-app"></a>Executar a aplicação

1. Vamos agora executar a aplicação outra vez. No Visual Studio Code, guarde todas as alterações, clique em no botão **Depurar** ![ícone de Depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png), no lado esquerdo, clique no botão **Iniciar Depuração** ![Ícone de depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

3. Agora, vamos passar para o browser. Abra as Ferramentas do programador e o separador Rede e navegue para http://localhost:3000, onde a nossa aplicação se encontra.

    ![Nova conta do Azure Cosmos DB no portal do Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   Ainda não existem heroes armazenados na aplicação, mas, no passo seguinte do tutorial, vamos adicionar as funcionalidades de Put, Push e Eliminar, para que possamos adicionar, atualizar e eliminar heroes a partir da IU mediante a utilização de ligações do Mongoose à nossa base de dados do Azure Cosmos DB. 

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Utilizou as APIs do Mongoose para ligar a sua aplicação de heroes ao Azure Cosmos DB. 
> * Adicionou a funcionalidade de obter heroes à aplicação

Pode avançar para a parte seguinte do tutorial para adicionar as funções Post, Put e Eliminar à aplicação.

> [!div class="nextstepaction"]
> [Add Post, Put, and Delete functions to the app](tutorial-develop-mongodb-nodejs-part6.md) (Adicionar as funções Post, Put e Eliminar à aplicação)

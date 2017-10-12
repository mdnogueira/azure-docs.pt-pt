---
title: Tutorial de MongoDB, Angular e Node para o Azure - Parte 2 | Microsoft Docs
description: "Parte 2 da série do tutorial sobre como criar uma aplicação MongoDB com Angular e Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB."
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
ms.openlocfilehash: 48b7da57b23dbd16571c8fa179efd900cdcf21ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-2-create-a-nodejs-express-app-with-the-angular-cli"></a>Criar uma aplicação MongoDB com o Angular e o Azure Cosmos DB - Parte 2: Criar uma aplicação Node.js Express com a CLI do Angular 

Este tutorial com várias parte demonstra como criar uma nova aplicação [API de MongoDB](mongodb-introduction.md) escrita em Node.js com Express, Angular e a sua base de dados do Azure Cosmos DB.

A Parte 2 do tutorial é a continuação da [introdução](tutorial-develop-mongodb-nodejs.md) e abrange as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a CLI do Angular e o TypeScript
> * Criar um novo projeto com o Angular
> * Utilizar a arquitetura Express para compilar a aplicação
> * Testar a aplicação no Postman

## <a name="video-walkthrough"></a>Instruções de vídeo

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, certifique-se de que já viu o [vídeo de introdução](tutorial-develop-mongodb-nodejs.md).

Este tutorial também requer: 
* A versão 8.4.0 do [Node.js](https://nodejs.org/) ou superior.
* [Postman](https://www.getpostman.com/)
* O [Visual Studio Code](https://code.visualstudio.com/) ou o seu editor de código preferido.

> [!TIP]
> Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="install-the-angular-cli-and-typescript"></a>Instalar a CLI do Angular e o TypeScript

1. Abra uma Janela da Linha de Comandos do Windows ou uma janela de Terminal do Mac e instale a CLI do Angular.

    ```bash
    npm install -g @angular/cli
    ```

2. Introduza o comando seguinte na linha de comandos para instalar o TypeScript. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Utilizar a CLI do Angular para criar um projeto novo

1. Na linha de comandos, mude para a pasta em que pretende criar o projeto novo e execute o comando abaixo. Este comando cria uma pasta e um projeto novos com o nome angular-cosmosdb e instala os componentes do Angular necessários para uma nova aplicação. Também instala o código de origem na pasta src/client (-sd src/client), utiliza a configuração mínima (--minimal) e especifica que o projeto utiliza Sass (uma sintaxe semelhante a CSS com o sinalizador --style scss).

    ```bash
    ng new angular-cosmosdb -sd src/client --minimal --style scss
    ```

2. Após a conclusão do comando, mude os diretórios para a pasta src/client.

    ```bash
    cd angular-cosmosdb
    ```

3. Em seguida, abra a pasta no Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Utilizar a arquitetura Express para compilar a aplicação

1. No Visual Studio Code, no painel **Explorer**, clique com botão direito do rato na pasta **src**, clique em **Nova Pasta** e dê o nome *server* à mesma.

2. No painel **Explorer**, clique com o botão direito do rato na pasta **server**, clique em **Novo Ficheiro** e dê o nome *index.js* ao mesmo.

3. Novamente na linha de comandos, utilize o comando seguinte para instalar o analisador de corpo. Isto ajuda a nossa aplicação a analisar os dados JSON que são transmitidos através das APIs.

    ```bash
    npm i express body-parser --save
    ```

4. No Visual Studio Code, copie o código abaixo no ficheiro index.js. Este código:
    * Referencia o Express
    * Extrai o analisador de corpo para ler os dados JSON no corpo dos pedidos
    * Utiliza uma funcionalidade incorporada, denominada caminho
    * Define variáveis de raiz para tornar mais fácil localizar o código
    * Configura uma porta
    * Inicia o Express
    * Diz à aplicação como utilizar o middleware que vamos utilizar para servir o servidor
    * Serve tudo o que está na pasta dist, que será o conteúdo estático
    * Serve a aplicação e index.html para os pedidos GET que não forem encontrados no servidor (para ligações avançadas)
    * Inicia o servidor com app.listen
    * Utiliza uma função arow para registar que a porta está ativada
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. No Visual Studio Code, no painel **Explorer**, clique com botão direito do rato na pasta **server** e clique em **Novo ficheiro**. Dê o nome *routes.js* ao ficheiro novo. 

6. Copie o código seguinte para **routes.js**. Este código:
   * Referencia o router do Express
   * Obtém os heroes
   * Envia o JSON novamente para um hero definido

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Guarde todos os seus ficheiros modificados. 

8. No Visual Studio Code, clique no botão **Depurar** ![Debug icon in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png), clique no botão de engrenagem ![Gear button in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png) e selecione **Node.js** para criar uma configuração.

   O novo ficheiro launch.json é aberto no Visual Studio Code.

8. Na linha 11 do ficheiro launch.json, altere `"program": "${file}"` para `"program": "${workspaceRoot}/src/server/index.js"` e guarde-o.

9. Clique no botão **Iniciar Depuração** ![Debug icon in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png) para executar a aplicação.

    A aplicação deverá ser executada sem erros.

## <a name="use-postman-to-test-the-app"></a>Utilizar o Postman para testar a aplicação

1. Agora, abra o Postman e coloque `http://localhost:3000/api/heroes` na caixa GET. 

2. Clique no botão **Enviar** e obtenha a resposta json da aplicação. 

    Esta resposta mostra que a aplicação está em execução localmente. 

    ![O Postman mostra o pedido e a resposta](./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png)


## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Criou um projeto Node.js com a CLI do Angular
> * Testou a aplicação com o Postman

Pode avançar para a parte seguinte do tutorial, para criar a IU.

> [!div class="nextstepaction"]
> [Build the UI with Angular](tutorial-develop-mongodb-nodejs-part3.md) (Criar a IU com o Angular)

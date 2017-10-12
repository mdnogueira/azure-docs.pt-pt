---
title: Tutorial de MongoDB, Angular e Node para o Azure - Parte 6 | Microsoft Docs
description: "Parte 6 da série do tutorial sobre como criar uma aplicação MongoDB com Angular e Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB."
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
ms.openlocfilehash: 4dee49f99118cc99c21ce23e32db3686c58cf4a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-6-add-post-put-and-delete-functions-to-the-app"></a>Criar uma aplicação MongoDB com o Angular e o Azure Cosmos DB - Parte 6: Adicionar funções Post, Put e Delete à aplicação

Este tutorial com várias parte demonstra como criar uma nova aplicação [API de MongoDB](mongodb-introduction.md) escrita em Node.js com o Express e Angular e ligá-la à sua base de dados do Azure Cosmos DB.

A Parte 6 do tutorial é a continuação da [Parte 5](tutorial-develop-mongodb-nodejs-part5.md) e abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar as funções Post, Put e Delete para o serviço hero
> * Executar a aplicação

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar esta parte do tutorial, certifique-se de que concluiu os passos na [Parte 5](tutorial-develop-mongodb-nodejs-part5.md) do tutorial.

> [!TIP]
> Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="add-a-post-function-to-the-hero-service"></a>Adicionar uma função Post ao serviço hero

1. No Visual Studio Code, abra **routes.js** e **hero.service.js** lado a lado ao premir o botão **Dividir Editor** ![botão de divisão Editor no Visual Studio ](./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png).

    Veja se a linha 7 de routes.js chama a função `getHeroes` na linha 5 de **hero.service.js**.  Temos de criar este emparelhamento para as funções post, put e delete. 

    ![routes.js e hero.service.js no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png)
    
    Vamos começar por programar o serviço hero. 

2. Copie o código seguinte para **hero.service.js** a seguir à função `getHeroes` e antes de `module.exports`. Este código:  
   * Utiliza o modelo de hero para publicar um hero novo.
   * Verifica as respostas para ver se existe um erro e devolve o valor de estado de 500.

   ```javascript
   function postHero(req, res) {
     const originalHero = { id: req.body.id, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. Em **hero.service.js**, atualize o `module.exports` para incluir a função `postHero` nova. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. Em **routes.js**, adicione um router para a função `post` a seguir ao router `get`. Este router publica um hero de cada vez. Estruturar o ficheiro do router desta forma mostra claramente todos os pontos finais da API disponíveis e deixa o trabalho real a cargo do ficheiro **hero.service.js**.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Execute a aplicação para confirmar que está tudo a funcionar. No Visual Studio Code, guarde todas as alterações, clique em no botão **Depurar** ![ícone de Depuração no Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png), no lado esquerdo, clique no botão **Iniciar Depuração** ![Start debugging icon in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png).

6. Agora, regresse ao browser e prima F12, na maioria dos computadores, para abrir o separador Ferramentas do programador e Rede. Navegue para [http://localhost:3000](http://localhost:3000) para ver as chamadas efetuadas através da rede.

    ![Separador Redes no Chrome que mostra a atividade de rede](./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png)

7. Clique no botão **Adicionar Hero Novo** para adicionar um hero novo. Introduza o ID "999", o nome "Pedro" e a indicação "Olá" e, em seguida, clique em **Guardar**. Deverá ver, no separador Rede, que enviou um pedido POST para um hero novo. 

    ![Separador Rede no Chrome que mostra a atividade de rede para as funções Get e Post](./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png)

    Agora, vamos voltar atrás e adicionar as funções Put e Delete à aplicação.

## <a name="add-the-put-and-delete-functions"></a>Adicionar as funções Put e Delete

1. Em **routes.js**, adicione os routers `put` e `delete` a seguir ao router post.

    ```javascript
    router.put('/hero/:id', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:id', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Copie o código seguinte para **hero.service.js** a seguir à função `checkServerError`. Este código:
   * Cria as funções `put` e `delete`
   * Verifica se o hero foi encontrado
   * Procede ao processamento de erros 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       id: parseInt(req.params.id, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ id: originalHero.id }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const id = parseInt(req.params.id, 10);
     Hero.findOneAndRemove({ id: id })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. Em **hero.service.js**, exporte os módulos novos:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Agora que atualizámos o código, clique no botão **Reiniciar** ![Restart button in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png) no Visual Studio Code.

5. Atualize a página do browser e clique no botão **Adicionar novo hero**. Adicione um hero novo com o ID "9", o nome "Starlord" e a indicação "Hi". Clique no botão **Guardar** para guardar o hero novo.

6. Agora, selecione o hero **Starlord**, mude a indicação de “Hi” para “Bye” e clique no botão **Guardar**. 

    Agora pode selecionar o ID no separador Rede para mostrar o payload. Pode ver, no payload, que a indicação está agora definida como “Bye”.

    ![Aplicação heroes e separador Rede que mostra o payload](./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png) 

    Também pode eliminar um dos heroes na IU e ver quanto tempo é que a operação delete demora a ser concluída. Experimente ao clicar no botão "Eliminar" no hero com o nome "Fred".

    ![Aplicação heroes e separador Rede que mostra o tempo que demora a concluir as funções](./media/tutorial-develop-mongodb-nodejs-part6/times.png) 

    Se atualizar a página, o separador Rede mostra o tempo que demora a obter os heroes. Embora estes tempos sejam rápidos, muito está dependente de onde os seus dados estão localizados no mundo e da sua capacidade de georreplicá-los numa zona perto dos seus utilizadores. Pode encontrar mais informações sobre a georreplicação no tutorial seguinte, que vai ser lançado em breve.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Adicionou as funções Post, Put e Delete à aplicação 

Regresse aqui para ver vídeos adicionais nesta série de tutoriais.


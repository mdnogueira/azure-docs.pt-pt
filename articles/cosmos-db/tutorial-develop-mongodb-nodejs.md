---
title: Tutorial de MongoDB, Angular e Node para o Azure | Microsoft Docs
description: "Saiba como criar uma aplicação MongoDB com o Angular e o Node no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB nesta série de tutoriais em vídeo."
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
ms.custom: mvc
ms.openlocfilehash: c6d31816b5919afb2cc67bc42321422183ab30df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db"></a>Criar uma aplicação MongoDB com o Angular e o Azure Cosmos DB 

Este tutorial com várias parte demonstra como criar uma nova aplicação [API de MongoDB](mongodb-introduction.md) com Express, Angular e Nojde.js (a pilha MEAN) e ligá-la à sua base de dados do Azure Cosmos DB. O Azure Cosmos DB suporta ligações de cliente MongoDB para que possa utilizar o Azure Cosmos DB em vez do MongoDB, mas utilizar o mesmo código das aplicações MongoDB, com benefícios adicionais. Estes benefícios do Azure Cosmos DB incluem a implementação na cloud fácil, o dimensionamento, a segurança, os dados replicados globais, o suporte multimodelos e leituras e escritas super-rápidas. 

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Permite-lhe criar e consultar rapidamente documentos, chaves/valores e bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global, que constituem o âmago do Azure Cosmos DB. 

Este tutorial com várias parte abrange as seguintes tarefas:

> [!div class="checklist"]
> * [Create a Node.js Express app with the Angular CLI](tutorial-develop-mongodb-nodejs-part2.md) (Criar uma aplicação Node.js Express com a CLI do Angular)
> * [Build the UI with Angular](tutorial-develop-mongodb-nodejs-part3.md) (Criar a IU com o Angular)
> * [Create an Azure Cosmos DB account using the Azure CLI](tutorial-develop-mongodb-nodejs-part4.md) (Criar uma conta do Azure Cosmos DB com a CLI do Azure) 
> * [Use Mongoose to connect to Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md) (Utilizar o Mongoose para ligar ao Azure Cosmos DB)
> * [Add Post, Put, and Delete functions to the app](tutorial-develop-mongodb-nodejs-part6.md) (Adicionar as funções Post, Put e Eliminar à aplicação)

Quer criar esta mesma aplicação com React? Veja [a série de vídeos de tutorial sobre React](tutorial-develop-mongodb-react.md).

## <a name="video-walkthrough"></a>Instruções de vídeo

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>Projeto concluído 

Este tutorial orienta-o ao longo dos passos para criar a aplicação passo a passo. Se quiser transferir o projeto concluído, pode obter a aplicação terminada a partir do [repositório angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) no GitHub.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, fez o seguinte:

> [!div class="checklist"]
> * Viu uma descrição geral dos passos para criar uma aplicação MEAN.js com o Azure Cosmos DB. 

Pode avançar para a parte seguinte do tutorial para criar a aplicação Node.js Express.

> [!div class="nextstepaction"]
> [Create a Node.js Express app with the Angular CLI](tutorial-develop-mongodb-nodejs-part2.md) (Criar uma aplicação Node.js Express com a CLI do Angular)

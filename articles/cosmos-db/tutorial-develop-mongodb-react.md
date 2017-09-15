---
title: Tutorial de MongoDB, React e Node.js para o Azure | Microsoft Docs
description: "Saiba como criar uma aplicação MongoDB com o React e o Node.js no Azure Cosmos DB mediante a utilização das mesmas APIs que são utilizadas para MongoDB nesta série de tutoriais em vídeo."
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
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 622a26da8b7d5f5c3a242c5c88a4ae479095bf6f
ms.contentlocale: pt-pt
ms.lasthandoff: 09/07/2017

---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Criar uma aplicação MongoDB com o React e o Azure Cosmos DB  

Este tutorial de vídeo com várias parte demonstra como criar uma aplicação de acompanhamento de heroes com um front-end do React. A aplicação utiliza Node e Express para o servidor, liga-se ao Azure Cosmos DB com a [API MongoDB](mongodb-introduction.md) e, em seguida, liga o front-end do React à parte do servidor da mesma. O tutorial também mostra como fazer o dimensionamento de tipo apontar e clicar do Azure Cosmos DB no portal do Azure, bem como implementar a aplicação na Internet, para que todos os utilizadores possam fazer o acompanhamento dos heroes preferidos. 

O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) suporta ligações de cliente MongoDB para que possa utilizar o Azure Cosmos DB em vez do MongoDB, mas utilizar o mesmo código das aplicações MongoDB, com benefícios adicionais, como a implementação na cloud simples, o dimensionamento e leituras e escritas super-rápidas.  

Este tutorial com várias parte abrange as seguintes tarefas:

> [!div class="checklist"]
> * Introdução
> * Configurar o projeto
> * Criar a IU com o React
> * Criar uma conta do Azure Cosmos DB com o portal do Azure
> * Utilizar o Mongoose para ligar ao Azure Cosmos DB
> * Adicionar operações React, Create, Update e Delete à aplicação

Quer criar esta mesma aplicação com o Angular? Veja a [série de vídeos de tutorial sobre o Angular](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Pré-requisitos
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Projeto concluído
Obtenha a aplicação concluída [no GitHub](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Introdução 

Neste vídeo, Burke Holland faz uma introdução ao Azure Cosmos DB e orienta-o ao longo da aplicação que é criada nesta série de vídeos. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Configuração do projeto

Este vídeo mostra como configurar o Express e o React no mesmo projeto. Depois, Burke faz a explicação passo a passo do código no projeto.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Criar a IU

Este vídeo mostra como criar a interface de utilizador (IU) da aplicação com o React. 

> [!NOTE]
> O CSS referenciado neste vídeo está disponível no [repositório do GitHub react-cosmosdb](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Ligar ao Azure Cosmos DB

Este vídeo mostra como criar uma conta do Azure Cosmos DB no portal do Azure, instalar os pacotes MongoDB e Mongoose e, em seguida, ligar a aplicação à conta criada recentemente com a cadeia de ligação do Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Ler e criar heroes na aplicação

Este vídeo mostra como ler e criar heroes na base de dados do Cosmos DB, bem como testar esses métodos com o Postman e a IU do React. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Eliminar e atualizar heroes na aplicação

Este vídeo mostra como eliminar e atualizar heroes na aplicação e apresentar as atualizações na IU. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Concluir a aplicação

Este vídeo mostra como concluir a aplicação e terminar a ligação da IU à API de back-end. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, siga os passos abaixo para eliminar todos os recursos criados por este tutorial no portal do Azure. 

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma aplicação com o React, o Node, o Express e o Azure Cosmos DB 
> * Criar uma conta do Azure Cosmos DB
> * Ligar a aplicação à conta do Azure Cosmos DB
> * Testar a aplicação com o Postman
> * Executar a aplicação e adicionar heroes à base de dados

Regresse aqui para ver vídeos adicionais nesta série de tutoriais que mostram como implementar a aplicação e replicar globalmente os seus dados.

Pode avançar para o próximo tutorial para ficar a saber como importar dados do MongoDB para o Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importar dados da MongoDB para o Azure Cosmos DB](mongodb-migrate.md)
 


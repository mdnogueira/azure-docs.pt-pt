---
title: Utilizar MongoChef para Azure Cosmos DB | Microsoft Docs
description: 'Saiba como utilizar MongoChef com uma base de dados do Azure Cosmos: API para a conta do MongoDB'
keywords: mongochef
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 54c9799bd646b827f602e2ea2f9a15a4fc853f00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-mongochef-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Utilizar MongoChef com uma base de dados do Azure Cosmos: API para a conta do MongoDB

Para ligar a uma base de dados do Azure Cosmos: API para a conta do MongoDB, tem de:

* Transfira e instale [MongoChef](http://3t.io/mongochef)
* Tem a sua base de dados do Azure Cosmos: API para a conta do MongoDB [cadeia de ligação](connect-mongodb-account.md) informações

## <a name="create-the-connection-in-mongochef"></a>Criar a ligação no MongoChef
Para adicionar a base de dados do Azure Cosmos: API para a conta do MongoDB para o Gestor de ligações de MongoChef, execute os seguintes passos.

1. Obter a sua base de dados do Azure Cosmos: API de informações de ligação do MongoDB utilizando as instruções [aqui](connect-mongodb-account.md).

    ![Captura de ecrã do painel de cadeia de ligação](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Clique em **Connect** para abrir o Gestor de ligações, em seguida, clique em **nova ligação**

    ![Captura de ecrã do Gestor de ligações MongoChef](./media/mongodb-mongochef/ConnectionManager.png)
3. No **nova ligação** janela, no **servidor** separador, introduza o anfitrião (FQDN) do BD Azure Cosmos: API para a conta do MongoDB e a porta.

    ![Captura de ecrã do separador de servidor do Gestor de ligação MongoChef](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. No **nova ligação** janela, no **autenticação** separador, escolha o modo de autenticação **Standard (MONGODB CR ou SCARM-SHA-1)** e introduza o nome de utilizador e palavra-passe.  Aceite a BD de autenticação predefinido (administrador) ou forneça o seu próprio valor.

    ![Captura de ecrã do separador autenticação de Gestor de ligação do MongoChef](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. No **nova ligação** janela, no **SSL** separador, verifique o **protocolo SSL utilizado para ligar** caixa de verificação e o **aceitar certificados SSL autoassinados do servidor**  botão de opção.

    ![Captura de ecrã do separador de SSL do Gestor de ligação MongoChef](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique em de **Testar ligação** botão para validar as informações de ligação, clique em **OK** para regressar à janela de nova ligação e, em seguida, clique em **guardar**.

    ![Captura de ecrã da janela de ligação de teste MongoChef](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>Utilize MongoChef para criar uma base de dados, coleção e documentos
Para criar uma base de dados, coleção e documentos utilizando MongoChef, execute os seguintes passos.

1. No **Gestor de ligações**, realce a ligação e clique em **Connect**.

    ![Captura de ecrã do Gestor de ligações MongoChef](./media/mongodb-mongochef/ConnectToAccount.png)
2. Clique com o botão direito do rato no anfitrião e escolha **Adicionar base de dados**.  Forneça um nome de base de dados e clique em **OK**.

    ![Captura de ecrã da opção MongoChef Adicionar base de dados](./media/mongodb-mongochef/AddDatabase1.png)
3. Clique com o botão direito do rato na base de dados e escolha **adicionar coleção**.  Forneça um nome da coleção e clique em **criar**.

    ![Captura de ecrã da opção MongoChef adicionar coleção](./media/mongodb-mongochef/AddCollection.png)
4. Clique em de **coleção** menu item, em seguida, clique em **Adicionar documento**.

    ![Captura de ecrã do item de menu MongoChef Adicionar documento](./media/mongodb-mongochef/AddDocument1.png)
5. Na caixa de diálogo Adicionar documento, cole o seguinte e, em seguida, clique em **Adicionar documento**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Adicione outro documento, desta vez com o seguinte conteúdo.

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Execute uma consulta de exemplo. Por exemplo, procure famílias com o apelido 'ou ' seja e devolver o principais e os campos de estado.

    ![Captura de ecrã Mongo Chef dos resultados da consulta](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Passos seguintes
* Explorar o Azure Cosmos DB: API para o MongoDB [amostras](mongodb-samples.md).

---
title: "Criar uma aplicação Node.js do Azure Cosmos DB com o Graph API | Microsoft Docs"
description: "Apresenta um exemplo de código Node.js que pode utilizar para ligar e consultar o Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: b9e8c46ba2f029f8dae2b357f05a806d769d0920
ms.contentlocale: pt-pt
ms.lasthandoff: 06/07/2017


---
<a id="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api" class="xliff"></a>

# Azure Cosmos DB: Criar uma aplicação Node.js com o Graph API

O Azure Cosmos DB é um serviço de bases de dados da Microsoft com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução rápida demonstra como criar uma conta do Azure Cosmos DB para o Graph API (pré-visualização), bases de dados e gráficos com o portal do Azure. Em seguida, irá criar e executar uma aplicação de consola com o controlador [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) «open-source».  

> [!NOTE]
> O módulo de npm `gremlin-secure` é uma versão modificada do módulo `gremlin`, com suporte para SSL e SASL exigidos para a ligação ao Azure Cosmos DB. O código de origem está disponível no [GitHub](https://github.com/CosmosDB/gremlin-javascript).
>

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

Antes de poder executar este exemplo, tem de ter os pré-requisitos seguintes:
* Versão de [Node.js](https://nodejs.org/en/) v0.10.29 ou superior
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-a-database-account" class="xliff"></a>

## Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

<a id="add-a-graph" class="xliff"></a>

## Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

<a id="clone-the-sample-application" class="xliff"></a>

## Clonar a aplicação de exemplo

Vamos agora clonar uma aplicação do Graph API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do Git, tal como Git Bash, e mude (com o comando `cd`) para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Abra o ficheiro da solução no Visual Studio. 

<a id="review-the-code" class="xliff"></a>

## Rever o código

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o ficheiro `app.js` e irá encontrar as seguintes linhas de código. 

* O cliente Gremlin é criado.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        config.endpoint, 
        { 
            "session": false, 
            "ssl": true, 
            "user": `/dbs/${config.database}/colls/${config.collection}`,
            "password": config.primaryKey
        });
    ```

  As configurações estão todas em `config.js` e podem ser editadas na secção seguinte.

* Uma série de passos do Gremlin são executados com o método `client.execute`.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

<a id="update-your-connection-string" class="xliff"></a>

## Atualizar a cadeia de ligação

Regresse agora ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. No [portal do Azure](http://portal.azure.com/), na sua conta do Azure Cosmos DB, no menu de navegação à esquerda, clique em **Chaves** e em **Chaves de leitura/escrita**. Irá utilizar os botões para copiar que se encontram no lado direito do ecrã e que lhe permitem copiar o URI e a chave principal para o ficheiro `app.js` no passo seguinte.

    ![O painel de chaves do portal do Azure](./media/create-graph-nodejs/keys.png)

2. Copie o valor do URI do Gremlin a partir do portal (com o botão para copiar) e transforme-o no valor da chave `config.endpoint` em config.js. O ponto final do Gremlin deve ser apenas o nome do sistema anfitrião sem o número de porta/protocolo, como `mygraphdb.graphs.azure.com` (não `https://mygraphdb.graphs.azure.com` ou `mygraphdb.graphs.azure.com:433`).

    `config.endpoint = "GRAPHENDPOINT";`

3. Copie o valor da chave primária do portal e transforme-o no valor de config.primaryKey em config.js. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

    `config.primaryKey = "PRIMARYKEY";`

4. Insira o nome da base de dados e o nome de gráfico (contentor) para o valor de config.database e config.collection. 

Eis um exemplo de como deve ser o aspeto do seu ficheiro config.js concluído:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "mygraphdb.graphs.azure.com";
config.primaryKey = "OjlhK6tjxfSXyKtrmCiM9O6gQQgu5DmgAoauzD1PdPIq1LZJmILTarHvrolyUYOB0whGQ4j21rdAFwoYep7Kkw==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

<a id="run-the-console-app" class="xliff"></a>

## Executar a aplicação de consola

1. Abra uma janela do terminal e mude (como o comando `cd`) para o diretório de instalação, para o ficheiro package.json que está incluído no projeto.  

2. Execute `npm install` para instalar os módulos npm necessários, incluindo `gremlin-secure`.

3. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

<a id="browse-with-data-explorer" class="xliff"></a>

## Procurar com o Data Explorer

Agora, pode voltar ao Data Explorer no portal do Azure para ver, consultar, alterar e trabalhar os dados do gráfico novo.

No Data Explorer, a base de dados nova é apresentada no painel **Coleções**. Expanda **graphdb**, **graphcoll** e clique em **Gráfico**.

Os dados gerados pela aplicação de exemplo são apresentados no painel **Gráficos**.

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-your-resources" class="xliff"></a>

## Apague os seus recursos

Se não planeia continuar a utilizar esta aplicação, elimine todos os recursos que criou neste artigo, fazendo o seguinte: 

1. No menu de navegação do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar e, em seguida, clique em **Eliminar**.

<a id="next-steps" class="xliff"></a>

## Passos seguintes

Neste artigo, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora pode criar consultas mais complexas e implementar a lógica de passagem de gráfico através do Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)


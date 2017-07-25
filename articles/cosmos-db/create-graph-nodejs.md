---
title: "Criar uma aplicação Node.js do Azure Cosmos DB com o Graph API | Microsoft Docs"
description: "Apresenta um exemplo de código Node.js que pode utilizar para ligar e consultar o Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 153b4cc668fdebd28cec5f3d95093a595064202a
ms.contentlocale: pt-pt
ms.lasthandoff: 07/17/2017

---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Criar uma aplicação Node.js com o Graph API

O Azure Cosmos DB é um serviço de bases de dados da Microsoft com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este guia de introdução rápida demonstra como criar uma conta do Azure Cosmos DB para o Graph API (pré-visualização), bases de dados e gráficos com o portal do Azure. Em seguida, irá criar e executar uma aplicação de consola com o controlador [Gremlin Node.js](https://www.npmjs.com/package/gremlin-secure) «open-source».  

> [!NOTE]
> O módulo de npm `gremlin-secure` é uma versão modificada do módulo `gremlin`, com suporte para SSL e SASL exigidos para a ligação ao Azure Cosmos DB. O código de origem está disponível no [GitHub](https://github.com/CosmosDB/gremlin-javascript).
>

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder executar este exemplo, tem de ter os pré-requisitos seguintes:
* Versão de [Node.js](https://nodejs.org/en/) v0.10.29 ou superior
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Adicionar um gráfico

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Vamos agora clonar uma aplicação do Graph API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do Git, tal como Git Bash, e mude (com o comando `cd`) para um diretório de trabalho.  

2. Execute o seguinte comando para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Abra o ficheiro da solução no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

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

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

1. Abra o ficheiro config.js. 

2. No config.js, preencha a chave de config.endpoint com o valor **URI de Gremlin** a partir da página **Descrição Geral** do portal do Azure. 

    `config.endpoint = "GRAPHENDPOINT";`

    ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-graph-nodejs/gremlin-uri.png)

   Se o valor **URI do Gremlin** estiver em branco, pode gerá-lo na página **Chaves** do portal, utilizando o valor **URI**, removendo https:// e alterando os documentos para gráficos.

   O ponto final do Gremlin deve ser apenas o nome do sistema anfitrião sem o número de porta/protocolo, como `mygraphdb.graphs.azure.com` (não `https://mygraphdb.graphs.azure.com` ou `mygraphdb.graphs.azure.com:433`).

3. No config.js, preencha o valor de config.primaryKey com o valor da **Chave Primária** a partir da página **Chaves** do portal do Azure. 

    `config.primaryKey = "PRIMARYKEY";`

   ![O painel de chaves do portal do Azure](./media/create-graph-nodejs/keys.png)

4. Insira o nome da base de dados e o nome de gráfico (contentor) para o valor de config.database e config.collection. 

Eis um exemplo de como deve ser o aspeto do seu ficheiro config.js concluído:

```nodejs
var config = {}

// Note that this must not have HTTPS or the port number
config.endpoint = "testgraphacct.graphs.azure.com";
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>Executar a aplicação de consola

1. Abra uma janela do terminal e mude (como o comando `cd`) para o diretório de instalação, para o ficheiro package.json que está incluído no projeto.  

2. Execute `npm install` para instalar os módulos npm necessários, incluindo `gremlin-secure`.

3. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

## <a name="browse-with-data-explorer"></a>Procurar com o Data Explorer

Agora, pode voltar ao Data Explorer no portal do Azure para ver, consultar, alterar e trabalhar os dados do gráfico novo.

No Data Explorer, a base de dados nova é apresentada no painel **Coleções**. Expanda **graphdb**, **graphcoll** e clique em **Gráfico**.

Os dados gerados pela aplicação de exemplo são apresentados no painel **Gráficos**.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Apague os seus recursos

Se não planeia continuar a utilizar esta aplicação, elimine todos os recursos que criou neste artigo, fazendo o seguinte: 

1. No menu de navegação do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar uma conta do Azure Cosmos DB, a criar um gráfico com o Data Explorer e a executar uma aplicação. Agora pode criar consultas mais complexas e implementar a lógica de passagem de gráfico através do Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md) (Utilizar Gremlin para consultar)


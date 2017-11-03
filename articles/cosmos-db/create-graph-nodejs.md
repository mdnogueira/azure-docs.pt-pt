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
ms.topic: quickstart
ms.date: 08/29/2017
ms.author: denlee
ms.openlocfilehash: 228d739ac4505d9f16c43bb484dd8050631f084e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-nodejs-application-by-using-graph-api"></a>Azure Cosmos DB: Criar uma aplicação Node.js com o Graph API

BD do Azure do Cosmos é o serviço de base de dados multimodel globalmente distribuída da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este artigo de início rápido demonstra como criar uma conta de base de dados do Azure Cosmos para Graph API (pré-visualização), a base de dados e o gráfico com o portal do Azure. Em seguida, irá criar e executar uma aplicação de consola com o controlador [Gremlin Node.js](https://www.npmjs.com/package/gremlin) «open-source».

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

2. Execute o seguinte comando para clonar o repositório de exemplo: 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Abra o ficheiro da solução no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o `app.js` ficheiros e ver as seguintes linhas de código. 

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

   Se o **Gremlin URI** valor está em branco, pode gerar o valor da **chaves** página no portal. Utilize o **URI** valor, remova https:// e alterar os documentos para gráficos.

   O ponto final do Gremlin deve ser apenas o nome do sistema anfitrião sem o número de porta/protocolo, como `mygraphdb.graphs.azure.com` (não `https://mygraphdb.graphs.azure.com` ou `mygraphdb.graphs.azure.com:433`).

3. Preencha, config.js, o valor de config.primaryKey com o **chave primária** valor a partir do **chaves** página do portal do Azure. 

    `config.primaryKey = "PRIMARYKEY";`

   ![Painel de "Chaves" portal do Azure](./media/create-graph-nodejs/keys.png)

4. Insira o nome da base de dados e o nome de gráfico (contentor) para o valor de config.database e config.collection. 

Eis um exemplo de que o ficheiro de config.js concluída deve ter o seguinte aspeto:

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

2. Execute `npm install` para instalar os módulos npm necessários, incluindo `gremlin`.

3. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

## <a name="browse-with-data-explorer"></a>Procurar com o Data Explorer

Agora, pode voltar ao Data Explorer no portal do Azure para ver, consultar, alterar e trabalhar os dados do gráfico novo.

No Data Explorer, a nova base de dados é apresentada no painel **Gráficos**. Expanda a base de dados, seguido pela coleção e, em seguida, selecione **gráfico**.

Os dados gerados pela aplicação de exemplo são apresentados no painel seguinte dentro de **gráfico** separador Quando seleciona **aplicar filtro**.

Experimente concluir `g.V()` com `.has('firstName', 'Thomas')`, para testar o filtro. Tenha em atenção que o valor é sensível às maiúsculas e minúsculas.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Apague os seus recursos

Se não planeia continuar a utilizar esta aplicação, elimine todos os recursos que criou neste artigo, fazendo o seguinte: 

1. No portal do Azure, no menu de navegação esquerdo, selecione **grupos de recursos**. Em seguida, selecione o nome do recurso que criou. 

2. Na página do grupo de recursos, selecione **Eliminar**. Escreva o nome do recurso que pretende eliminar e, em seguida, selecione **eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar uma conta de base de dados do Azure Cosmos, criar um gráfico com o Explorador de dados e executar uma aplicação. Agora pode criar consultas mais complexas e implementar a lógica de passagem de gráfico através do Gremlin. 

> [!div class="nextstepaction"]
> [Consultar utilizando Gremlin](tutorial-query-graph.md)

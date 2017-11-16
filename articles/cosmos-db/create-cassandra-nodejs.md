---
title: "Início rápido: Cassandra API com o Node.js - Cosmos BD do Azure | Microsoft Docs"
description: "Este guia de introdução mostra como utilizar a API do Azure Cosmos DB Cassandra para criar uma aplicação de perfil com o Node.js"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4732e57d-32ed-40e2-b148-a8df4ff2630d
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: 76850d6403fc4e87e95f5842b87b258d652c2c35
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-and-azure-cosmos-db"></a>Início rápido: Criar uma aplicação de Cassandra com o Node.js e Cosmos BD do Azure

Este guia de introdução mostra como utilizar o Node.js e a base de dados do Azure Cosmos [Cassandra API](cassandra-introduction.md) para criar uma perfil de aplicação através da clonagem de um exemplo do GitHub. Este guia de introdução também o orienta através da criação de uma conta de base de dados do Azure Cosmos através do portal do Azure baseada na web.

BD do Azure do Cosmos é serviço de base de dados com múltiplos modelo global distribuída da Microsoft. Pode criar e consultar documentos, tabela, chave-valor e bases de dados de gráfico, sendo todas beneficiam da distribuição global e as capacidades de dimensionamento horizontal o núcleo da BD do Cosmos Azure rapidamente. 

## <a name="prerequisites"></a>Pré-requisitos

* Acesso para o programa de pré-visualização do Azure Cosmos DB Cassandra API. Se ainda não aplicada para acesso ainda, [inscrever-se agora](https://aka.ms/cosmosdb-cassandra-signup).
* Versão de [Node.js](https://nodejs.org/en/) v0.10.29 ou superior
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Em alternativa, pode [tente BD do Azure do Cosmos gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos.


## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documento, terá de criar uma conta de Cassandra com base de dados do Azure Cosmos.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clone uma API Cassandra aplicação a partir do github, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tais como o git bash e utilize o `cd` comando para alterar para uma pasta para instalar a aplicação de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender a forma como os recursos de base de dados são criados no código, pode rever os seguintes fragmentos. Os fragmentos são obtidos a partir do `uprofile.js` ficheiro na pasta C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started. Caso contrário, pode avançar diretamente para [atualizar a cadeia de ligação](#update-your-connection-string). 

* Nome de utilizador e palavra-passe é definida utilizando a página de cadeia de ligação no portal do Azure. 'path\to\cert' fornece um caminho para um X509 certificado. 

   ```nodejs
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* O `client` é inicializado com contactPoint informações. O contactPoint é obtido a partir do portal do Azure.

    ```nodejs
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* O `client` estabelece ligação com o Azure Cosmos DB Cassandra API.

    ```nodejs
    client.connect(next);
    ```

* É criado um novo keyspace.

    ```nodejs
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* É criada uma nova tabela.

   ```nodejs
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Entidades de chave/valor são introduzidas.

    ```nodejs
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum', '2017-10-3136']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* Consulta para obter obter todos os valores de chaves.

    ```nodejs
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* Consulta para obter um chave-valor.

    ```nodejs
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite que a aplicação comunicar com a base de dados alojada.

1. No [portal do Azure](http://portal.azure.com/), clique em **cadeia de ligação**. 

    Utilize o ![Botão Copiar](./media/create-cassandra-nodejs/copy.png) botão à direita do ecrã, para copiar o valor superior, o ponto de contacto.

    ![Ver e copiar o ponto de contacto, nome de utilizador e palavra-passe da página de cadeia de ligação portal, do Azure](./media/create-cassandra-nodejs/keys.png)

2. Abra o ficheiro `config.js`. 

3. Colar o valor do ponto de contacto do portal através de `<FillMEIN>` linha 4.

    Linha 4 deve agora ter um aspeto semelhante a 

    `config.contactPoint = "cosmos-db-quickstarts.documents.azure.com:10350"`

4. Copie o valor de nome de utilizador a partir do portal e cole-o ao longo do `<FillMEIN>` linha 2.

    Linha 2 deve agora ter um aspeto semelhante a 

    `config.username = 'cosmos-db-quickstart';`
    
5. Copie o valor de palavra-passe a partir do portal e cole-o ao longo do `<FillMEIN>` na linha de 3.

    Linha 3 deve agora ter um aspeto semelhante a

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Guarde o ficheiro de config.js.
    
## <a name="use-the-x509-certificate"></a>Utilizar o X509 certificado 

1. Se precisar de adicionar a entrada de Root da CyberTrust Baltimore, tem o número de série 02:00:00:b9 e SHA1 identificação digital d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Pode ser transferido a partir https://cacert.omniroot.com/bc2025.crt, guardados num ficheiro local com. cer de extensão. 

2. Abra uprofile.js e altere o 'path\to\cert' para apontar para o novo certificado. 

3. Guarde uprofile.js. 

## <a name="run-the-app"></a>Executar a aplicação

1. Na janela de terminal do git, execute `npm install` para instalar os módulos npm necessária.

2. Executar `node uprofile.js` para iniciar a aplicação de nó.

3. Verificar os resultados como esperado na linha de comandos.

    ![Ver e certifique-se a saída](./media/create-cassandra-nodejs/output.png)

    Prima CTRL + C para parar exection do programa e feche a janela de consola. 

    Pode agora abrir o Explorador de dados no portal do Azure para ver a consulta, modificar e trabalhar com estes novos dados. 

    ![Ver os dados no Explorador de dados](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma coleção com o Data Explorer e a executar uma aplicação. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados de Cassandra para a base de dados do Azure Cosmos](cassandra-import-data.md)



---
title: "Início rápido: Cassandra API com Java - Cosmos BD do Azure | Microsoft Docs"
description: "Este guia de introdução mostra como utilizar a API do Azure Cosmos DB Cassandra para criar uma aplicação de perfil com o portal do Azure e o Java"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: ef611081-0195-4ad8-9b54-b313588e5754
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 2097aa1c158f88a06ab93123f4e374b4245430d6
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="quickstart-build-a-cassandra-app-with-java-and-azure-cosmos-db"></a>Início rápido: Criar uma aplicação de Cassandra com Java e Cosmos BD do Azure

Este guia de introdução mostra como utilizar o Java e a base de dados do Azure Cosmos [Cassandra API](cassandra-introduction.md) para criar uma perfil de aplicação através da clonagem de um exemplo do GitHub. Este guia de introdução também o orienta através da criação de uma conta de base de dados do Azure Cosmos através do portal do Azure baseada na web.

BD do Azure do Cosmos é serviço de base de dados com múltiplos modelo global distribuída da Microsoft. Pode criar e consultar documentos, tabela, chave-valor e bases de dados de gráfico, sendo todas beneficiam da distribuição global e as capacidades de dimensionamento horizontal o núcleo da BD do Cosmos Azure rapidamente. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Em alternativa, pode [tente BD do Azure do Cosmos gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos.

Acesso para o programa de pré-visualização do Azure Cosmos DB Cassandra API. Se ainda não aplicada para acesso ainda, [inscrever-se agora](cassandra-introduction.md#sign-up-now).

Além disso: 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Certifique-se de que define a variável de ambiente JAVA_HOME para apontar para a pasta onde está instalado o JDK.
* [Transferir](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um arquivo binário [Maven](http://maven.apache.org/)
    * No Ubuntu, pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, pode executar `sudo apt-get install git` para instalar o Git.



## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documento, terá de criar uma conta de Cassandra com base de dados do Azure Cosmos.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos de clone de uma aplicação Cassandra a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tais como o git bash e utilize o `cd` comando para alterar para uma pasta para instalar a aplicação de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender a forma como os recursos de base de dados são criados no código, pode rever os seguintes fragmentos. Caso contrário, pode avançar diretamente para [atualizar a cadeia de ligação](#update-your-connection-string). Estes fragmentos são obtidos a partir do src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.  

* Cassandra anfitrião, porta, nome de utilizador, palavra-passe e as opções de SSL estão definidas. As informações de cadeia de ligação provêm da página de cadeia de ligação no portal do Azure.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* O `cluster` estabelece ligação ao Azure Cosmos DB Cassandra API e devolve uma sessão para aceder.

    ```java
    return cluster.connect();
    ```

São os seguintes fragmentos do ficheiro src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.

* Crie um novo keyspace.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Crie uma nova tabela.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Inserir entidades de utilizador utilizando um objeto de instrução preparado.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Consulta para obter todas as informações de utilizador.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Consulta para obter informações de um único utilizador.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite que a aplicação comunicar com a base de dados alojada.

1. No [portal do Azure](http://portal.azure.com/), clique em **cadeia de ligação**. 

    ![Ver e copiar um nome de utilizador a partir da página de portal, cadeia de ligação do Azure](./media/create-cassandra-java/keys.png)

2. Utilize o ![Copiar botão](./media/create-cassandra-java/copy.png) botão à direita do ecrã, para copiar o valor do ponto de contacto.

3. Abra o `config.properties` ficheiro a partir da pasta C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources. 

3. Colar o valor do ponto de contacto do portal através de `<Cassandra endpoint host>` linha 2.

    Linha 2 de Properties deve agora ter um aspeto semelhante a 

    `cassandra_host=cosmos-db-quickstarts.documents.azure.com`

3. Volte ao portal e copie o valor de nome de utilizador. Após o valor de nome de utilizador a partir do portal através de `<cassandra endpoint username>` linha 4.

    Linha 4 de Properties deve agora ter um aspeto semelhante a 

    `cassandra_username=cosmos-db-quickstart`

4. Volte ao portal e copie o valor de palavra-passe. Colar o valor de palavra-passe do portal através de `<cassandra endpoint password>` na linha 5.

    Linha 5 de Properties deve agora ter um aspeto semelhante a 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Na linha 6, se pretender utilizar um certificado SSL específico, em seguida, substitua `<SSL key store file location>` com a localização do certificado SSL. Se não for fornecido um valor, o certificado JDK instalado em < JAVA_HOME >/jre/lib/segurança/cacerts é utilizado. 

6. Se tiver alterado linha 6 para utilizar um certificado SSL específico, atualize linha 7 para utilizar a palavra-passe para esse certificado. 

7. Guarde o ficheiro Properties.

## <a name="run-the-app"></a>Executar a aplicação

1. Na janela de terminal do git, `cd` para a pasta azure-cosmosdb-cassandra-java-getting-started\java-examples.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. Na janela de terminal do git, utilize o seguinte comando para gerar o ficheiro cosmosdb-cassandra-examples.jar.

    ```git
    mvn clean install
    ```

3. Na janela de terminal do git, execute o seguinte comando para iniciar a aplicação de Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    A janela de terminal apresenta as notificações que são criados o keyspace e a tabela. Em seguida, seleciona e devolve todos os utilizadores na tabela e apresenta a saída e, em seguida, seleciona uma linha por id e apresenta o valor.  

    Prima CTRL + C para parar exection do programa e feche a janela de consola. 
    
    Pode agora abrir o Explorador de dados no portal do Azure para ver a consulta, modificar e trabalhar com estes novos dados. 

    ![Ver os dados no Explorador de dados](./media/create-cassandra-java/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, aprendeu como criar uma conta de base de dados do Azure Cosmos, Cassandra base de dados e utilizar o Explorador de dados de coleção e executar uma aplicação para fazer a mesma coisa através de programação. Agora pode importar dados adicionais para a coleção de BD do Cosmos do Azure. 

> [!div class="nextstepaction"]
> [Importar dados de Cassandra para a base de dados do Azure Cosmos](cassandra-import-data.md)

---
title: "Azure Cosmos DB: criar uma aplicação de consola da API do MongoDB com o Golang e o portal do Azure | Microsoft Docs"
description: "Apresenta um código Golang de exemplo que pode utilizar para ligar e consultar o Azure Cosmos DB"
services: cosmos-db
author: Durgaprasad-Budhwani
manager: jhubbard
editor: mimig1
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 07/21/2017
ms.author: mimig
ms.openlocfilehash: cb123107178f5e7c0207524c19331a6fa4658739
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-mongodb-api-console-app-with-golang-and-the-azure-portal"></a>Azure Cosmos DB: criar uma aplicação de consola da API do MongoDB com o Golang e o portal do Azure

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB.

Este guia de introdução demonstra como utilizar uma aplicação [MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) existente escrita em [Golang](https://golang.org/) e como ligá-la à base de dados do Azure Cosmos DB, que suporta ligações de cliente da MongoDB.

Por outras palavras, a aplicação Golang sabe apenas que está a ligar a uma base de dados através das APIs do MongoDB. É transparente para a aplicação onde os dados são armazenados no Azure Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- O [Go](https://golang.org/dl/) e conhecimento básico da linguagem [Go](https://golang.org/).
- Um IDE — [Gogland](https://www.jetbrains.com/go/) da Jetbrains, o [Visual Studio Code](https://code.visualstudio.com/) da Microsoft ou o [Atom](https://atom.io/). Neste tutorial, estamos a utilizar o Goglang.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Clone a aplicação de exemplo e instale os pacotes necessários.

1. Crie uma pasta chamada CosmosDBSample dentro da pasta GOROOT\src, que é C:\Go\ por predefinição.
2. Execute o comando seguinte com uma janela de terminal do git, como bash git, para clonar o repositório de exemplo para a pasta CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Execute o seguinte comando para obter o pacote mgo. 

    ```
    go get gopkg.in/mgo.v2
    ```

O controlador [mgo](http://labix.org/mgo) (pronunciado *mango*) é um controlador [MongoDB](http://www.mongodb.org/) para a [linguagem Go](http://golang.org/), que implementa uma seleção avançada e bem testada de funcionalidades, numa API muito simples de acordo com idiomas Go padrão.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação.

1. Clique em **Início rápido** no menu de navegação à esquerda e clique em **Outros** para ver as informações da cadeia de ligação exigidas pela aplicação Go.

2. No Goglang, abra o ficheiro main.go no diretório GOROOT\CosmosDBSample e atualize as seguintes linhas de código com as informações da cadeia de ligação do portal do Azure, conforme mostrado na seguinte captura de ecrã. 

    O nome da Base de Dados é o prefixo do valor do **Anfitrião** no painel da cadeia de ligação do portal do Azure. Para a conta mostrada na imagem abaixo, o nome da Base de Dados é golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Painel Início rápido, separador Outros no portal do Azure, que mostra as informações da cadeia de ligação](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Guarde o ficheiro main.go.

## <a name="review-the-code"></a>Rever o código

Vamos fazer uma breve revisão do que está a acontecer ao ficheiro main.go. 

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Ligar a aplicação Go ao Azure Cosmos DB

O Azure Cosmos DB suporta o MongoDB com capacidade SSL. Para ligar a uma base de dados do MongoDB com capacidade SSL, tem de definir a função **DialServer** em [mgo. DialInfo](http://gopkg.in/mgo.v2#DialInfo)e utilizara função [tls.*Dial*](http://golang.org/pkg/crypto/tls#Dial) para fazer a ligação.

O seguinte fragmento de código Golang liga-se à aplicação Go com a API do MongoDB do Azure Cosmos DB. A classe *DialInfo* contém opções para estabelecer uma sessão com um cluster do MongoDB.

```go
// DialInfo holds options for establishing a session with a MongoDB cluster.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to our Azure Cosmos DB MongoDB database.
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect to mongo, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

O método **mgo.Dial()** é utilizado quando não existir uma ligação SSL. Para uma ligação SSL, o método **mgo.DialWithInfo()** é necessário.

Uma instância do objeto **DialWIthInfo{}** é utilizada para criar o objeto de sessão. Quando a sessão é estabelecida, pode aceder à coleção com o seguinte fragmento de código:

```go
collection := session.DB(“database”).C(“package”)
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Criar um documento

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Consultar ou ler um documento

O Azure Cosmos DB suporta consultas extensas de documentos JSON armazenados em cada coleção. O seguinte código de exemplo mostra uma consulta que pode executar nos documentos da sua coleção.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Atualizar um documento

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Eliminar um documento

O Azure Cosmos DB suporta a eliminação de documentos JSON.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Executar a aplicação

1. No Goglang, certifique-se de que o GOPATH (disponível em **Ficheiro**, **Definições**, **Go**, **GOPATH**) inclui a localização na qual o gopkg foi instalado, que é USERPROFILE\go por predefinição. 
2. Comente as linhas que eliminam o documento, as linhas 91-96, para conseguir ver o documento depois de executar a aplicação.
3. No Goglang, clique em **Executar**e, em seguida, clique em **Executar “Criar main.go e executar”**.

    A aplicação termina e apresenta a descrição do documento criado em [Criar um documento](#create-document).
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Goglang a mostrar a saída da aplicação](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Rever o documento no Data Explorer

Regresse ao portal do Azure para ver o documento no Data Explorer.

1. Clique em **Data Explorer (Pré-visualização)** no menu de navegação à esquerda, expanda **golang-coach**, **pacote**e, em seguida, clique em **Documentos**. No separador **Documentos**, clique no \_id para exibir o documento no painel direito. 

    ![O Data Explorer a mostrar o documento recentemente criado](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Pode então trabalhar com o documento inline e clicar em **Atualizar** para guardá-lo. Também pode eliminar o documento ou criar novos documentos ou consultas.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados com este guia de introdução no portal do Azure com os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB e a executar uma aplicação Golang com a API do MongoDB. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB for the MongoDB API](mongodb-migrate.md) (Importar dados para o Azure Cosmos DB para a MongoDB API)

---
title: "Início rápido: Cassandra API com .NET - Cosmos BD do Azure | Microsoft Docs"
description: "Este guia de introdução mostra como utilizar a API do Azure Cosmos DB Cassandra para criar uma aplicação de perfil com o portal do Azure e o .NET"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 73839abf-5af5-4ae0-a852-0f4159bc00a0
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 2503e7e6025e6f064574f14855468ae9b1b97fa0
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-build-a-cassandra-app-with-net-and-azure-cosmos-db"></a>Início rápido: Criar uma aplicação de Cassandra com .NET e do Azure Cosmos DB

Este guia de introdução mostra como utilizar o .NET e a base de dados do Azure Cosmos [Cassandra API](cassandra-introduction.md) para criar uma perfil de aplicação através da clonagem de um exemplo do GitHub. Este guia de introdução também o orienta através da criação de uma conta de base de dados do Azure Cosmos através do portal do Azure baseada na web.   

BD do Azure do Cosmos é serviço de base de dados com múltiplos modelo global distribuída da Microsoft. Pode criar e consultar documentos, tabela, chave-valor e bases de dados de gráfico, sendo todas beneficiam da distribuição global e as capacidades de dimensionamento horizontal o núcleo da BD do Cosmos Azure rapidamente. 

## <a name="prerequisites"></a>Pré-requisitos

Acesso para o programa de pré-visualização do Azure Cosmos DB Cassandra API. Se ainda não aplicada para acesso ainda, [inscrever-se agora](https://aka.ms/cosmosdb-cassandra-signup).

Se ainda não tiver o Visual Studio 2017, instalado, pode transferir e utilizar o **livre** [edição de Comunidade de 2017 do Visual Studio](https://www.visualstudio.com/downloads/). Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Em alternativa, pode [tente BD do Azure do Cosmos gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos e compromissos.

Instalar [Git](https://www.git-scm.com/) para clonar o exemplo.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos trabalhar com código. Vamos de clone de uma aplicação Cassandra API a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma janela de terminal do git, tais como o git bash e utilize o `cd` comando para alterar para uma pasta para instalar a aplicação de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-getting-started.git
    ```

3. Em seguida, abra o ficheiro de solução CassandraQuickStartSample no Visual Studio. 

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender a forma como os recursos de base de dados são criados no código, pode rever os seguintes fragmentos. Os fragmentos são obtidos a partir do `Program.cs` ficheiro instalado na pasta C:\git-samples\azure-cosmos-db-cassandra-dotnet-getting-started\CassandraQuickStartSample. Caso contrário, pode avançar diretamente para [atualizar a cadeia de ligação](#update-your-connection-string).

* Iniciar a sessão através da ligação a um ponto final de cluster de Cassandra. A API de Cassandra na base de dados do Azure Cosmos suporta apenas TLSv1.2. 

  ```csharp
   var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
   options.SetHostNameResolver((ipAddress) => CassandraContactPoint);
   Cluster cluster = Cluster.Builder().WithCredentials(UserName, Password).WithPort(CassandraPort).AddContactPoint(CassandraContactPoint).WithSSL(options).Build();
   ISession session = cluster.Connect();
   ```

* Crie um novo keyspace.

    ```csharp
    session.Execute("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"); 
    ```

* Crie uma nova tabela.

   ```csharp
  session.Execute("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)");
   ```

* Inserir entidades de utilizador, utilizando o objeto de IMapper com uma nova sessão que liga o keyspace uprofile.

    ```csharp
    mapper.Insert<User>(new User(1, "LyubovK", "Dubai"));
    ```
    
* Consulta para obter informações de todos os utilizadores.

    ```csharp
   foreach (User user in mapper.Fetch<User>("Select * from user"))
   {
      Console.WriteLine(user);
   }
    ```
    
 * Consulta para obter informações de um único utilizador.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. As informações de cadeia de ligação permite que a sua aplicação comunicar com a base de dados alojada.

1. No [portal do Azure](http://portal.azure.com/), clique em **cadeia de ligação**. 

    Utilize o ![Botão Copiar](./media/create-cassandra-dotnet/copy.png) botão à direita do ecrã, para copiar o valor de nome de utilizador.

    ![Ver e copiar uma chave de acesso na página do portal, cadeia de ligação do Azure](./media/create-cassandra-dotnet/keys.png)

2. No Visual Studio 2017, abra o ficheiro Program.cs. 

3. Colar o valor de nome de utilizador do portal através de `<FILLME>` linha 13.

    Linha 13 de Program.cs deve agora ter um aspeto semelhante a 

    `private const string UserName = "cosmos-db-quickstart";`

3. Volte ao portal e copie o valor de palavra-passe. Colar o valor de palavra-passe do portal através de `<FILLME>` linha 14.

    Linha 14 de Program.cs deve agora ter um aspeto semelhante a 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

4. Volte ao portal e copie o valor do ponto de contacto. Colar o valor do ponto de contacto do portal através de `<FILLME>` linha 15.

    Linha 15 de Program.cs deve agora ter um aspeto semelhante a 

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.documents.azure.com"; //  DnsName`

5. Guarde o ficheiro Program.cs.
    
## <a name="run-the-app"></a>Executar a aplicação

1. No Visual Studio, clique em **ferramentas** > **Gestor de pacotes NuGet** > **consola do Gestor de pacotes**.

2. Na linha de comandos, utilize o seguinte comando para instalar o pacote NuGet do controlador de .NET. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Clique em CTRL + F5 para executar a aplicação. Apresenta a aplicação na sua janela de consola. 

    ![Ver e certifique-se a saída](./media/create-cassandra-dotnet/output.png)

    Prima CTRL + C para parar exection do programa e feche a janela de consola. 
    
    Pode agora abrir o Explorador de dados no portal do Azure para ver a consulta, modificar e trabalhar com estes novos dados. 

    ![Ver os dados no Explorador de dados](./media/create-cassandra-dotnet/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia rápido, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma coleção com o Data Explorer e a executar uma aplicação Web. Agora, pode importar dados adicionais à sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados de Cassandra para a base de dados do Azure Cosmos](cassandra-import-data.md)

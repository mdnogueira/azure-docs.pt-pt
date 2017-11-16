---
title: "Utilizar a API da BD do Cosmos do Azure para o MongoDB para criar uma aplicação web | Microsoft Docs"
description: "Um tutorial de base de dados do Azure Cosmos que cria uma aplicação web de base de dados online utilizando a API para MongoDB."
keywords: Exemplos do mongodb
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: f7aed1c69e930bdd5e5b451fa2726a75b78515eb
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB: Ligar a uma aplicação do MongoDB através do .NET

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode criar e consultar rapidamente o documento, a chave/valor e as bases de dados de gráficos, que beneficiam de capacidades de escalamento horizontal e distribuição global no centro do Azure Cosmos DB. 

Este tutorial demonstra como criar uma conta de base de dados do Azure Cosmos no portal do Azure e como criar uma base de dados e coleção para armazenar dados utilizando o [MongoDB API](mongodb-introduction.md). 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB 
> * Atualizar a cadeia de ligação
> * Criar uma aplicação do MongoDB numa máquina virtual 


## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Vamos começar por criar uma conta de base de dados do Azure Cosmos no portal do Azure.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * Já tem uma conta de base de dados do Azure Cosmos? Se assim for, avançar diretamente para [configurar a sua solução Visual Studio](#SetupVS)
> * Se estiver a utilizar o emulador de BD do Cosmos do Azure, siga os passos indicados em [emulador de BD do Azure Cosmos](local-emulator.md) para configurar o emulador e avançar diretamente para [configurar a sua solução do Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

1. No portal do Azure, no **Azure Cosmos DB** página, selecione a API para a conta do MongoDB. 
2. Na barra da esquerda do painel de conta, clique em **início rápido**. 
3. Escolha a sua plataforma (*controlador .NET*, *Node.js controlador*, *MongoDB Shell*, *controlador Java*, *controlador Python*). Se não vir o controlador ou a ferramenta listados, não se preocupe, vamos continuamente o documento mais fragmentos de código de ligação. 
4. Copiar e colar o fragmento de código para a aplicação do MongoDB e está pronto para funcionar.

## <a name="set-up-your-mongodb-app"></a>Configurar a aplicação do MongoDB

Pode utilizar o [criar uma aplicação web no Azure que se liga a MongoDB em execução numa máquina virtual](../app-service/app-service-web-tutorial-nodejs-mongodb-app.md) tutorial, com o mínimo modificação, para configurar rapidamente a uma aplicação de MongoDB (quer localmente ou publicados para uma aplicação web do Azure) que liga a uma API para a conta do MongoDB.  

1. Siga o tutorial, com uma alteração.  Substitua o código de Dal.cs com isto:

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
   
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. Modifique as seguintes variáveis no ficheiro Dal.cs por definições da sua conta da página de chaves no portal do Azure:

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. Utilize a aplicação!

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, siga os passos abaixo para eliminar todos os recursos criados por este tutorial no portal do Azure. 

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB 
> * Atualizar a cadeia de ligação
> * Criar uma aplicação do MongoDB numa máquina virtual

Pode avançar para o próximo tutorial e importar os seus dados de MongoDB à base de dados do Azure Cosmos.  

> [!div class="nextstepaction"]
> [Importar dados da MongoDB para o Azure Cosmos DB](mongodb-migrate.md)


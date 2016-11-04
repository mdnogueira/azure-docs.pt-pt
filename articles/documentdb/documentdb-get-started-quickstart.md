---
title: 'Tutorial NoSQL: SDK do .NET DocumentDB | Microsoft Docs'
description: Um tutorial NoSQL que cria uma base de dados online e uma aplicação de consola C# com o SDK do .NET DocumentDB. O DocumentDB é uma base de dados NoSQL para JSON.
keywords: tutorial nosql, base de dados online, aplicações de consola em c#
services: documentdb
documentationcenter: .net
author: AndrewHoh
manager: jhubbard
editor: monicar

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/01/2016
ms.author: anhoh

---
# Tutorial NoSQL: criar uma consola de aplicação DocumentDB C
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> 
> 

Bem-vindo ao tutorial NoSQL para o SDK .NET do Azure DocumentDB! Depois de obter o projeto QuickStart ou de concluir o tutorial, terá uma aplicação de consola que cria e consulta recursos do DocumentDB.

* **[Início Rápido](#quickstart)**: transfira o projeto de exemplo, adicione as informações da sua ligação e terá a sua aplicação do DocumentDB a funcionar em menos de 10 minutos.
* **[Tutorial](#tutorial)**: crie a aplicação QuickStart do zero em 30 minutos.

## Pré-requisitos
* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* [Visual Studio 2013 ou Visual Studio 2015](http://www.visualstudio.com/).
* .NET Framework 4.6

## QuickStart
1. Transfira o projeto de exemplo .zip do [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started-quickstart/archive/master.zip) ou clone o repo. [documentdb-dotnet-getting-started-quickstart](https://github.com/Azure-Samples/documentdb-dotnet-getting-started-quickstart).
2. Utilize o portal do Azure para [criar uma conta do DocumentDB](documentdb-create-account.md).
3. No ficheiro App.config, substitua os valores de EndpointUri e PrimaryKey pelos valores obtidos no [portal do Azure](https://portal.azure.com/), ao navegar para o painel **DocumentDB (NoSQL)**, clicar no **nome da Conta** e, em seguida, clicar em **Chaves**, no menu do recurso.
    ![Captura de ecrã do valor de EndpointUri e PrimaryKey a substituir no App.config](./media/documentdb-get-started-quickstart/nosql-tutorial-documentdb-keys.png)
4. Crie o projeto. A janela da consola mostra os recursos novos a serem criados, consultados e, depois limpos.
   
    ![Captura de ecrã do resultado da consola](./media/documentdb-get-started-quickstart/nosql-tutorial-documentdb-console-output.png)

## <a id="tutorial"></a>Tutorial
Este tutorial orienta-o ao longo da criação de uma base de dados do DocumentDB, de uma coleção do DocumentDB e de documentos JSON. Posteriormente, vai consultar a coleção e limpar e eliminar a base de dados. Este tutorial constrói o mesmo projeto que o projeto QuickStart, mas vai criá-lo de forma incremental e vai receber explicações sobre o código adicionado ao mesmo.

## Passo 1: Criar uma conta DocumentDB
Criemos uma conta DocumentDB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a sua solução do Visual Studio](#SetupVS).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>Passo 2: Configurar a sua Solução Visual Studio
1. Abra o **Visual Studio 2015** no seu computador.
2. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.
3. Na caixa de diálogo **Novo projeto**, selecione **Modelos** / **Visual C#** / **Aplicação de Consola**, atribua um nome ao projeto e, em seguida, clique em **OK**.
   ![Captura de ecrã da janela Novo Projeto](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. No **Explorador de Soluções**, clique com o botão direito do rato na sua nova aplicação de consola, que está sob a sua solução Visual Studio.
5. Em seguida, sem sair do menu, clique em **Gerir … Pacotes NuGet...**
   ![Faça uma captura de ecrã do Menu do clique direito para o Projeto](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. No separador **Nuget**, clique em **Procurar**, e escreva **azure documentdb** na caixa de pesquisa.
7. Nos resultados, localize **Microsoft.Azure.DocumentDB** e clique em **Instalar**.
   O ID do pacote para a Biblioteca de Cliente do DocumentDB é [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)
   ![Faça uma captura de ecrã do Menu Nuget para encontrar o SDK do Cliente do DocumentDB](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

Ótimo! Agora que concluímos a configuração, comecemos a escrever certos códigos. Pode encontrar um projeto de código completo deste tutorial em [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a>Passo 3: Ligar a uma conta DocumentDB
Primeiro, adicione estas referências ao início da sua aplicação C#, no ficheiro Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> Para concluir este tutorial NoSQL, certifique-se de que adiciona as dependências acima.
> 
> 

Agora, adicione estas duas constantes e a sua variável de *Cliente* por baixo do seu *Programa* de classe pública.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUri = "<your endpoint URI>";
        private const string PrimaryKey = "<your key>";
        private DocumentClient client;

De seguida, vá para o [Portal do Azure](https://portal.azure.com) para obter o seu URI e a chave primária. O URI do DocumentDB e a chave primária são necessários para que a sua aplicação saiba onde ligar e para que o DocumentDB confie na ligação da sua aplicação.

No Portal do Azure, navegue até à sua conta do DocumentDB e clique em **Chaves**.

Copie o URI do portal e cole-o em `<your endpoint URI>`, no ficheiro program.cs. Em seguida, copie A CHAVE PRIMÁRIA do portal e cole-a em `<your key>`.

![Faça uma captura de ecrã do Portal do Azure utilizado pelo tutorial NoSQL para criar uma aplicação de consola C#. Mostra uma conta DocumentDB, com o ACTIVE hub realçado, o botão CHAVES realçado no painel de conta DocumentDB e os valores URI, CHAVE PRIMÁRIA e CHAVE SECUNDÁRIA realçados no painel de Chaves][keys]

Iremos começar a aplicação de introdução pela criação de uma nova instância do **DocumentClient**.

Sob o método **Principal**, adicione esta nova tarefa assíncrona designada **GetStartedDemo**, que irá criar a instância novo **DocumentClient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
    }

Adicione o seguinte código para executar a tarefa assíncrona a partir do seu método **Principal**. O método **Principal** irá capturar as exceções e escrevê-las na consola.

    static void Main(string[] args)
    {
            // ADD THIS PART TO YOUR CODE
            try
            {
                    Program p = new Program();
                    p.GetStartedDemo().Wait();
            }
            catch (DocumentClientException de)
            {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
            }
            catch (Exception e)
            {
                    Exception baseException = e.GetBaseException();
                    Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
            }
            finally
            {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
            }

Prima **F5** para executar a sua aplicação.

Parabéns! Ligou-se com êxito a uma conta DocumentDB. Vejamos agora como trabalhar com recursos do DocumentDB.  

## Passo 4: Criar uma base de dados
Antes de adicionar o código para a criação de uma base de dados, adicione um método de programa auxiliar para escrever na consola.

Copie e cole o método **WriteToConsoleAndPromptToContinue** por baixo do método **GetStartedDemo**.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Pode criar a sua [base de dados](documentdb-resources.md#databases) do DocumentDB utilizando o método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) da classe **DocumentClient**. Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções.

Copie e cole o método **CreateDatabaseIfNotExists** por baixo do método **WriteToConsoleAndPromptToContinue**.

    // ADD THIS PART TO YOUR CODE
    private async Task CreateDatabaseIfNotExists(string databaseName)
    {
            // Check to verify a database with the id=FamilyDB does not exist
            try
            {
                    await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(databaseName));
                    this.WriteToConsoleAndPromptToContinue("Found {0}", databaseName);
            }
            catch (DocumentClientException de)
            {
                    // If the database does not exist, create a new database
                    if (de.StatusCode == HttpStatusCode.NotFound)
                    {
                            await this.client.CreateDatabaseAsync(new Database { Id = databaseName });
                            this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
                    }
                    else
                    {
                            throw;
                    }
            }
    }

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação de cliente. Esta ação irá criar uma base de dados designada *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseIfNotExists("FamilyDB_va");

Prima **F5** para executar a sua aplicação.

Parabéns! Criou uma base de dados DocumentDB com êxito.  

## <a id="CreateColl"></a>Passo 5: Criar uma coleção
> [!WARNING]
> **CreateDocumentCollectionAsync** irá criar uma nova coleção com débito reservado, tendo repercussões sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Pode criar uma [coleção](documentdb-resources.md#collections) utilizando o método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) da classe **DocumentClient**. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.

Copie e cole o método **CreateDocumentCollectionIfNotExists** por baixo do seu método **CreateDatabaseIfNotExists**.

    // ADD THIS PART TO YOUR CODE
    private async Task CreateDocumentCollectionIfNotExists(string databaseName, string collectionName)
    {
        try
        {
            await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
            this.WriteToConsoleAndPromptToContinue("Found {0}", collectionName);
        }
        catch (DocumentClientException de)
        {
            // If the document collection does not exist, create a new collection
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                DocumentCollection collectionInfo = new DocumentCollection();
                collectionInfo.Id = collectionName;

                // Configure collections for maximum query flexibility including string range queries.
                collectionInfo.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

                // Here we create a collection with 400 RU/s.
                await this.client.CreateDocumentCollectionAsync(
                    UriFactory.CreateDatabaseUri(databaseName),
                    collectionInfo,
                    new RequestOptions { OfferThroughput = 400 });

                this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
            }
            else
            {
                throw;
            }
        }
    }

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação da base de dados. Desta forma, é criada uma coleção de documentos com o nome *FamilyCollection_va*.

        this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

        await this.CreateDatabaseIfNotExists("FamilyDB_oa");

        // ADD THIS PART TO YOUR CODE
        await this.CreateDocumentCollectionIfNotExists("FamilyDB_va", "FamilyCollection_va");

Prima **F5** para executar a sua aplicação.

Parabéns! Criou uma coleção de documentos DocumentDB com êxito.  

## <a id="CreateDoc"></a>Passo 6: Criar documentos JSON
Pode criar um [documento](documentdb-resources.md#documents) utilizando o método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) da classe **DocumentClient**. Os documentos são conteúdos (arbitrários) JSON definidos pelo utilizador. Podemos agora inserir um ou mais documentos. Se já tiver dados que pretende armazenar na sua base de dados, pode utilizar a [ferramenta de Migração de Dados](documentdb-import-data.md) do DocumentDB.

Em primeiro lugar, temos de criar uma classe **Família** que irá representar objetos armazenados no DocumentDB neste exemplo. Também iremos criar subclasses **Principal**, **Subordinado**, **Animal de estimação** e **Endereço** utilizadas dentro da **Família**. Tenha em atenção que os documentos têm de ter um **Id** propriedade serializado como **id** no JSON. Crie estas classes ao adicionar as seguintes subclasses internas a seguir ao método **GetStartedDemo**.

Copie e cole as classes **Família**, **Principal**, **Subordinado**, **Animal de estimação**, e **Endereço** por baixo do método **WriteToConsoleAndPromptToContinue**.

    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }

    // ADD THIS PART TO YOUR CODE
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
                return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

Copie e cole o método **CreateFamilyDocumentIfNotExists** por baixo do seu método **CreateDocumentCollectionIfNotExists**.

    // ADD THIS PART TO YOUR CODE
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
            }
            else
            {
                throw;
            }
        }
    }

E insira dois documentos, ou seja um para a Família Andersen e outro para a Família Wakefield.

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação de coleção de documentos.

    await this.CreateDatabaseIfNotExists("FamilyDB_va");

    await this.CreateDocumentCollectionIfNotExists("FamilyDB_va", "FamilyCollection_va");

    // ADD THIS PART TO YOUR CODE
    Family andersenFamily = new Family
    {
            Id = "Andersen.1",
            LastName = "Andersen",
            Parents = new Parent[]
            {
                    new Parent { FirstName = "Thomas" },
                    new Parent { FirstName = "Mary Kay" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FirstName = "Henriette Thaulow",
                            Gender = "female",
                            Grade = 5,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Fluffy" }
                            }
                    }
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", andersenFamily);

    Family wakefieldFamily = new Family
    {
            Id = "Wakefield.7",
            LastName = "Wakefield",
            Parents = new Parent[]
            {
                    new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                    new Parent { FamilyName = "Miller", FirstName = "Ben" }
            },
            Children = new Child[]
            {
                    new Child
                    {
                            FamilyName = "Merriam",
                            FirstName = "Jesse",
                            Gender = "female",
                            Grade = 8,
                            Pets = new Pet[]
                            {
                                    new Pet { GivenName = "Goofy" },
                                    new Pet { GivenName = "Shadow" }
                            }
                    },
                    new Child
                    {
                            FamilyName = "Miller",
                            FirstName = "Lisa",
                            Gender = "female",
                            Grade = 1
                    }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

Prima **F5** para executar a sua aplicação.

Parabéns! Criou dois documentos DocumentDB com êxito.  

![Diagrama que ilustra a relação hierárquica entre a conta, a base de dados online, a coleção e os documentos utilizados pelo tutorial NoSQL na criação da aplicação de consola C#](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Passo 7: Consultar recursos do DocumentDB
O DocumentDB suporta [consultas](documentdb-sql-query.md) extensas contra documentos JSON armazenados em cada coleção.  O seguinte código de exemplo mostra várias consultas - utilizando ambas as sintaxes DocumentDB SQ e LINQ - que podemos procurar nos documentos que inserimos no passo anterior.

Copie e cole o método **ExecuteSimpleQuery** por baixo do seu método **CreateFamilyDocumentIfNotExists**.

    // ADD THIS PART TO YOUR CODE
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

            // Here we find the Andersen family via its LastName
            IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                    .Where(f => f.LastName == "Andersen");

            // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
            Console.WriteLine("Running LINQ query...");
            foreach (Family family in familyQuery)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            // Now execute the same query via direct SQL
            IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                    "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                    queryOptions);

            Console.WriteLine("Running direct SQL query...");
            foreach (Family family in familyQueryInSql)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação do segundo documento.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

Prima **F5** para executar a sua aplicação.

Parabéns! Consultou com êxito uma coleção do DocumentDB.

O seguinte diagrama ilustra como a sintaxe de consulta do DocumentDB SQL é chamada na coleção que criou, sendo a mesma lógica aplicada à consulta LINQ.

![Diagrama que ilustra o âmbito e o significado da consulta utilizada pelo tutorial NoSQL para criar uma aplicação de consola C#](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md#from-clause) é opcional na consulta porque as consultas do DocumentDB já estão confinadas a uma única coleção. Por conseguinte, "FROM Families f" pode ser trocada por "FROM root r" ou qualquer outra variável de nome escolher. Por predefinição, o DocumentDB irá inferir essas Famílias, raiz ou o nome da variável que escolheu, referenciar a atual coleção.

## <a id="ReplaceDocument"></a>Passo 8: Substituir um documento JSON
O DocumentDB suporta a substituição de documentos JSON.  

Copie e cole o método **ReplaceFamilyDocument** por baixo do seu método **ExecuteSimpleQuery**.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
        try
        {
            await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
            this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da execução da consulta. Depois de substituir o documento, este irá executar a mesma consulta par ver o documento alterado.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB_va", "FamilyCollection_va", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

Prima **F5** para executar a sua aplicação.

Parabéns! Substituiu um documento do DocumentDB com êxito.

## <a id="DeleteDocument"></a>Passo 9: Eliminar um documento JSON
O DocumentDB suporta a eliminação de documentos JSON.  

Copie e cole o método **DeleteFamilyDocument** por baixo do seu método **ReplaceFamilyDocument**.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        try
        {
            await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
            Console.WriteLine("Deleted Family {0}", documentName);
        }
        catch (DocumentClientException de)
        {
            throw;
        }
    }

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da segunda execução de consulta.

    await this.ReplaceFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1");

Prima **F5** para executar a sua aplicação.

Parabéns! Eliminou um documento do DocumentDB com êxito.

## <a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados
Eliminar a base de dados criada irá remover a base de dados e todos os recursos subordinados (coleções, documentos, etc.).

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo do documento a eliminar para eliminar a base de dados e todos os recursos subordinados.

    this.ExecuteSimpleQuery("FamilyDB_va", "FamilyCollection_va");

    await this.DeleteFamilyDocument("FamilyDB_va", "FamilyCollection_va", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_va"));

Prima **F5** para executar a sua aplicação.

Parabéns! Eliminou uma base de dados DocumentDB com êxito.

## <a id="Run"></a>Passo 11: Executar a sua aplicação de consola C# em conjunto!
Prima F5 no Visual Studio para criar a aplicação no modo de depuração.

Deverá ver o resultado da sua aplicação Introdução. O resultado apresentará os resultados das consultas que adicionámos e deverá corresponder ao texto de exemplo abaixo.

    Created FamilyDB_va
    Press any key to continue ...
    Created FamilyCollection_va
    Press any key to continue ...
    Created Family Andersen.1
    Press any key to continue ...
    Created Family Wakefield.7
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Replaced Family Andersen.1
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Deleted Family Andersen.1
    End of demo, press any key to exit.

Parabéns! Concluiu este tutorial NoSQL e a sua aplicação de consola C# está a funcionar!

## Passos seguintes
* Quer um tutorial NoSQL ASP.NET MVC mais complexo? Consulte o artigo [Criar uma aplicação Web com o ASP.NET MVC utilizando o DocumentDB](documentdb-dotnet-application.md).
* Pretende testar o dimensionamento e desempenho com o DocumentDB? Consulte o artigo [Testar o Desempenho e o Dimensionamento com o Azure DocumentDB](documentdb-performance-testing.md)
* Saiba como [monitorizar uma conta DocumentDB](documentdb-monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na secção Desenvolver da [página de documentação do DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-criar-conta]: documentdb-create-account.md
[documentdb-gerir]: documentdb-manage.md
[keys]: media/documentdb-get-started-quickstart/nosql-tutorial-keys.png




<!--HONumber=Sep16_HO3-->



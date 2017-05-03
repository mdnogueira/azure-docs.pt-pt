---
title: 'Tutorial NoSQL: SDK do DocumentDB para .NET Core | Microsoft Docs'
description: "Um tutorial NoSQL que cria uma base de dados online e uma aplicação de consola em C# com o SDK do DocumentDB para .NET Core. O DocumentDB é uma base de dados NoSQL para JSON."
services: documentdb
documentationcenter: .net
author: arramac
manager: jhubbard
editor: 
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/28/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a2ebd1181535087dbd33c5088fc495ccebf76e4f
ms.lasthandoff: 03/31/2017


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application-on-net-core"></a>Tutorial NoSQL: criar uma consola de aplicação DocumentDB em C# no .NET Core
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bem-vindo ao tutorial NoSQL para o SDK do Azure DocumentDB para .NET Core! Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do DocumentDB.

Iremos abranger:

* Criação e ligação a uma conta DocumentDB
* Configuração da sua Solução Visual Studio
* Criação de uma base de dados online
* Criação de uma coleção
* Criação de documentos JSON
* Consulta da coleção
* Substituir um documento
* Eliminar um documento
* Eliminar a base de dados

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). Avance para a [secção Obter a solução completa](#GetSolution) para instruções rápidas.

Pretende criar uma aplicação Xamarin iOS, Android ou Formulários com o SDK do DocumentDB .NET Core? Consulte [Developing Xamarin mobile applications using DocumentDB (Programar aplicações móveis Xamarin com o DocumentDB)](documentdb-mobile-apps-with-xamarin.md).

De seguida, utiliza os botões de voto na parte superior ou inferior desta página para enviar os seus comentários. Se preferir que entremos diretamente em contacto consigo, não hesite em indicar o seu endereço de e-mail nos seus comentários.

> [!NOTE]
> O .NET Core SDK do DocumentDB utilizado neste tutorial ainda não é compatível com as aplicações de Plataforma Universal do Windows (UWP). Para obter uma versão de pré-visualização do .NET Core SDK que suporta as aplicações UWP, envie um e-mail para [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

Agora comecemos!

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/). 
    * Em alternativa, pode utilizar o [Emulador do Azure DocumentDB](documentdb-nosql-local-emulator.md) para este tutorial.
* [Visual Studio 2017](https://www.visualstudio.com/vs/) 
    * Se estiver a trabalhar no MacOS ou em Linux, pode desenvolver aplicações .NET Core a partir da linha de comandos ao instalar o [SDK para .NET Core](https://www.microsoft.com/net/core#macos) para uma plataforma à sua escolha. 
    * Se estiver a trabalhar em Windows, pode desenvolver aplicações .NET Core a partir da linha de comandos ao instalar o [SDK para .NET Core](https://www.microsoft.com/net/core#windows). 
    * Pode utilizar o seu próprio editor ou transferir o [Visual Studio Code](https://code.visualstudio.com/), que é gratuito e funciona em Windows, Linux e MacOS. 

## <a name="step-1-create-a-documentdb-account"></a>Passo 1: Criar uma conta DocumentDB
Criemos uma conta DocumentDB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a sua solução do Visual Studio](#SetupVS). Se estiver a utilizar o Emulador do DocumentDB, siga os passos em [Azure DocumentDB Emulator (Emulador do Azure DocumentDB)](documentdb-nosql-local-emulator.md) para configurar o emulador e avance para [Setup your Visual Studio Solution (Configurar a Solução do Visual Studio)](#SetupVS).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>Passo 2: Configurar a sua Solução Visual Studio
1. Abra o **Visual Studio 2017** no seu computador.
2. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.
3. Na caixa de diálogo **Novo Projeto**, selecione **Modelos** / **Visual C#** / **.NET Core**/**Aplicação de Consola (.NET Core)**, atribua ao projeto o nome **DocumentDBGettingStarted**, e clique em **OK**.

   ![Captura de ecrã da janela Novo Projeto](./media/documentdb-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. No **Explorador de Soluções**, clique com o botão direito do rato em **DocumentDBGettingStarted**.
5. Em seguida, sem sair do menu, clique em **Gerir Pacotes NuGet...**.

   ![Captura de ecrã do Menu com Botão do Lado Direito para o Projeto](./media/documentdb-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. No separador **NuGet**, clique em **Procurar** na parte superior da janela, e escreva **azure documentdb** na caixa de pesquisa.
7. Nos resultados, localize **Microsoft.Azure.DocumentDB.Core** e clique em **Instalar**.
   O ID de pacote da Biblioteca de Cliente do DocumentDB para .NET Core é [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Se tiver como destino uma versão do .NET Framework (como net461) que não é suportada por este pacote.NET Core NuGet, utilize o [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) que suporta todas as versões do .NET Framework, desde o .NET Framework 4.5.
8. Nos pedidos, aceite as instalações de pacotes NuGet e o contrato de licença.

Ótimo! Agora que concluímos a configuração, comecemos a escrever certos códigos. Pode encontrar um projeto de código completo deste tutorial em [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Passo 3: Ligar a uma conta do DocumentDB
Primeiro, adicione estas referências ao início da sua aplicação C#, no ficheiro Program.cs:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

> [!IMPORTANT]
> Para concluir este tutorial NoSQL, certifique-se de que adiciona as dependências acima.

Agora, adicione estas duas constantes e a sua variável de *Cliente* por baixo do seu *Programa* de classe pública.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

De seguida, vá para o [Portal do Azure](https://portal.azure.com) para obter o seu URI e a chave primária. O URI do DocumentDB e a chave primária são necessários para que a sua aplicação saiba onde ligar e para que o DocumentDB confie na ligação da sua aplicação.

No Portal do Azure, navegue até à sua conta do DocumentDB e clique em **Chaves**.

Copie o URI do portal e cole-o em `<your endpoint URI>`, no ficheiro program.cs. Em seguida, copie A CHAVE PRIMÁRIA do portal e cole-a em `<your key>`. Se estiver a utilizar o Emulador do Azure DocumentDB, utilize `https://localhost:8081` como o ponto final e a chave de autorização definida especificamente de [How to develop using the DocumentDB Emulator (Como programar com o Emulador do DocumentDB)](documentdb-nosql-local-emulator.md). Certifique-se de que remove o < and >, mas deixe as aspas no ponto final e na chave.

![Faça uma captura de ecrã do Portal do Azure utilizado pelo tutorial NoSQL para criar uma aplicação de consola C#. Mostra uma conta DocumentDB, com o ACTIVE hub realçado, o botão CHAVES realçado no painel de conta DocumentDB e os valores URI, CHAVE PRIMÁRIA e CHAVE SECUNDÁRIA realçados no painel de Chaves][keys]

Iremos começar a aplicação de introdução pela criação de uma nova instância do **DocumentClient**.

Sob o método **Principal**, adicione esta nova tarefa assíncrona designada **GetStartedDemo**, que irá criar a instância novo **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Adicione o seguinte código para executar a tarefa assíncrona a partir do seu método **Principal**. O método **Principal** irá capturar as exceções e escrevê-las na consola.

```csharp
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
```

Prima o botão **DocumentDBGettingStarted** para criar e executar a aplicação.

Parabéns! Ligou-se com êxito a uma conta DocumentDB. Vejamos agora como trabalhar com recursos do DocumentDB.  

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados
Antes de adicionar o código para a criação de uma base de dados, adicione um método de programa auxiliar para escrever na consola.

Copie e cole o método **WriteToConsoleAndPromptToContinue** por baixo do método **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Pode criar a sua [base de dados](documentdb-resources.md#databases) do DocumentDB utilizando o método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) da classe **DocumentClient**. Uma base de dados é o contentor lógico do armazenamento de documentos JSON particionado em coleções.

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação de cliente. Esta ação irá criar uma base de dados designada *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Prima o botão **DocumentDBGettingStarted** para executar a aplicação.

Parabéns! Criou uma base de dados DocumentDB com êxito.  

## <a id="CreateColl"></a>Passo 5: Criar uma coleção
> [!WARNING]
> **CreateDocumentCollectionAsync** irá criar uma nova coleção com débito reservado, tendo repercussões sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).

Pode criar uma [coleção](documentdb-resources.md#collections) utilizando o método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) da classe **DocumentClient**. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação da base de dados. Desta forma, é criada uma coleção de documentos com o nome *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.CreateDatabaseIfNotExists("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Prima o botão **DocumentDBGettingStarted** para executar a aplicação.

Parabéns! Criou uma coleção de documentos DocumentDB com êxito.  

## <a id="CreateDoc"></a>Passo 6: Criar documentos JSON
Pode criar um [documento](documentdb-resources.md#documents) utilizando o método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) da classe **DocumentClient**. Os documentos são conteúdos (arbitrários) JSON definidos pelo utilizador. Podemos agora inserir um ou mais documentos. Se já tiver dados que pretende armazenar na sua base de dados, pode utilizar a [ferramenta de Migração de Dados](documentdb-import-data.md) do DocumentDB.

Em primeiro lugar, temos de criar uma classe **Família** que irá representar objetos armazenados no DocumentDB neste exemplo. Também iremos criar subclasses **Principal**, **Subordinado**, **Animal de estimação** e **Endereço** utilizadas dentro da **Família**. Tenha em atenção que os documentos têm de ter um **Id** propriedade serializado como **id** no JSON. Crie estas classes ao adicionar as seguintes subclasses internas a seguir ao método **GetStartedDemo**.

Copie e cole as classes **Família**, **Principal**, **Subordinado**, **Animal de estimação**, e **Endereço** por baixo do método **WriteToConsoleAndPromptToContinue**.

```csharp
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
```

Copie e cole o método **CreateFamilyDocumentIfNotExists** por baixo do seu método **CreateDocumentCollectionIfNotExists**.

```csharp
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
```

E insira dois documentos, ou seja um para a Família Andersen e outro para a Família Wakefield.

Copie e cole o código a seguir a `// ADD THIS PART TO YOUR CODE` no método **GetStartedDemo**, por baixo da criação da coleção de documentos.

```csharp
await this.CreateDatabaseIfNotExists("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Prima o botão **DocumentDBGettingStarted** para executar a aplicação.

Parabéns! Criou dois documentos DocumentDB com êxito.  

![Diagrama que ilustra a relação hierárquica entre a conta, a base de dados online, a coleção e os documentos utilizados pelo tutorial NoSQL na criação da aplicação de consola C#](./media/documentdb-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Passo 7: Consultar os recursos do DocumentDB
O DocumentDB suporta [consultas](documentdb-sql-query.md) extensas contra documentos JSON armazenados em cada coleção.  O seguinte código de exemplo mostra várias consultas - utilizando ambas as sintaxes DocumentDB SQ e LINQ - que podemos procurar nos documentos que inserimos no passo anterior.

Copie e cole o método **ExecuteSimpleQuery** por baixo do seu método **CreateFamilyDocumentIfNotExists**.

```csharp
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
```

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da criação do segundo documento.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Prima o botão **DocumentDBGettingStarted** para executar a aplicação.

Parabéns! Consultou com êxito uma coleção do DocumentDB.

O seguinte diagrama ilustra como a sintaxe de consulta do DocumentDB SQL é chamada na coleção que criou, sendo a mesma lógica aplicada à consulta LINQ.

![Diagrama que ilustra o âmbito e o significado da consulta utilizada pelo tutorial NoSQL para criar uma aplicação de consola C#](./media/documentdb-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md#FromClause) é opcional na consulta porque as consultas do DocumentDB já estão confinadas a uma única coleção. Por conseguinte, "FROM Families f" pode ser trocada por "FROM root r" ou qualquer outra variável de nome escolher. Por predefinição, o DocumentDB irá inferir essas Famílias, raiz ou o nome da variável que escolheu, referenciar a atual coleção.

## <a id="ReplaceDocument"></a>Passo 8: Substituir um documento JSON
O DocumentDB suporta a substituição de documentos JSON.  

Copie e cole o método **ReplaceFamilyDocument** por baixo do seu método **ExecuteSimpleQuery**.

```csharp
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
```

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da execução da consulta. Depois de substituir o documento, este irá executar a mesma consulta par ver o documento alterado.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Prima o botão **DocumentDBGettingStarted** para executar a aplicação.

Parabéns! Substituiu um documento do DocumentDB com êxito.

## <a id="DeleteDocument"></a>Passo 9: Eliminar um documento JSON
O DocumentDB suporta a eliminação de documentos JSON.  

Copie e cole o método **DeleteFamilyDocument** por baixo do seu método **ReplaceFamilyDocument**.

```csharp
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
```

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo da segunda execução de consulta.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Prima o botão **DocumentDBGettingStarted** para executar a aplicação.

Parabéns! Eliminou um documento do DocumentDB com êxito.

## <a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados
Eliminar a base de dados criada irá remover a base de dados e todos os recursos subordinados (coleções, documentos, etc.).

Copie e cole o seguinte código no seu método **GetStartedDemo**, por baixo do documento a eliminar para eliminar a base de dados e todos os recursos subordinados.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Prima o botão **DocumentDBGettingStarted** para executar a aplicação.

Parabéns! Eliminou uma base de dados DocumentDB com êxito.

## <a id="Run"></a>Passo 11: Executar a sua aplicação de consola C# em conjunto!
Prima o botão **DocumentDBGettingStarted** no Visual Studio para criar a aplicação no modo de depuração.

Deverá ver o resultado da sua aplicação Introdução. O resultado apresentará os resultados das consultas que adicionámos e deverá corresponder ao texto de exemplo abaixo.

```
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
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
```

Parabéns! Concluiu este tutorial NoSQL e a sua aplicação de consola C# está a funcionar!

## <a id="GetSolution"></a>Obter a solução completa do tutorial NoSQL
Para criar a solução GetStarted que contém todos os exemplos deste artigo, deverá ter o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* Uma [conta do DocumentDB][documentdb-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) está disponível no GitHub.

Para restaurar as referências ao SDK do DocumentDB para .NET Core no Visual Studio, clique com o botão direito do rato na solução **GetStarted**, no Explorador de Soluções, e clique em **Ativar Restauro do Pacote NuGet**. Em seguida, no ficheiro Program.cs, atualize os valores EndpointUrl e AuthorizationKey, conforme descrito em [Ligar a uma conta do DocumentDB](#Connect).

## <a name="next-steps"></a>Passos seguintes
* Quer um tutorial NoSQL ASP.NET MVC mais complexo? Consulte o artigo [Criar uma aplicação Web com o ASP.NET MVC utilizando o DocumentDB](documentdb-dotnet-application.md).
* Pretende programar uma aplicação Xamarin iOS, Android ou Formulários com o SDK do DocumentDB .NET Core? Consulte [Developing Xamarin mobile applications using DocumentDB (Programar aplicações móveis Xamarin com o DocumentDB)](documentdb-mobile-apps-with-xamarin.md).
* Pretende testar o dimensionamento e desempenho com o DocumentDB? Consulte o artigo [Testar o Desempenho e o Dimensionamento com o Azure DocumentDB](documentdb-performance-testing.md)
* Saiba como [monitorizar uma conta DocumentDB](documentdb-monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na secção Desenvolver da [página de documentação do DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-dotnetcore-get-started/nosql-tutorial-keys.png


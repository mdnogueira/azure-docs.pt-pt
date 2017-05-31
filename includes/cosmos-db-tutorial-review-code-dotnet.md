Vamos fazer uma breve revisão do que está a acontecer à aplicação. Abra o ficheiro DocumentDBRepository.cs e verá que estas linhas de código criam os recursos do DocumentDB. 

* O DocumentClient é inicializado.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* É criada uma nova base de dados.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* É criada uma nova coleção.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```

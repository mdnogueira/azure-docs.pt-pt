---
title: "Como gerir escritas em simultâneo a recursos na Azure Search"
description: "Utilize simultaneidade otimista para evitar colisões ondas intermédio actualizações ou eliminações índices de pesquisa do Azure, indexadores e origens de dados."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: aee1b7376d4829e3e2f5a232525e3c3cb4df9d8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Como gerir simultaneidade na Azure Search

Ao gerir os recursos de pesquisa do Azure, como índices e origens de dados, é importante atualizar recursos de forma segura, especialmente se aceder aos recursos em simultâneo por vários componentes da aplicação. Quando dois clientes em simultâneo atualizar um recurso sem coordenação, condições race são possíveis. Para evitar esta situação, pesquisa do Azure oferece uma *modelo de simultaneidade otimista*. Existem não existem bloqueios num recurso. Em vez disso, não há uma ETag para cada recurso que identifica a versão de recurso, de modo a que pode craft pedidos evitar acidental substitui.

> [!Tip]
> Código conceptual um [c# solução de exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) explica como funciona o controlo de simultaneidade na Azure Search. O código cria as condições que invocam o controlo de simultaneidade. Ler a [fragmento de código abaixo](#samplecode) é provavelmente suficiente para a maior parte dos programadores, mas se pretender executar, editar appsettings.json para adicionar o nome do serviço e uma chave de api de administração. -Lhe fornecido um URL de serviço de `http://myservice.search.windows.net`, é o nome do serviço `myservice`.

## <a name="how-it-works"></a>Como funciona

É implementada simultaneidade otimista através do acesso condição verifica nas chamadas API ao escrever no índices, indexadores, origens de dados e recursos de synonymMap. 

Todos os recursos de ter um [ *tag de entidade (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) que fornece informações de versão do objeto. Marcando a ETag pela primeira vez, pode evitar atualizações em simultâneo num fluxo de trabalho normal (obter, modificar localmente, atualizar), assegurando que corresponde à ETag do recurso a sua cópia local. 

+ A API de REST utiliza um [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no cabeçalho do pedido.
+ O SDK .NET define o ETag através de um objeto de accessCondition, definir o [If-Match | O cabeçalho If-Match-nenhuma](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no recurso. Qualquer objeto herdar [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) tem um objeto de accessCondition.

Sempre que atualizar um recurso, a ETag alterado automaticamente. Quando implementar a gestão de simultaneidade, tudo o que estiver a fazer é colocar pré-condição o pedido de atualização que requer que o recurso remoto ter o mesmo ETag como a cópia do recurso que modificada no cliente. Se um processo em simultâneo tiver sido alterado o recurso remoto já, o ETag não corresponderá a pré-condição e o pedido irá falhar com HTTP 412. Se estiver a utilizar o SDK .NET, isto manifestos como um `CloudException` onde o `IsAccessConditionFailed()` método de extensão devolve true.

> [!Note]
> Não há apenas um mecanismo de concorrência. É sempre utilizado, independentemente da que API é utilizada para atualizações de recursos. 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Casos de utilização e o código de exemplo

O código seguinte demonstra accessCondition verifica a existência de operações de atualização de chave:

+ Efetuar uma atualização se o recurso já não existe
+ Efetuar uma atualização se altera a versão de recurso

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Código de exemplo [DotNetETagsExplainer programa](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2, 
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Padrão de conceção

Um padrão de conceção para a implementação deve incluir um ciclo que repete a condição de acesso a simultaneidade otimista verificar, um teste para a condição de acesso e, opcionalmente, obtém um recurso atualizado antes de tentar voltar a aplicar as alterações. 

Este fragmento de código ilustra a adição de um synonymMap para um índice que já existe. Este código é a partir de [sinónimo (pré-visualização) c# tutorial de Azure Search](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk). 

O fragmento obtém o índice "Hotéis", verifica a versão do objeto de uma operação de atualização, emite uma exceção se a condição falhar e, em seguida, repetem a operação (até três vezes), começando com a obtenção de índice do servidor para obter a versão mais recente.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>Passos seguintes

Reveja o [exemplo sinónimos c#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) para o contexto mais sobre como atualizar em segurança um índice existente.

Tente modificar qualquer uma das seguintes exemplos para incluir objetos ETags ou AccessCondition.

+ [Exemplo de REST API no Github](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [Exemplo de SDK do .NET no Github](https://github.com/Azure-Samples/search-dotnet-getting-started). Esta solução inclui o projeto "DotNetEtagsExplainer" que contém o código apresentado neste artigo.

## <a name="see-also"></a>Consultar também

  [Pedido e resposta cabeçalhos de HTTP comuns](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)    
  [Códigos de estado HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) [índice operações (REST API)](https://docs.microsoft.com/\rest/api/searchservice/index-operations)
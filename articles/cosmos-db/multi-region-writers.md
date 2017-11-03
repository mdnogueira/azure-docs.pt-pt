---
title: "Arquiteturas de base de dados com múltiplos mestra com a base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba mais sobre como conceber arquiteturas de aplicações com locais leituras e escritas em várias regiões geográficas com base de dados do Azure Cosmos."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf1482ae7b1070023703f5dbe861d151f5d64fd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Mestre multi replicado globalmente arquiteturas de base de dados com base de dados do Azure Cosmos
BD do Azure do Cosmos suporta chave na mão [replicação global](distribute-data-globally.md), que permite-lhe distribuir dados para várias regiões com acesso de latência baixa em qualquer lugar na carga de trabalho. Este modelo é geralmente utilizado para cargas de trabalho do publicador/consumidor onde existe um escritor numa única região geográfica e leitores globalmente distribuídos noutras regiões (leitura). 

Também pode utilizar o suporte de replicação global de BD Cosmos do Azure para criar aplicações no qual escritores e leitores globalmente distribuídos. Este documento descreve um padrão que permite a alcançar escrita local e acesso de leitura local para escritores distribuídas através do Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publicar conteúdo - um cenário de exemplo
Vamos ver um cenário do mundo real para descrever como pode utilizar padrões globalmente distribuída várias-region/várias-master leitura escrita com a base de dados do Azure Cosmos. Considere uma plataforma de publicação conteúda incorporada numa base de dados do Azure Cosmos. Seguem-se alguns requisitos que esta plataforma tem de cumprir para uma experiência de utilizador excelente para consumidores e Publicadores.

* Autores e os subscritores são distribuídos pelo mundo 
* Os autores tem de publicar artigos (escrita) para os respetivos região local (mais próximo)
* Os autores tem leitores/subscritores dos respetivos artigos são distribuídos em todo o mundo. 
* Os subscritores devem receber uma notificação quando novos artigos são publicados.
* Subscritores tem de conseguir ler artigos da sua região local. Também deve ser capazes de adicionar revisões para estes artigos. 
* Qualquer pessoa, incluindo o autor dos artigos deve ser capaz de ver todas as revisões exposta artigos a partir de uma região local. 

Partindo do princípio de milhões de consumidores e publicadores com billions dos artigos, logo que temos de confront os problemas de dimensionamento, juntamente com guaranteeing localidade de acesso. Tal como acontece com a maior parte dos problemas de escalabilidade, a solução situam-se num boa estratégia de criação de partições. Em seguida, vamos ver como artigos, reveja e notificações como documentos de modelo, configurar contas de base de dados do Azure Cosmos e implementar uma camada de acesso de dados. 

Se quiser saber mais sobre a criação de partições e chaves de partição, consulte o artigo [criação de partições e dimensionamento do BD Azure Cosmos](partition-data.md).

## <a id="ModelingNotifications"></a>Notificações de modelação
As notificações são feeds de dados específicos a um utilizador. Por conseguinte, os padrões de acesso para documentos notificações são sempre no contexto de utilizador único. Por exemplo, seria "publicar uma notificação para um utilizador" ou "obter todas as notificações para um determinado utilizador". Por isso, a escolha ideal de chave para este tipo de criação de partições seria `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Modelação subscrições
É possível criar subscrições para vários critérios como uma categoria específica de artigos de interesse ou de um fabricante específico. Por conseguinte, o `SubscriptionFilter` é uma boa opção para a chave de partição.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Artigos de modelação
Assim que for identificado um artigo através de notificações, consultas subsequentes normalmente baseiam o `Article.Id`. Escolher `Article.Id` como partição a chave, por conseguinte, fornece a melhor distribuição para armazenar os artigos dentro de uma coleção de BD do Cosmos do Azure. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Modelação revê
Como artigos, revisões são principalmente escritos e ler no contexto do artigo. Escolher `ArticleId` como uma partição chave fornece melhor distribuição e acesso eficiente de revisões associado artigo. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Métodos de camada de acesso de dados
Agora vamos ver os dados principais métodos de acesso, temos de implementar. Eis a lista de métodos de que o `ContentPublishDatabase` tem de:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Configuração de conta do Cosmos BD do Azure
Para garantir local lê e escreve, é necessário de partição dados não apenas na partição chave, mas também com base num padrão de acesso geográfica em regiões. O modelo baseia-se em ter uma conta de base de dados de base de dados do Azure Cosmos georreplicação para cada região. Por exemplo, com duas regiões, segue-se um programa de configuração para escritas de multirregião:

| Nome da Conta | Escrever região | Região de leitura |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

O diagrama seguinte mostra como leituras e escritas são efetuadas numa aplicação com este programa de configuração típica:

![Arquitetura de multi-mestre Cosmos BD do Azure](./media/multi-region-writers/multi-master.png)

Eis um fragmento de código que mostra como iniciar, os clientes de uma camada DAL em execução no `West US` região.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Com a configuração anterior, a camada de acesso de dados pode reencaminhar todas as operações de escrita para a conta local, com base nos quais tenha sido implementado. Leituras são efetuadas durante a leitura do ambas as contas para obter a vista de dados global. Esta abordagem pode ser expandida para regiões tantos conforme necessário. Por exemplo, é uma configuração com três regiões geográficas aqui:

| Nome da Conta | Escrever região | Ler região 1 | Ler região 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Implementação de camada de acesso de dados
Agora vamos ver a implementação de camada de acesso de dados (DAL) para uma aplicação com duas regiões graváveis. A camada DAL tem de implementar os seguintes passos:

* Criar várias instâncias do `DocumentClient` para cada conta. Com duas regiões, cada instância da camada DAL tem um `writeClient` e um `readClient`. 
* Com base na região da aplicação implementada, configure os pontos finais para `writeclient` e `readClient`. Por exemplo, a camada DAL implementada no `West US` utiliza `contentpubdatabase-usa.documents.azure.com` para efetuar operações de escrita. A camada DAL implementado na `NorthEurope` utiliza `contentpubdatabase-europ.documents.azure.com` para escritas.

Com a configuração anterior, os métodos de acesso de dados podem ser implementados. Escrever operações reencaminhar a escrever na correspondente `writeClient`.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Para ler as notificações e revisões, é necessário de leitura de regiões e union os resultados conforme mostrado no seguinte fragmento:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Assim, ao escolher uma chave de criação de partições bom e, em seguida, estático baseado em conta a criação de partições, pode conseguir escritas locais multirregião e lê a utilizar a base de dados do Azure Cosmos.

## <a id="NextSteps"></a>Passos seguintes
Neste artigo, vamos descrito como pode utilizar padrões de escrita de leitura de multirregião globalmente distribuída com o Azure Cosmos BD utilizando a publicação de conteúdos como um cenário de exemplo.

* Saiba mais sobre como base de dados do Azure Cosmos suporta [distribuição global](distribute-data-globally.md)
* Saiba mais sobre [automáticas e manual as ativações pós-falha na base de dados do Azure Cosmos](regional-failover.md)
* Saiba mais sobre [global consistência com a base de dados do Azure Cosmos](consistency-levels.md)
* Desenvolver com várias regiões, utilizando o [BD do Cosmos Azure - DocumentDB API](tutorial-global-distribution-documentdb.md)
* Desenvolver com várias regiões, utilizando o [Azure Cosmos DB - API do MongoDB](tutorial-global-distribution-MongoDB.md)
* Desenvolver com várias regiões, utilizando o [Azure Cosmos DB - API de tabela](tutorial-global-distribution-table.md)

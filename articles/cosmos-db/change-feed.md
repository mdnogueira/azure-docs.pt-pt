---
title: "Suporte de feed de trabalhar com a alteração do BD Azure Cosmos | Microsoft Docs"
description: "Utilize o suporte de feed de alteração de base de dados do Azure Cosmos para controlar as alterações em documentos e executar processamento baseadas em eventos como acionadores e manter os sistemas de caches e análise atualizado."
keywords: "Alteração do feed"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 10/30/2017
ms.author: arramac
ms.openlocfilehash: e21925b8f84f0805f41fd698965cac09286b92c1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Trabalhar com a alteração de feed de suporte do BD Azure Cosmos

[BD do Azure do Cosmos](../cosmos-db/introduction.md) são um fast e flexível globalmente replicados base de dados adequada para IoT, revenda, jogos e aplicações de registo operacional. É um padrão de conceção comuns nestas aplicações ao utilizar as alterações dos dados para iniciar ações adicionais. Estas ações adicionais pode ser qualquer um dos seguintes procedimentos: 

* Acionar uma notificação ou uma chamada a uma API, quando um documento é inserido ou modificado.
* Fluxo de processamento de IoT ou efetuar a análise.
* Movimento de dados adicionais por sincronizar com uma cache, o motor de busca ou do armazém de dados ou arquivar dados para armazenamento de frio.

O **alteração feed suporte** do BD Azure Cosmos permite-lhe criar soluções eficientes e dimensionáveis para cada um desses padrões, conforme mostrado na imagem seguinte:

![Utilizar base de dados do Azure Cosmos alteração feed análise em tempo real de energia e condicionada por eventos cenários de computação](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Alteração de feed de suporte é fornecida para todos os contentores do BD Azure Cosmos e modelos de dados. No entanto, o feed de alteração de leitura com o cliente do DocumentDB e serializa itens em formato JSON. Devido ao JSON de formatação, MongoDB, os clientes irão sentir um erro de correspondência entre documentos BSON formatado e o JSON com formato alteração feed. 

## <a name="how-does-change-feed-work"></a>Como a alteração feed trabalho?

Altere o feed suporte na base de dados do Azure Cosmos funciona através da escuta de uma coleção de BD do Cosmos do Azure para efetuar quaisquer alterações. -Lo, em seguida, devolve a lista ordenada de documentos que foram alteradas na ordem em que foram modificadas. As alterações são mantidas, podem ser processados no modo assíncrono e incremental e a saída pode ser distribuída por um ou mais consumidores para processamento paralelo. 

Pode ler a alteração do feed de três formas diferentes, tal como explicado posteriormente neste artigo:

1.  [Utilizar as funções do Azure](#azure-functions)
2.  [Utilizar o Azure Cosmos DB SDK](#rest-apis)
3.  [Utilizar a biblioteca de processador de Feed de alteração do Azure Cosmos DB](#change-feed-processor)

O feed de alteração está disponível para cada intervalo de chave de partição dentro da coleção de documentos e, por conseguinte, pode ser distribuído por um ou mais consumidores para processamento paralelo conforme mostrado na imagem seguinte.

![Feed de processamento distribuído de alteração de base de dados do Azure Cosmos](./media/change-feed/changefeedvisual.png)

Detalhes adicionais:
* O feed de alteração está ativado por predefinição para todas as contas.
* Pode utilizar o [débito aprovisionado](request-units.md) na sua região de escrita ou qualquer [ler região](distribute-data-globally.md) ler a partir da alteração do feed, tal como qualquer outra operação de base de dados do Azure Cosmos.
* O feed de alteração inclui inserções e operações de atualização efetuadas para documentos dentro da coleção. Pode capturar eliminações ao definir um sinalizador de "eliminar de forma recuperável" dentro os documentos em vez de eliminações. Em alternativa, pode definir um período de expiração finita para os documentos através de [capacidade TTL](time-to-live.md), por exemplo, 24 horas e utilize o valor da propriedade para capturar as eliminações. Com esta solução, tem de processar alterações de dentro de um intervalo de tempo mais curto que o período de expiração do valor TTL.
* Cada alteração a um documento é apresentada exatamente uma vez na alteração do feed, e clientes gerir os respetivos lógica de pontos de verificação. A biblioteca de processador de feed de alteração fornece pontos de verificação automático e ", pelo menos, uma vez" semântica.
* A alteração mais recente para um determinado documento está incluída no registo de alterações. Alterações intermédias poderão não estar disponíveis.
* O feed de alteração é ordenado by order of modificação dentro de cada valor de chave de partição. Não há nenhuma ordem garantida entre os valores de chave de partição.
* As alterações podem ser sincronizadas a partir de qualquer ponto no tempo, ou seja, não existe nenhum período de retenção de dados fixa para o qual as alterações estão disponíveis.
* As alterações estão disponíveis em segmentos de intervalos de chaves de partição. Esta capacidade permite alterações de coleções de grandes dimensões para ser processados em paralelo pelos vários consumidores/servidores.
* As aplicações podem pedir vários feeds de alteração em simultâneo na mesma coleção.

## <a name="use-cases-and-scenarios"></a>Cenários e casos de utilização

O feed de alteração permite o processamento eficiente de grandes conjuntos de dados com um grande volume de escritas e oferece uma alternativa à consulta de um conjunto completo de dados para identificar o que mudou. 

Por exemplo, com uma alteração do feed, pode realizar as seguintes tarefas de forma eficiente:

* Atualize uma cache, o índice de pesquisa ou um armazém de dados com dados armazenados na base de dados do Azure Cosmos.
* Implementar dados ao nível da aplicação e o arquivo de camadas, ou seja, armazenar "dados" na base de dados do Azure Cosmos e expira "dados amovíveis" para [Blob Storage do Azure](../storage/common/storage-introduction.md) ou [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementar a análise de lote em dados utilizando [do Apache Hadoop](run-hadoop-with-hdinsight.md).
* Execute zero migrações de tempo inferior para outra conta de base de dados do Azure Cosmos com um esquema de partições diferente.
* Implementar [pipelines de lambda no Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) com base de dados do Azure Cosmos. BD do Cosmos do Azure fornece uma solução de bases de dados dimensionável que pode processar ingestão e consulta e implementa arquiteturas de lambda com TCO baixa. 
* Receber e armazenar dados de eventos de dispositivos, sensores, infraestrutura e de aplicações e processar esses eventos em tempo real com [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md), ou [Apache O Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

A imagem seguinte mostra como pipelines de lambda que ambos ingestão e pode utilizar a consulta utilizando a base de dados do Azure Cosmos alteram feed suporte: 

![Pipeline de lambda com base em Cosmos BD do Azure para ingestão e consulta](./media/change-feed/lambda.png)

Além disso, no seu [sem servidor](http://azure.com/serverless) web apps e móveis, pode controlar eventos, tais como as alterações ao seu cliente Perfil, as preferências ou localização para acionar determinadas ações como enviar notificações push para os respetivos dispositivos utilizando [As funções do azure](#azure-functions). Se estiver a utilizar a base de dados do Azure Cosmos para criar um jogo, pode, por exemplo, utilize alterar o feed para implementar leaderboards em tempo real com base no pontuações de jogos concluídas.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Utilizar as funções do Azure 

Se estiver a utilizar as funções do Azure, é a forma mais simples para ligar a um feed de alteração de BD do Cosmos do Azure adicionar um acionador de base de dados do Azure Cosmos à sua aplicação de funções do Azure. Quando cria um acionador de BD do Cosmos Azure numa aplicação das funções do Azure, selecione a coleção de BD do Cosmos do Azure para ligar a e a função é acionada sempre que for efetuada uma alteração à coleção. 

Acionadores que podem ser criados no portal das funções do Azure, no portal do Azure Cosmos DB ou através de programação. Para obter mais informações, consulte [BD do Cosmos do Azure: através das funções do Azure de computação de base de dados sem servidor](serverless-computing-database.md).

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>Utilizando o SDK

O [DocumentDB SDK](documentdb-sdk-dotnet.md) para a BD do Cosmos Azure dá-lhe potência para ler e gerir uma alteração do feed. Mas com energia excelente inclui muitas das responsabilidades, demasiado. Se pretender gerir pontos de verificação, lidar com os números de sequência de documento e ter um controlo granular sobre chaves de partição, em seguida, utilizando o SDK pode estar a abordagem à direita.

Esta secção explica como utilizar o SDK do DocumentDB para trabalhar com uma alteração do feed.

1. Inicie o lendo os seguintes recursos da appconfig. Instruções sobre como obter a chave de autorização e de ponto final estão disponíveis no [atualizar a cadeia de ligação](create-documentdb-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Crie o cliente da seguinte forma:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Obter a partição de intervalos de chaves:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Chamada ExecuteNextAsync para cada intervalo de chave de partição:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

Se tiver vários leitores, pode utilizar **ChangeFeedOptions** distribuam a carga de leitura para diferentes threads ou diferentes clientes.

E que é, com estes algumas linhas de código pode começar a ler o feed de alteração. Pode obter o código de conclusão utilizado neste artigo o [repositório do GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

No código no passo 4 acima, o **ResponseContinuation** nos últimos linha tem o último número de sequência lógica (LSN) do documento, que irá utilizar na próxima vez que o leia novos documentos após o seguinte número de sequência. Utilizando o **StartTime** do **ChangeFeedOption** pode alargar o net para obter os documentos. Se Sim, o **ResponseContinuation** é nulo, mas o **StartTime** ficar atrás no tempo, em seguida, irá obter todos os documentos que foram alterados desde o **StartTime**. No entanto, se sua **ResponseContinuation** tem um valor, em seguida, o sistema irá ajudá-lo a todos os documentos desde que LSN.

Por isso, a matriz de ponto de verificação apenas consiste em manter o LSN para cada partição. Mas, se não quiser lidar com as partições, pontos de verificação, LSN, a hora de início, etc. a opção mais simples consiste em utilizar a biblioteca de processador de Feed de alteração.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Utilizar a biblioteca de processador de Feed de alteração 

O [biblioteca de processador de Feed de alteração do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed) podem ajudar a distribuir facilmente o processamento de eventos por vários consumidores. Esta biblioteca simplifica as alterações de leitura em partições e de vários threads funcionam em paralelo.

A vantagem principal da biblioteca de processador de Feed de alteração é que não tem de gerir cada partição e o token de continuação e não tem de consultar manualmente cada coleção.

A biblioteca de processador de Feed de alteração simplifica as alterações de leitura em partições e de vários threads funcionam em paralelo.  Gere automaticamente as alterações de leitura em partições utilizando um mecanismo de concessão. Como pode ver na imagem seguinte, se iniciar duas clientes que utilizam a biblioteca de processador de Feed de alteração, se dividem o trabalho entre si próprios. Como continuar a aumentar os clientes, estes mantém dividindo o trabalho entre si próprios.

![Feed de processamento distribuído de alteração de base de dados do Azure Cosmos](./media/change-feed/change-feed-output.png)

O cliente esquerdo foi iniciado primeiro e iniciar a monitorização de todas as partições, em seguida, o cliente segundo foi iniciado e, em seguida, o primeiro permitem aceder de algumas das concessões a segunda cliente. Como pode ver esta é a forma nice para distribuir trabalho entre vários computadores e os clientes.

Tenha em atenção que se tiver dois funtions de Azure sem servidor a mesma coleção de monitorização e utilizar o mesmo período de concessão, em seguida, as duas funções podem obter documentos diferentes consoante o modo como a biblioteca de processador decide processs as partições.

### <a name="understanding-the-change-feed-processor-library"></a>Noções sobre a biblioteca de processador de Feed de alteração

Existem quatro componentes principais de implementar o processador de Feed de alteração: a coleção monitorizada, a coleção de concessão, o anfitrião do processador e os consumidores. 

> [!WARNING]
> Criar uma coleção tem implicações, de preços são reservar o débito da aplicação para comunicar com a base de dados do Azure Cosmos. Para obter mais detalhes, visite o [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Coleção de monitorizado:** a coleção monitorizada é os dados a partir do qual o feed de alteração é gerado. Qualquer inserções e as alterações à coleção monitorizada são refletidas do feed de alteração da coleção. 

**Coleção de concessão:** as coordenadas de coleção de concessão processar a alteração entre vários workers do feed. Uma coleção separada é utilizada para armazenar as concessões com uma concessão por partição. É vantajoso para armazenar esta coleção de concessão numa conta diferente com a região de escrita próximo para onde o processador de Feed de alteração está em execução. Um objeto de concessão contém os seguintes atributos: 
* Proprietário: Especifica o anfitrião que é proprietário da concessão
* Continuação: Especifica a posição (token de continuação) para uma determinada partição do feed de alterações do
* Timestamp: Hora da última concessão foi atualizada; o carimbo pode ser utilizado para verificar se é considerada a concessão expirou 

**Anfitrião do processador:** cada anfitrião determina quantos partições para o processo com base em quantos outras instâncias dos anfitriões têm concessões ativas são. 
1.  Quando um anfitrião é iniciado, adquire concessões para balancear a carga de trabalho em todos os anfitriões. Um anfitrião periodicamente é renovada de concessões de, pelo que concessões permanecem ativos. 
2.  Pontos de verificação um anfitrião do token de continuação último para a concessão para cada ler. Para garantir a segurança de simultaneidade, o anfitrião verifica ETag para cada atualização de concessão. Também são suportadas outras estratégias de ponto de verificação.  
3.  Após encerramento, um anfitrião versões todas as concessões, mas mantém as informações de continuação, para que possa retomar ler a partir do ponto de verificação armazenado mais tarde. 

Neste momento, o número de anfitriões não pode ser superior ao número de partições (concessões).

**Os consumidores:** consumidores ou técnicos, existem threads que efetuam o processamento de alteração feed iniciado por cada anfitrião. Cada anfitrião do processador pode ter vários consumidores. Cada consumidor lê a alteração do feed da partição que está atribuído à e notifica o respetivo anfitrião de alterações e expirado concessões.

Para compreender melhor como estes quatro elementos do processador de Feed de alteração de trabalham em conjunto, vamos ver um exemplo no diagrama seguinte. A coleção monitorizada armazena documentos e utiliza a "Cidade" como a chave de partição. Vemos que a partição azul contém documentos com o campo "Cidade" de "I A" e assim sucessivamente. Existem dois anfitriões, cada um com dois os consumidores de ler a partir de quatro partições em paralelo. As setas mostram os consumidores ler a partir de um lugar para cima específico na alteração do feed. Na primeira partição, o darker azul representa alterações unread enquanto o leve azul representa as alterações efetuadas já leitura a alteração do feed. Os anfitriões de utilizam a coleção de concessão para armazenar um valor de "continuação" para controlar a posição actual de leitura para cada consumidor. 

![Utilizar a alteração da base de dados do Azure Cosmos feed anfitrião do processador](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Trabalhar com a biblioteca de processador de Feed de alteração

Antes de instalar o pacote NuGet do processador de Feed de alteração, instale primeiro: 

* Microsoft.Azure.DocumentDB, versão 1.13.1 ou superior 
* Newtonsoft, versão 9.0.1 ou superior

Em seguida, instale o [pacote Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) e incluí-la como uma referência.

Para implementar a biblioteca de processador de Feed de alteração que precisa de executar os seguintes:

1. Implementar um **DocumentFeedObserver** objeto que implementa **IChangeFeedObserver**.

2. Implementar um **DocumentFeedObserverFactory**, que implementa um **IChangeFeedObserverFactory**.

3. No **CreateObserver** método **DocumentFeedObserverFacory**, instanciar o **ChangeFeedObserver** que criou no passo 1 e devolvê-la.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Instanciar **DocumentObserverFactory**.

5. Instanciar um **ChangeFeedEventHost**:

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Registar o **DocumentFeedObserverFactory** com o anfitrião.

O código para os passos 4 a 6 é: 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

Já está. Após estes passos alguns documentos iniciará entra o **DocumentFeedObserver ProcessChangesAsync** método.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como utilizar a base de dados do Azure Cosmos com as funções do Azure, consulte [BD do Cosmos do Azure: através das funções do Azure de computação de base de dados sem servidor](serverless-computing-database.md).

Para obter mais informações sobre como utilizar a biblioteca de processador de Feed de alteração, utilize os seguintes recursos:

* [Página de informações](documentdb-sdk-dotnet-changefeed.md) 
* [Pacote Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Código de exemplo que mostra os passos acima 1-6](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Exemplos adicionais no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Para obter mais informações sobre como utilizar a alteração do feed através do SDK, utilize os seguintes recursos:

* [Página de informações do SDK](documentdb-sdk-dotnet.md)

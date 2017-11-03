---
title: "Tutorial de distribuição global Cosmos BD do Azure para a API do DocumentDB | Microsoft Docs"
description: "Saiba como configurar a base de dados do Azure Cosmos distribuição global utilizando a API do DocumentDB."
services: cosmos-db
keywords: "distribuição global, o documentdb"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 0ba30ca4687248a27d9fe72acdc65a95114a437f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-documentdb-api"></a>Como configurar a base de dados do Azure Cosmos distribuição global utilizando a API do DocumentDB

Neste artigo, vamos mostrar como utilizar o portal do Azure para distribuição global do Azure Cosmos DB de configuração e, em seguida, ligue-se utilizando a API do DocumentDB.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global utilizando o [APIs do DocumentDB](documentdb-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-documentdb-api"></a>Ligar a uma região preferencial utilizando a API do DocumentDB

Para tirar o máximo partido das [distribuição global](distribute-data-globally.md), aplicações de cliente podem especificar a lista de preferência ordenada de regiões para ser utilizado para executar operações de documento. Isto pode ser feito ao definir a política de ligação. Com base na configuração da conta de base de dados do Azure Cosmos, disponibilidade regional atual e a lista de preferência especificado, o ponto final ideal será selecionado pelo SDK do DocumentDB para efetuar a escrita e operações de leitura.

Esta lista de preferência é especificada ao inicializar uma ligação com os SDKs do DocumentDB. Os SDKs aceitam um parâmetro opcional "PreferredLocations" que é uma lista ordenada de regiões do Azure.

O SDK irá enviar automaticamente todas as operações de escrita para o atual escrever região.

Todas as leituras serão enviadas para a região disponível primeiro na lista PreferredLocations. Se falhar o pedido, o cliente irá falhar para baixo a lista para a região seguinte e assim sucessivamente.

Os SDKs só irão tentar ler a partir de regiões especificadas no PreferredLocations. Por isso, por exemplo, se a conta de base de dados está disponível em quatro regiões, mas o cliente especifica apenas duas regiões read(non-write) para PreferredLocations, em seguida, nenhum leituras serão servidas fora da região de leitura que não foram especificada no PreferredLocations. Se as regiões leitura especificadas no PreferredLocations não estiverem disponíveis, serão servidas leituras fora de região de escrita.

A aplicação pode verificar o ponto final de escrita atual e ler o ponto final escolhido pelo SDK, verificando duas propriedades WriteEndpoint e ReadEndpoint, disponível na versão 1.8 do SDK e acima.

Se a propriedade PreferredLocations não estiver definida, serão servidos todos os pedidos de região de escrita atual.

## <a name="net-sdk"></a>SDK .NET
O SDK pode ser utilizado sem quaisquer alterações de código. Neste caso, o SDK automaticamente direciona ambas as leituras e escritas para a região de escrita atual.

Na versão 1.8 e mais tarde do .NET SDK, o parâmetro ConnectionPolicy para o construtor de DocumentClient tem uma propriedade denominada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Esta propriedade é do tipo de coleção `<string>` e deve conter uma lista de nomes de região. Os valores de cadeia são formatados pela coluna de nome de região no [regiões do Azure] [ regions] página, sem espaços antes ou depois do primeiro e último caráter, respetivamente.

A escrita atual e os pontos finais de leitura estão disponíveis em DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint respetivamente.

> [!NOTE]
> Os URLs para os pontos finais não devem ser considerados como constantes de longa duração. O serviço pode atualizar estes em qualquer momento. O SDK processa automaticamente esta alteração.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS, JavaScript e SDKs de Python
O SDK pode ser utilizado sem quaisquer alterações de código. Neste caso, o SDK irá encaminhar automaticamente região de escrita de leituras e escritas para o atual.

Na versão 1.8 e posterior de cada SDK, o parâmetro ConnectionPolicy para o construtor de DocumentClient uma nova propriedade denominada DocumentClient.ConnectionPolicy.PreferredLocations. Este é o parâmetro é uma matriz de cadeias que utiliza uma lista de nomes de região. Os nomes são formatados pela coluna de nome de região no [regiões do Azure] [ regions] página. Também pode utilizar as constantes predefinidas no objeto conveniência AzureDocuments.Regions

A escrita atual e os pontos finais de leitura estão disponíveis em DocumentClient.getWriteEndpoint e DocumentClient.getReadEndpoint respetivamente.

> [!NOTE]
> Os URLs para os pontos finais não devem ser considerados como constantes de longa duração. O serviço pode atualizar estes em qualquer momento. O SDK processará automaticamente esta alteração.
>
>

Segue-se um exemplo de código para NodeJS/Javascript. Python e Java seguirá o mesmo padrão.

```java
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="rest"></a>REST
Depois de uma conta de base de dados disponibilizada em várias regiões, os clientes podem consultar a disponibilidade do mesmo através de um pedido GET no URI seguinte.

    https://{databaseaccount}.documents.azure.com/

O serviço irá devolver uma lista de regiões e os respetivos correspondente Azure Cosmos DB ponto final URIs para as réplicas. A região de escrita atual será indicada na resposta. O cliente, em seguida, pode selecionar o ponto final adequado para obter todos os pedidos de REST API da seguinte forma.

Exemplo de resposta

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Pedidos PUT, POST e DELETE tem de ir para a operação de escrita indicada URI
* Obtém todas as e outros pedidos de só de leitura (por exemplo consultas) podem ir para qualquer ponto final da preferência do cliente

Escreva irão falhar pedidos de regiões de só de leitura com o código de erro HTTP 403 ("proibido").

Se a região de escrita for alterada após a fase de deteção inicial do cliente, escreve subsequentes para a região de escrita anterior irá falhar com o código de erro HTTP 403 ("proibido"). O cliente, em seguida, deve obter a lista de regiões novamente para obter a região de escrita atualizado.

Que é, que conclui neste tutorial. Pode saber como gerir a consistência da sua conta replicada globalmente o lendo [níveis de consistência na base de dados do Azure Cosmos](consistency-levels.md). E para obter mais informações sobre a replicação de base de dados como global funciona do BD Azure Cosmos, consulte [distribuir dados globalmente com a base de dados do Azure Cosmos](distribute-data-globally.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com as APIs do DocumentDB

Agora pode avançar para o próximo tutorial para saber como desenvolver localmente, utilizando o emulador local da base de dados do Azure Cosmos.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/


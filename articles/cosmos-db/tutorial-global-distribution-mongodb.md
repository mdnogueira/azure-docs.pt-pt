---
title: "Tutorial de distribuição global do Cosmos BD do Azure para MongoDB API | Microsoft Docs"
description: "Saiba como configurar a base de dados do Azure Cosmos distribuição global utilizando a API do MongoDB."
services: cosmos-db
keywords: "distribuição global, MongoDB"
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
ms.openlocfilehash: 1689374d180e9c806516812d6d4b51df3b06cb21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Como configurar a base de dados do Azure Cosmos distribuição global utilizando a API do MongoDB

Neste artigo, vamos mostrar como utilizar o portal do Azure para distribuição global do Azure Cosmos DB de configuração e, em seguida, ligue-se utilizando a API do MongoDB.

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global utilizando o [API do MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>Verificar a configuração regional utilizando a API do MongoDB
A forma mais simples de duplo a verificar a configuração global dentro da API para MongoDB está a ser executado o *isMaster()* comando da Shell do Mongo.

A partir da Shell do Mongo:

   ```
      db.isMaster()
   ```
   
Resultados de exemplo:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Ligar a uma região preferencial utilizando a API do MongoDB

A API do MongoDB permite-lhe especificar preferência de leitura da coleção para uma base de dados globalmente distribuída. Para ambos baixa latência leituras e global elevada disponibilidade, recomendamos a definição de preferência de leitura da coleção para *mais próximo*. Uma preferência de leitura do *mais próximo* está configurado para a região mais próxima de leitura.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Para aplicações com um site primário de leitura/escritam região e uma região secundária para a recuperação após desastre (DR) cenários, recomendamos que a definição de preferência de leitura da coleção para *secundário preferido*. Uma preferência de leitura do *secundário preferido* está configurado para ler a partir da região secundária quando a região primária não está disponível.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Por último, se pretende manualmente especificar as regiões de leitura. Pode definir a região Tag dentro da sua preferência de leitura.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Que é, que conclui neste tutorial. Pode saber como gerir a consistência da sua conta replicada globalmente o lendo [níveis de consistência na base de dados do Azure Cosmos](consistency-levels.md). E para obter mais informações sobre a replicação de base de dados como global funciona do BD Azure Cosmos, consulte [distribuir dados globalmente com a base de dados do Azure Cosmos](distribute-data-globally.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global com as APIs do DocumentDB

Agora pode avançar para o próximo tutorial para saber como desenvolver localmente, utilizando o emulador local da base de dados do Azure Cosmos.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador](local-emulator.md)

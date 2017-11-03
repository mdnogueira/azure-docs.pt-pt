---
title: "Tutorial de distribuição global do Cosmos BD do Azure para a Graph API | Microsoft Docs"
description: "Saiba como configurar a base de dados do Azure Cosmos distribuição global utilizando a API de gráfico."
services: cosmos-db
keywords: "distribuição global, gráfico, gremlin"
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.openlocfilehash: d05295f6b952209e6df88d9015153893fbbb9903
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Como configurar a base de dados do Azure Cosmos distribuição global utilizando a API do gráfico

Neste artigo, vamos mostrar como utilizar o portal do Azure para a configuração distribuição global da BD do Cosmos do Azure e, em seguida, ligue-se utilizando a API do gráfico (pré-visualização).

Este artigo abrange as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global com o portal do Azure
> * Configurar a distribuição global utilizando o [gráfico APIs](graph-introduction.md) (pré-visualização)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Ligar a uma região preferencial a utilizar a Graph API utilizando o SDK .NET

A Graph API é exposta como uma biblioteca de extensão sobre o SDK do DocumentDB.

Para tirar o máximo partido das [distribuição global](distribute-data-globally.md), aplicações de cliente podem especificar a lista de preferência ordenada de regiões para ser utilizado para executar operações de documento. Isto pode ser feito ao definir a política de ligação. Com base na configuração da conta de base de dados do Azure Cosmos, disponibilidade regional atual e a lista de preferência especificado, o ponto final ideal será selecionado pelo SDK para efetuar a escrita e operações de leitura.

Esta lista de preferência é especificada ao inicializar uma ligação com os SDKs. Os SDKs aceitam um parâmetro opcional "PreferredLocations" que é uma lista ordenada de regiões do Azure.

* **Escreve**: O SDK irá enviar automaticamente todas as operações de escrita para o atual escrever região.
* **Lê**: todas as leituras serão enviadas para a região disponível primeiro na lista PreferredLocations. Se falhar o pedido, o cliente irá falhar para baixo a lista para a região seguinte e assim sucessivamente. Os SDKs só irão tentar ler a partir de regiões especificadas no PreferredLocations. Por isso, por exemplo, se a conta de base de dados do Cosmos está disponível em três regiões, mas o cliente especifica apenas dois as regiões de escrita para PreferredLocations, em seguida, nenhum leituras serão servidas fora da região de escrita, mesmo em caso de ativação pós-falha.

A aplicação pode verificar o ponto final de escrita atual e ler o ponto final escolhido pelo SDK, verificando duas propriedades WriteEndpoint e ReadEndpoint, disponível na versão 1.8 do SDK e acima. Se a propriedade PreferredLocations não estiver definida, serão servidos todos os pedidos de região de escrita atual.

### <a name="using-the-sdk"></a>Utilizando o SDK

Por exemplo, no SDK do .NET, o `ConnectionPolicy` parâmetro para o `DocumentClient` construtor tem uma propriedade denominada `PreferredLocations`. Esta propriedade pode ser definida para uma lista de nomes de região. Os nomes a apresentar para [regiões do Azure] [ regions] pode ser especificado como parte da `PreferredLocations`.

> [!NOTE]
> Os URLs para os pontos finais não devem ser considerados como constantes de longa duração. O serviço pode atualizar estes em qualquer momento. O SDK processa automaticamente esta alteração.
>
>

```cs
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

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
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

[regions]: https://azure.microsoft.com/regions/


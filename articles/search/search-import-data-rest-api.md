---
title: Carregar dados (API REST - Azure Search) | Microsoft Docs
description: "Saiba como carregar dados para um índice na Pesquisa do Azure utilizando a API REST."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: 8d0749fb-6e08-4a17-8cd3-1a215138abc6
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: f22a33ed86fbfc46dfa732239263a49f34c4afee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Importar dados para a Pesquisa do Azure utilizando a API REST
> [!div class="op_single_selector"]
>
> * [Descrição geral](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

Este artigo irá mostrar como utilizar a [API REST da Pesquisa do Azure](https://docs.microsoft.com/rest/api/searchservice/) para importar dados para um índice da Pesquisa do Azure.

Antes de iniciar estas instruções, já deverá ter [criado um índice de Pesquisa do Azure](search-what-is-an-index.md).

Para enviar documentos para o índice utilizando a API REST, irá emitir um pedido de HTTP POST ao ponto final do URL do seu índice. O corpo do corpo do pedido de HTTP é um objeto JSON que contém os documentos a serem adicionados, modificados ou eliminados.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificar a sua chave de API do administrador do serviço Azure Search
Quando emitir pedidos de HTTP relativamente ao seu serviço utilizando a API REST *cada* pedido da API tem de incluir a chave de API que foi gerada para o serviço de Pesquisa que aprovisionou. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

1. Para localizar as chaves de API do seu serviço, pode iniciar sessão no [portal do Azure](https://portal.azure.com/)
2. Aceda ao painel do seu serviço Azure Search
3. Clique no ícone "Chaves"

O seu serviço terá *chaves de administração* e *chaves de consulta*.

* As suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade para gerir o serviço, criar e eliminar índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária caso opte por voltar a gerar a chave principal e vice-versa.
* As suas *chaves de consulta* concedem acesso só de leitura para índices e documentos e, normalmente, são distribuídas por aplicações cliente que emitem pedidos de pesquisa.

Para o efeito de importação de dados, pode utilizar tanto a chave de administrador principal como a secundária.

## <a name="decide-which-indexing-action-to-use"></a>Decidir a ação de indexação a utilizar
Ao utilizar a API REST, irá emitir pedidos de HTTP POST com corpos de pedido JSON para o URL de ponto final do seu índice de Pesquisa do Azure. O objeto JSON no corpo do pedido de HTTP irá conter uma única matriz JSON com o nome "valor", que contém objetos JSON que representam os documentos que pretende adicionar ao seu índice, atualizar ou eliminar.

Cada objeto JSON na matriz "valor" representa um documento a ser indexado. Cada um destes objetos contém a chave do documento e especifica a ação de indexação pretendida (carregar, intercalar, eliminar, etc.). Dependendo das ações que escolher abaixo, apenas determinados campos tem de ser incluídos para cada documento:

| @search.action | Descrição | Campos necessários para cada documento | Notas |
| --- | --- | --- | --- |
| `upload` |Um ação `upload` é semelhante a um "upsert" onde o documento será inserido se for novo e atualizado/substituído se já existir. |chave, juntamente com quaisquer outros campos que pretende definir |Quando atualizar/substituir um documento existente, qualquer campo que não está especificado no pedido terá o respetivo campo definido como `null`. Isto ocorre mesmo quando o campo foi anteriormente definido para um valor não nulo. |
| `merge` |Atualiza um documento existente com os campos especificados. Se o documento não existe no índice, a intercalação irá falhar. |chave, juntamente com quaisquer outros campos que pretende definir |Qualquer campo que especifique numa intercalação irá substituir o campo existente no documento. Isto inclui campos do tipo `Collection(Edm.String)`. Por exemplo, se o documento contém um campo `tags` com o valor `["budget"]` e executar uma intercalação com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Não será `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Esta ação tem o mesmo comportamento de `merge` caso um documento com a chave especificada já exista no índice. Se o documento não existir, tem um comportamento semelhante `upload` a um novo documento. |chave, juntamente com quaisquer outros campos que pretende definir |- |
| `delete` |Remove o documento especificado do índice. |apenas chave |Quaisquer campos que especificar diferentes do campo de chave serão ignorados. Se pretender remover um campo individual de um documento, utilize `merge` em vez disso e simplesmente defina o campo explicitamente como nulo. |

## <a name="construct-your-http-request-and-request-body"></a>Construir o seu pedido de HTTP e corpo do pedido
Agora que recolheu os valores de campo necessários para as suas ações de índice, está pronto para construir o pedido de HTTP real e o corpo do pedido JSON para importar os dados.

#### <a name="request-and-request-headers"></a>Pedido e Cabeçalhos do Pedido
No URL, é necessário fornecer o nome do serviço, o nome do índice ("hotéis", bem como a versão de API correta (no momento de publicação deste documento a versão de API atual é `2016-09-01`. Terá de definir o `Content-Type` e os cabeçalhos de pedido `api-key`. Para esta última opção, utilize uma das chaves de administração do seu serviço.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Corpo do Pedido
```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Neste caso, podemos utilizar `upload`, `mergeOrUpload`, e `delete` como as nossas ações de pesquisa.

Partamos do princípio que este índice "hotéis" de exemplo já está preenchido com vários documentos. Observe como não tivemos de especificar todos os campos de documentos possíveis quando utilizamos `mergeOrUpload` e como especificamos apenas a chave do documento (`hotelId`) ao utilizar `delete`.

Além disso, tenha em atenção que só pode incluir até 1000 documentos (ou 16 MB) num único pedido de indexação.

## <a name="understand-your-http-response-code"></a>Compreender o código de resposta HTTP
#### <a name="200"></a>200
Depois de submeter um pedido de indexação com êxito, receberá uma resposta HTTP com o código de estado de `200 OK`. O corpo JSON da resposta HTTP será o seguinte:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Um código de estado de `207` vai ser devolvido quando pelo menos um item não foi indexado com êxito. O corpo JSON da resposta HTTP irá incluir informações sobre os documentos sem êxito.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> Muitas vezes, isto significa que a carga no seu serviço de pesquisa está a atingir um ponto onde os pedidos de indexação pedidos irão começar a devolver `503` respostas. Neste caso, recomendamos vivamente que desative o código de cliente e aguarde antes de tentar novamente. Isto irá dar ao sistema algum tempo para recuperar, aumentando a possibilidade de êxito de pedidos futuros. Ao repetir rapidamente os seus pedidos só irá prolongar a situação.
>
>

#### <a name="429"></a>429
Um código de estado de `429` será devolvido quando excedeu a quota no número de documentos por índice.

#### <a name="503"></a>503
Um código de estado de `503` será devolvido se nenhum dos itens no pedido for indexado com êxito. Este erro significa que o sistema está com muita carga e não é possível processar o pedido neste momento.

> [!NOTE]
> Neste caso, recomendamos vivamente que desative o código de cliente e aguarde antes de tentar novamente. Isto irá dar ao sistema algum tempo para recuperar, aumentando a possibilidade de êxito de pedidos futuros. Ao repetir rapidamente os seus pedidos só irá prolongar a situação.
>
>

Para obter mais informações sobre ações de documentos e as respostas de erros/com êxito, consulte o artigo [Adicionar, Atualizar ou Eliminar documentos](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents). Para obter mais informações sobre outros códigos de estado HTTP que possam ser devolvidos em caso de falha, consulte [Códigos de estado HTTP (Pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

## <a name="next-steps"></a>Passos seguintes
Depois de preencher o seu índice da Azure Search, estará pronto para começar a emitir consultas para procurar documentos. Consulte o artigo [Consultar o Índice da Azure Search](search-query-overview.md) para obter detalhes.

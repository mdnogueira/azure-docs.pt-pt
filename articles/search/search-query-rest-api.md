---
title: "Consultar um índice (API REST - Azure Search) | Microsoft Docs"
description: "Crie uma consulta de pesquisa na pesquisa do Azure e utilize parâmetros de pesquisa para filtrar e ordenar os resultados da pesquisa."
services: search
documentationcenter: 
manager: jhubbard
author: ashmaka
ms.assetid: 8b3ca890-2f5f-44b6-a140-6cb676fc2c9c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/12/2017
ms.author: ashmaka
ms.openlocfilehash: 49062bec233ad35cd457f9665fa94c1855343582
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="query-your-azure-search-index-using-the-rest-api"></a>Consultar o índice da Azure Search através da API REST
> [!div class="op_single_selector"]
>
> * [Descrição geral](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

Este artigo mostra-lhe como consultar um índice com a [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

Antes de iniciar estas instruções, já deverá ter [criado um índice de Pesquisa do Azure](search-what-is-an-index.md) e [preenchido este com dados](search-what-is-data-import.md). Para obter informações gerais, veja [Como funciona a pesquisa de texto completa do Azure Search](search-lucene-query-architecture.md).

## <a name="identify-your-azure-search-services-query-api-key"></a>Identificar a sua chave de API de consulta do serviço Azure Search
Um elemento-chave de qualquer operação de pesquisa em relação à API REST de Azure Search é a *chave de API* gerada para o serviço prestado por si. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

1. Para localizar as chaves de API do seu serviço, pode iniciar sessão no [portal do Azure](https://portal.azure.com/)
2. Aceda ao painel do seu serviço Azure Search
3. Clique no ícone "Chaves"

O seu serviço tem *chaves de administração* e *chaves de consulta*.

* As suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade para gerir o serviço, criar e eliminar índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária caso opte por voltar a gerar a chave principal e vice-versa.
* As suas *chaves de consulta* concedem acesso só de leitura para índices e documentos e, normalmente, são distribuídas por aplicações cliente que emitem pedidos de pesquisa.

Para efeitos de consulta de um índice, pode utilizar uma das chaves de consulta. As chaves de administração também podem ser utilizadas para consultas, contudo, deve utilizar uma chave de consulta no código da aplicação, uma vez que tal segue melhor o [Princípio de menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="formulate-your-query"></a>Formular a consulta
Existem duas formas de [pesquisar o índice através da API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Uma forma consiste em emitir um pedido de HTTP POST, em que os parâmetros da sua consulta são definidos num objeto JSON no corpo do pedido. A outra forma consiste em emitir um pedido de HTTP GET, em que os parâmetros da consulta são definidos no URL do pedido. O POST tem [limites mais flexíveis](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) em relação ao tamanho dos parâmetros de consulta do que o GET. Por este motivo, recomendamos a utilização do POST, salvo se tiver circunstâncias especiais em que a utilização do GET seja mais conveniente.

Para o POST e o GET, é necessário fornecer o *nome do serviço*, o *nome do índice* e a *versão de API* correta (no momento de publicação deste documento a versão de API atual é `2016-09-01`) no URL do pedido. No GET, os parâmetros de consulta são fornecidos na *cadeia de consulta* no final do URL. Veja a seguir o formato URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2016-09-01

O formato para o POST é o mesmo, contudo, apenas com a versão de API nos parâmetros de cadeia de consulta.

#### <a name="example-queries"></a>Consultas de exemplo
Eis algumas consultas de exemplo sobre um índice designado "hotéis". Estas consultas são apresentadas nos formatos GET e POST.

Procure no índice completo o termo "orçamento" e devolva apenas o campo `hotelName`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "budget",
    "select": "hotelName"
}
```

Aplique um filtro ao índice para localizar hotéis com um preço inferior a 150 $ por noite e devolva `hotelId` e `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Procure no índice completo, ordene através de um campo específico (`lastRenovationDate`) por ordem descendente, utilize os dois resultados principais e mostre apenas `hotelName` e `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="submit-your-http-request"></a>Submeter o pedido de HTTP
Agora que formulou a consulta como parte do URL (para o GET) ou do corpo (para o POST) do pedido de HTTP, pode definir os cabeçalhos do pedido e submeter a consulta.

#### <a name="request-and-request-headers"></a>Pedido e Cabeçalhos do Pedido
Tem de definir dois cabeçalhos do pedido para o GET ou três para o POST:

1. O cabeçalho `api-key` deve ser definido para a chave de consulta indicada no passo I acima. Também pode utilizar uma chave de administração como o cabeçalho `api-key`, mas recomenda-se a utilização de uma chave de consulta, uma vez que esta concede exclusivamente acesso só de leitura aos índices e documentos.
2. O cabeçalho `Accept` deve ser definido para `application/json`.
3. Para o POST apenas, o cabeçalho `Content-Type` deve ser igualmente definido para `application/json`.

Veja a seguir um pedido de HTTP GET para pesquisar o índice "hotéis" com a API REST do Azure Search através de uma consulta simples que procura o termo "hotel":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2016-09-01
Accept: application/json
api-key: [query key]
```

Eis a mesma consulta de exemplo, desta vez utilizando o HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Um pedido de consulta com êxito resultará num Código de Estado de `200 OK` e os resultados da pesquisa são devolvidos como JSON no corpo da resposta. Eis o aspeto dos resultados da consulta acima, partindo do princípio de que o índice "hotéis" é preenchido com os dados de exemplo em [Importação de Dados na Azure Search através da API REST](search-import-data-rest-api.md) (tenha em atenção que o JSON foi formatado para clareza).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Para saber mais, consulte a secção "Resposta" dos [Documentos sobre Pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Para obter mais informações sobre outros códigos de estado HTTP que possam ser devolvidos em caso de falha, consulte [Códigos de estado HTTP (Pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

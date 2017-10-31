---
title: Explorar as APIs REST no Fiddler ou no Postman (REST do Azure Search) | Microsoft Docs
description: "Como utilizar o Fiddler ou o Postman para emitir pedidos HTTP e chamadas à API REST para o Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/17/2017
ms.author: heidist
ms.openlocfilehash: d8da3f02fab90e0c690e320736409a4d113d634c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Explorar as APIs REST do Azure Search com o Fiddler ou o Postman

Uma das formas mais fáceis de explorar a [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice) é utilizar o Fiddler ou o Postman para formular pedidos HTTP e inspecionar as respostas. Neste artigo, experimente as cargas de trabalho de pedido e resposta sem escrever código.

> [!div class="checklist"]
> * Transferir uma ferramenta de teste de api Web
> * Obter a chave de api e o ponto final do seu serviço de pesquisa
> * Configurar cabeçalhos de pedidos
> * Criar um índice
> * Carregar um índice
> * Pesquisar um índice

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar e [inscreva-se no Azure Search](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Transferir e instalar ferramentas

As ferramentas que se seguem são amplamente utilizadas na programação Web, mas se estiver familiarizado com outra ferramenta, as instruções neste artigo devem também aplicar-se à mesma.

+ [Postman (suplemento do Google Chrome)](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Obter a chave de api e o ponto final

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. No portal do Azure, abra a página do serviço de pesquisa a partir do dashboard ou [encontre o seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.
2. Obtenha o ponto final em **Descrição geral** > **Essentials** > **Url**. Um ponto final de exemplo poderá ser parecido com `https://my-service-name.search.windows.net`.
3. Obtenha a chave de api em **Definições** > **Chaves**. Existem duas chaves administrativas para redundância, caso pretenda reverter as chaves. As chaves administrativas concedem as permissões de escrita no seu serviço, as quais são necessárias para criar e carregar índices. Pode utilizar a chave primária ou secundária para operações de escrita.

## <a name="configure-request-headers"></a>Configurar cabeçalhos de pedidos

Cada ferramenta mantém as informações de cabeçalho do pedido para a sessão, o que significa que só tem de introduzir o ponto final do URL, a versão de api, a chave de api e o tipo de conteúdo uma vez.

O URL completo deve ter um aspeto semelhante ao do seguinte exemplo, mas com uma substituição válida para o nome do marcador de posição **`my-app`**: `https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01`

A composição do URL do serviço inclui os elementos seguintes:

+ Prefixo HTTPS.
+ URL do serviço, obtido no portal.
+ Recurso, uma operação que cria um objeto no seu serviço. Neste passo, é um índice designado “hotels” (“hotéis”).
+ versão de api, uma cadeia em minúsculas necessária especificada como "?api-version=2016-09-01" para a versão atual. As [versões de API](search-api-versions.md) são atualizadas regularmente. Incluir a versão de api em cada pedido dá-lhe controlo total sobre qual das versões é utilizada.  

A composição do cabeçalho do pedido inclui dois elementos, o tipo de conteúdo e a chave de api descrita na secção anterior:

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Formule um pedido parecido com a captura de ecrã seguinte. Escolha **PUT** como o verbo. O Fiddler adiciona `User-Agent=Fiddler`. Pode colar os dois cabeçalhos de pedido adicionais em novas linhas abaixo do mesmo. Inclua o tipo de conteúdo e a chave de api do seu serviço mediante a utilização da chave de acesso administrativa do mesmo.

![Cabeçalho de pedido do Fiddler][1]

> [!Tip]
> Pode desativar o tráfego Web para ocultar atividades HTTP estranhadas e não relacionadas com as tarefas que está a realizar. No Fiddler, aceda ao menu **File** (Ficheiro) e desative **Capture Traffic** (Capturar tráfego). 

### <a name="postman"></a>Postman

Formule um pedido parecido com a captura de ecrã seguinte. Escolha **PUT** como o verbo. 

![Cabeçalho de pedido do Postman][6]

## <a name="create-the-index"></a>Criar o índice

O corpo do pedido contém a definição do índice. Adicionar o corpo do pedido completa o pedido que produz o seu índice.

Para além do nome do índice, o componente mais importante no pedido é a coleção de campos. A coleção de campos define o esquema do índice. Em cada campo, especifique o respetivo tipo. Os campos de cadeia são utilizados em pesquisas em texto completo, pelo que pode transmitir os dados numéricos como cadeias se quiser que esse conteúdo seja pesquisável.

Os atributos no campo determinam a ação permitida. As APIs REST permitem muitas ações, por predefinição. Por exemplo, todas as cadeias são pesquisáveis, recuperáveis, filtráveis e facetáveis por predefinição. Muitas vezes, só é necessário definir atributos quando tem de desativar um comportamento. Para obter mais informações sobre os atributos, veja [Create Index (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Criar Índice [REST]).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }


Quando submete este pedido, deverá receber uma resposta HTTP 201, que indica que o índice foi criado com êxito. Pode verificar esta ação no portal, mas tenha em conta que a página do portal tem intervalos de atualização, de modo que poderá demorar um minuto ou dois a estar atualizada.

Se obtiver HTTP 504, certifique-se de que o URL especifica HTTPS. Se vir HTTP 400 ou 404, verifique o corpo do pedido para verificar que não ocorreram erros ao copiar-colar. Um HTTP 403 normalmente indica um problema com a chave de API (uma chave inválida ou um problema de sintaxe com a forma como a chave de API é especificada).

### <a name="fiddler"></a>Fiddler

Copie a definição do índice para o corpo do pedido, de forma semelhante à da captura de ecrã, e clique em **Execute** (Executar), no canto superior direito, para enviar o pedido concluído.

![Corpo de pedido do Fiddler][7]

### <a name="postman"></a>Postman

Copie a definição do índice para o corpo do pedido, de forma semelhante à da captura de ecrã, e clique em **Send** (Enviar), no canto superior direito, para enviar o pedido concluído.

![Corpo de pedido do Postman][8]

## <a name="load-documents"></a>Carregar documentos

A criação e o preenchimento do índice são dois passos distintos. No Azure Search, o índice contém todos os dados pesquisáveis, que pode fornecer como documentos JSON. Para rever a API para esta operação, veja [Add, update, or delete documents (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Adicionar, atualizar ou eliminar documentos [REST]).

+ Neste passo, altere o verbo para **POST**.
+ Altere o ponto final para incluir `/docs/index`. O URL completo deve ser parecido com `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01`
+ Mantenha os cabeçalhos de pedido como estão. 

O Corpo do Pedido contém quatro documentos que devem ser adicionados ao índice dos hotéis.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
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
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Em alguns segundos, deve ver uma resposta HTTP 200 na lista de sessão. Isto indica que os documentos foram criados com êxito. 

Se obtiver um 207, pelo menos um documento falhou ao carregar. Se obtiver um 404, tem um erro de sintaxe no cabeçalho ou no corpo do pedido. Confirme que alterou o ponto final, de modo a incluir `/docs/index`.

> [!Tip]
> Em determinadas origens de dados, pode escolher a abordagem de *indexador* alternativa, que simplifica e reduz a quantidade de código necessário para a indexação. Para obter mais informações, veja [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Operações do indexador).

### <a name="fiddler"></a>Fiddler

Altere o verbo para **POST**. Altere o URL para incluir `/docs/index`. Copie os documentos para o corpo do pedido, semelhante à captura de ecrã seguinte e execute-o.

![Payload de pedido do Fiddler][9]

### <a name="postman"></a>Postman

Altere o verbo para **POST**. Altere o URL para incluir `/docs/index`. Copie os documentos para o corpo do pedido, semelhante à captura de ecrã seguinte e execute-o.

![Payload de pedido do Postman][10]

## <a name="query-the-index"></a>Consultar o índice
Agora que um índice e os documentos foram carregados, pode consultar os mesmos. Para obter mais informações sobre esta API, veja [Search Documents (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Pesquisar Documentos [REST])  

+ Neste passo, altere o verbo para **GET**.
+ Altere o ponto final para incluir parâmetros de consulta, incluindo cadeias de pesquisa. Um URL de consulta poderá ser parecido com `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2016-09-01`
+ Mantenha os cabeçalhos de pedido como estão

Esta consulta pesquisa o termo "motel" e devolve uma contagem dos documentos nos resultados da pesquisa. O pedido e a resposta devem ter um aspeto semelhantes ao da captura de ecrã seguinte do Postman depois de clicar em **Send**. O código de estado deve ser 200.

 ![Resposta de consulta do Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Sugestões para executar as nossas consultas de exemplo no Fiddler

A consulta de exemplo seguinte é retirada do artigo [Search Index operation (Azure Search API)](http://msdn.microsoft.com/library/dn798927.aspx) (Operação de Pesquisa no Índice [API do Azure Search]). Muitas das consultas de exemplo neste artigo incluem espaços, algo não permitido no Fiddler. Substitua cada espaço por um caráter de + antes de colar a cadeia de consulta, antes de tentar a consulta no Fiddler.

**Antes de os espaços serem substituídos (em lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

**Depois de os espaços serem substituídos por + (em lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-index-properties"></a>Consultar propriedades do índice
Também pode consultar informações do sistema para obter contagens de documentos e o consumo de armazenamento: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01`

No Postman, o pedido deve ter um aspeto semelhante ao seguinte e a resposta inclui uma contagem de documentos e o espaço utilizado em bytes.

 ![Consulta de sistema do Postman][12]

Repare que a sintaxe da versão de api é diferente. Para este pedido, utilize `?` para acrescentar a versão de api. O ? separa o caminho do URL da cadeia de consulta, ao passo que & separa cada par “name=value” na cadeia de consulta. Nesta consulta, a versão de api é o primeiro e único item na cadeia de consulta.

Para obter mais informações sobre esta API, veja [Get Index Statistics (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) (Obter Estatísticas de Índice [REST]).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Sugestões para ver estatísticas de índice no Fiddler

No Fiddler, clique no separador **Inspectors** (Inspetores), clique no separador **Headers** (Cabeçalhos) e selecione o formato JSON. Deve ver a contagem dos documentos e o tamanho de armazenamento (em KB).

## <a name="next-steps"></a>Passos seguintes

Os clientes REST são indispensáveis para explorações improvisadas, mas agora que já sabe como é que as APIs REST funcionam, pode avançar com código. Para os passos seguintes, veja as ligações abaixo:

+ [Criar um índice (REST)](search-create-index-rest-api.md)
+ [Importar dados (REST)](search-import-data-rest-api.md)
+ [Consultar um índice (REST)](search-query-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png
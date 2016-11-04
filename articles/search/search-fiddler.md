---
title: Como utilizar o Fiddler para avaliar e testar as APIs REST da Azure Search | Microsoft Docs
description: Utilize o Fiddler e obtenha uma abordagem sem código para verificar a disponibilidade da Azure Search e experimentar as APIs REST.
services: search
documentationcenter: ''
author: HeidiSteen
manager: mblythe
editor: ''

ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/08/2016
ms.author: heidist

---
# Utilizar o Fiddler para avaliar e testar as APIs REST da Azure Search
> [!div class="op_single_selector"]
> * [Descrição geral](search-query-overview.md)
> * [Explorador de Procura](search-explorer.md)
> * [Fiddler](search-fiddler.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Este artigo explica como utilizar o Fiddler, disponível como uma [transferência gratuita da Telerik](http://www.telerik.com/fiddler), para emitir pedidos de HTTP e ver respostas utilizando a API REST da Azure Search, sem ter de escrever qualquer código. A Azure Search é serviço de pesquisa na nuvem alojado completamente gerido no Microsoft Azure, facilmente programável através das APIs .NET e REST. As APIs REST do serviço Azure Search estão documentadas na [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Nos passos seguintes, irá criar um índice, carregar documentos, consultar o índice e, em seguida, consultar o sistema para obter informações do serviço.

Para concluir estes passos, irá precisar de um serviço Azure Search e uma `api-key`. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter instruções sobre como começar.

## Criar um índice
1. Inicie o Fiddler. No menu **Ficheiro**, desative **Capturar Tráfego** para ocultar atividades HTTP estranhas que não estão relacionadas com a tarefa atual.
2. No separador **Compositor**, irá formular um pedido parecido com a seguinte captura de ecrã.
   
    ![][1]
3. Selecione **PUT**.
4. Introduza um URL que especifica o URL de serviço, os atributos de pedido e a versão de API. Alguns pontos a ter em atenção:
   
   * Utilize o prefixo HTTPS.
   * O atributo de pedido é "/indexes/hotels". Isto instrui a Pesquisa para criar um índice com o nome "hotéis".
   * A versão de API está em minúsculas, especificada como "?api-version = 2015-02-28". As versões de API são importantes porque a Azure Search implementa atualizações regularmente. Em raras ocasiões, uma atualização de serviço pode introduzir uma alteração inovadora para a API. Por este motivo, a Azure Search necessita de uma versão de API em cada pedido para que esteja em total controlo sobre qual é utilizada.
     
     O URL completo deve ter um aspeto semelhante ao seguinte exemplo.
     
            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28
5. Especifique o cabeçalho do pedido, substituindo o anfitrião e a chave de API por valores válidos para o serviço.
   
           User-Agent: Fiddler
           host: my-app.search.windows.net
           content-type: application/json
           api-key: 1111222233334444
6. No Corpo do Pedido, cole os campos que compõem a definição do índice.
   
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
7. Clique em **Executar**.

Em alguns segundos, deve ver uma resposta HTTP 201 na lista de sessão, indicando que o índice foi criado com êxito.

Se obtiver HTTP 504, certifique-se de que o URL especifica HTTPS. Se vir HTTP 400 ou 404, verifique o corpo do pedido para verificar que não ocorreram erros ao copiar-colar. Um HTTP 403 normalmente indica um problema com a chave de API (uma chave inválida ou um problema de sintaxe com a forma como a chave de API é especificada).

## Carregar documentos
No separador **Compositor**, o seu pedido para publicar documentos terá de ter o seguinte aspeto. O corpo do pedido contém os dados de pesquisa de 4 hotéis.

   ![][2]

1. Selecione **PUBLICAR**.
2. Introduza um URL que comece com HTTPS, seguido pelo seu URL de serviço e, depois, "/indexes/<'indexname'>/docs/index?api-version=2015-02-28". O URL completo deve ter um aspeto semelhante ao seguinte exemplo.
   
           https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
3. O Cabeçalho do Pedido deve ser o mesmo que antes. Lembre-se de que substituiu o anfitrião e a chave de API por valores válidos para o seu serviço.
   
           User-Agent: Fiddler
           host: my-app.search.windows.net
           content-type: application/json
           api-key: 1111222233334444
4. O Corpo do Pedido contém quatro documentos que devem ser adicionados ao índice dos hotéis.
   
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
5. Clique em **Executar**.

Em alguns segundos, deve ver uma resposta HTTP 200 na lista de sessão. Isto indica que os documentos foram criados com êxito. Se obtiver um 207, pelo menos um documento falhou ao carregar. Se obtiver um 404, tem um erro de sintaxe no cabeçalho ou no corpo do pedido.

## Consultar o índice
Agora que um índice e os documentos foram carregados, pode consultar os mesmos.  No separador **Compositor**, um compositor **OBTER** que consulta o seu serviço terá um aspeto semelhante à seguinte captura de ecrã.

   ![][3]

1. Selecione **OBTER**.
2. Introduza um URL que comece com HTTPS, seguido pelo seu URL de serviço, depois "/indexes/<'indexname'>/docs?" e, por fim, os parâmetros de consulta. A título de exemplo, utilize o seguinte URL, substituindo o nome de anfitrião do exemplo por um que seja válido para o seu serviço.
   
           https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28
   
   Esta consulta pesquisa o termo "motel" e obtém categorias de faceta para classificações.
3. O Cabeçalho do Pedido deve ser o mesmo que antes. Lembre-se de que substituiu o anfitrião e a chave de API por valores válidos para o seu serviço.
   
           User-Agent: Fiddler
           host: my-app.search.windows.net
           content-type: application/json
           api-key: 1111222233334444

O código de resposta deve ser 200 e a saída de resposta deve ter um aspeto semelhante à seguinte captura de ecrã.

   ![][4]

A seguinte consulta de exemplo é retirada da [operação do Índice de Pesquisa (API da Azure Search)](http://msdn.microsoft.com/library/dn798927.aspx) na MSDN. Muitas das consultas de exemplo neste tópico incluem espaços, algo não permitido no Fiddler. Substitua cada espaço por um caráter de + antes de colar a cadeia de consulta, antes de tentar a consulta no Fiddler.

**Os espaços anteriores são substituídos:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Os espaços posteriores são substituídos por +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## Consultar o sistema
Também pode consultar o sistema para obter contagens de documentos e o consumo de armazenamento. No separador **Compositor**, o pedido terá um aspeto semelhante ao seguinte e a resposta irá devolver uma contagem do número de documentos e espaço utilizado.

 ![][5]

1. Selecione **OBTER**.
2. Introduza um URL que inclua o URL de serviço, seguido por "/indexes/hotels/stats?api-version=2015-02-28":
   
           https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28
3. Especifique o cabeçalho do pedido, substituindo o anfitrião e a chave de API por valores válidos para o serviço.
   
           User-Agent: Fiddler
           host: my-app.search.windows.net
           content-type: application/json
           api-key: 1111222233334444
4. Deixe o corpo do pedido em branco.
5. Clique em **Executar**. Deve ver um código de estado HTTP 200 na lista de sessão. Selecione a entrada publicada para o comando.
6. Clique no separador **Inspetores**, no separador **Cabeçalhos** e, em seguida, selecione o formato JSON. Deve ver a contagem dos documentos e o tamanho de armazenamento (em KB).

## Passos seguintes
Consulte [Gerir o serviço de Pesquisa no Azure](search-manage.md) para uma abordagem sem código à gestão e a utilização da Azure Search.

<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png



<!--HONumber=Sep16_HO3-->



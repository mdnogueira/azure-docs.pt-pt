---
title: "Rota de localizar com base dos serviços de localização do Azure | Microsoft Docs"
description: "Encaminhar para um ponto de interesse utilizando com base dos serviços de localização do Azure"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0f784e8ecd8fc94c12df1a819055718e06547b6b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Encaminhar para um ponto de interesse utilizando com base dos serviços de localização do Azure

Este tutorial mostra como utilizar a sua conta com base dos serviços de localização do Azure e o SDK do serviço de rota, para encontrar a rota para o ponto de interesse. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Obter endereço coordenadas
> * Consultar o serviço de rota para as direções de pontos de interesse

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se de que [criar a sua conta com base dos serviços de localização do Azure](./tutorial-search-location.md#createaccount), e [obter a chave de subscrição para a sua conta](./tutorial-search-location.md#getkey). Também poderá reparar como utilizar o controlo de mapa e APIs de serviço de pesquisa, tal como explicado no tutorial [pesquisa próximas pontos de interesse com base dos serviços de localização do Azure a utilizar](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Obter endereço coordenadas

Utilize os seguintes passos para criar uma página HTML estática incorporada com API de controlo de mapa a localização com base em serviços. 

1. No seu computador local, crie um novo ficheiro e nome **MapRoute.html**. 
2. Adicione os seguintes componentes HTML para o ficheiro:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body>
        <div id="map"></div>
        <script>
        // Embed Map Control JavaScript code here
        </script>

    </body>

    </html>
    ```
    Tenha em atenção a como o cabeçalho HTML incorpora as localizações de recursos para ficheiros CSS e JavaScript para a biblioteca com base dos serviços de localização do Azure. Repare também o *script* segmento no corpo do ficheiro HTML, que irá conter o código JavaScript para acesso a APIs do Azure localização com base do serviço de inline.

3. Adicione o seguinte código JavaScript para a *script* bloco do ficheiro HTML. Substitua o marcador de posição *< chave de inserção >* com a chave primária da sua conta de localização com base em serviços.

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    O **atlas. Mapa** fornece o controlo de um mapa de interativa e visual web e é um componente da API de controlo de mapa do Azure.

4. Adicione o seguinte código JavaScript para a *script* bloco. Esta ação adiciona uma camada de *multipoints* para o controlo de mapa para mostrar a rota:

    ```HTML
            // Initialize the linestring layer for routes on the map
            var routeLinesLayerName = "routes";
            map.addLinestrings([], {
                name: routeLinesLayerName,
                color: "#2272B9",
                width: 5,
                cap: "round",
                join: "round",
                before: "labels"
            });
    ```

5. Adicione o seguinte código JavaScript para criar pontos inicial e final para a rota:

    ```HTML
            // Create the GeoJSON objects which represent the start and end point of the route
            var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
            var startPin = new atlas.data.Feature(startPoint, {
                title: "Microsoft",
                icon: "pin-round-blue"
            });

            var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
            var destinationPin = new atlas.data.Feature(destinationPoint, {
                title: "Contoso Oil & Gas",
                icon: "pin-blue"
            });
    ```
    Este código cria dois [GeoJSON objetos](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos inicial e final da rota. O ponto final é a combinação de latitude/longitude para uma do *estações gasoline* procurado no tutorial anterior [pesquisa próximas pontos de interesse com base dos serviços de localização do Azure a utilizar](./tutorial-search-location.md).

6. Adicione o seguinte código JavaScript para adicionar os pins para os pontos inicial e final para o mapa:

    ```HTML
            // Fit the map window to the bounding box defined by the start and destination points
            var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });

            // Add pins to the map for the start and end point of the route
            map.addPins([startPin, destinationPin], {
                name: "route-pins",
                textFont: "SegoeUi-Regular",
                textOffset: [0, -20]
            });
    ``` 
    A API **map.setCameraBounds** ajusta a janela de mapa de acordo com as coordenadas dos pontos inicial e final. A API **map.addPins** adiciona os pontos de controlo de mapa como componentes visuais.

7. Guardar o **MapRoute.html** ficheiro no seu computador. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Consultar o serviço de rota para as direções de pontos de interesse

Esta secção mostra como utilizar API do Azure com base dos serviços de localização do serviço de rota para encontrar a rota de um ponto de início especificada para um destino. O serviço de rota fornece APIs para planear o fastest, mais curto ou eco rota entre duas localizações, considerar as condições de tráfego em tempo real. Também permite que os utilizadores planear rotas no futuro através extensas tráfego históricos da base de dados do Azure e prever durações de rota para qualquer dia e hora. 

1. Abra o **MapRoute.html** ficheiro criado na secção anterior e adicione o seguinte código JavaScript para a *script* bloco, para ilustrar o serviço de rota.

    ```HTML
            // Perform a request to the route service and draw the resulting route on the map
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200) {
                    var response = JSON.parse(xhttp.responseText);

                    var route = response.routes[0];
                    var routeCoordinates = [];
                    for (var leg of route.legs) {
                        var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                        routeCoordinates = routeCoordinates.concat(legCoordinates);
                    }

                    var routeLinestring = new atlas.data.LineString(routeCoordinates);
                    map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
                }
            };
    ```
    Este fragmento de código cria um [XMLHttpRequest](https://xhr.spec.whatwg.org/), e adiciona um processador de eventos ao analisar a resposta de entrada. Para uma resposta com êxito,-constrói uma matriz de coordenadas para os segmentos de linha da rota primeiro devolvido. Em seguida, adiciona este conjunto de coordenadas para esta rota para o mapa *multipoints* camada.

2. Adicione o seguinte código para o *script* bloco, para enviar o XMLHttpRequest para rota serviço do Azure com base dos serviços de localização:

    ```HTML
            var url = "https://atlas.microsoft.com/route/directions/json?";
            url += "&api-version=1.0";
            url += "&subscription-key=" + subscriptionKey;
            url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    
            xhttp.open("GET", url, true);
            xhttp.send();
    ```
    O pedido acima mostra os parâmetros necessários, o que são a chave de subscrição da sua conta e as coordenadas para os pontos inicial e final, pela ordem indicada. 

3. Guardar o **MapRoute.html** localmente, de ficheiros, em seguida, abra-o num browser da sua preferência e observe o resultado. Para uma ligação com êxito com APIs a localização com base em serviços, deverá ver um mapa semelhante ao seguinte. 

    ![Controlo de mapa do Azure e o serviço de rota](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Obter endereço coordenadas
> * Consultar o serviço de rota para as direções de pontos de interesse

Avance para o tutorial [localizar rotas para os diferentes modos de levar a utilizar com base dos serviços de localização do Azure](./tutorial-prioritized-routes.md) para aprender a utilizar o Azure com base dos serviços de localização para definir prioridades de rotas para o ponto de interesse, com base no modo de transporte. 

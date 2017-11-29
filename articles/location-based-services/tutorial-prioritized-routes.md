---
title: "Várias rotas com base dos serviços de localização do Azure | Microsoft Docs"
description: "Localizar rotas para os diferentes modos de levar a utilizar com base dos serviços de localização do Azure"
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
ms.openlocfilehash: 3631bab8e5cb505689e92d2862c6863bcd56404d
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Localizar rotas para os diferentes modos de levar a utilizar com base dos serviços de localização do Azure

Este tutorial mostra como utilizar a sua conta com base dos serviços de localização do Azure e o SDK do serviço de rota, para encontrar a rota para o ponto de interesse, definida pelo seu modo de levar. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a sua consulta do serviço de rota
> * Composição rotas definidas pelo modo de levar

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se de que [criar a sua conta com base dos serviços de localização do Azure](./tutorial-search-location.md#createaccount), e [obter a chave de subscrição para a sua conta](./tutorial-search-location.md#getkey). Também poderá reparar como utilizar o controlo de mapa e APIs de serviço de pesquisa, tal como explicado no tutorial [pesquisa próximas pontos de interesse com base dos serviços de localização do Azure a utilizar](./tutorial-search-location.md), bem como obter a utilização básica das APIs de serviço de rota, como descrito no tutorial [rota para um ponto de interesse com base dos serviços de localização do Azure a utilizar](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Configurar a sua consulta do serviço de rota

Utilize os seguintes passos para criar uma página HTML estática incorporada com API de controlo de mapa a localização com base em serviços. 

1. No seu computador local, crie um novo ficheiro e nome **MapTruckRoute.html**. 
2. Adicione os seguintes componentes HTML para o ficheiro:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
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
    Tenha em atenção que o cabeçalho HTML incorpora as localizações de recursos para ficheiros CSS e JavaScript para a biblioteca com base dos serviços de localização do Azure. Repare também o *script* segmento adicionado ao corpo de HTML, que contém o inline código JavaScript para aceder à API de controlo de mapa do Azure.
3. Adicione o seguinte código JavaScript para a *script* bloco do ficheiro HTML. Substitua o marcador de posição *< chave de inserção >* com a chave primária da sua conta de localização com base em serviços.

    ```HTML
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    O **atlas. Mapa** fornece o controlo de um mapa de interativa e visual web e é um componente da API de controlo de mapa do Azure.

4. Adicione o seguinte código JavaScript para a *script* bloco, para adicionar a apresentação de fluxo de tráfego para o mapa:

    ```HTML
            // Add Traffic Flow to the Map
            map.setTraffic({
                flow: "relative"
            });
    ```
    Este código define o fluxo de tráfego para `relative`, que é a velocidade do viagem relativos ao fluxo livre. Também pode defini-lo `absolute` velocidade de viagem, ou `relative-delay` que apresenta a velocidade relativa em que difere do fluxo de gratuita. 

5. Adicione o seguinte código JavaScript para criar os pins para os pontos inicial e final da rota:

    ```HTML
            // Create the GeoJSON objects which represent the start and end point of the route
            var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
            var startPin = new atlas.data.Feature(startPoint, {
                title: "Fabrikam, Inc.",
                icon: "pin-round-blue"
            });

            var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
            var destinationPin = new atlas.data.Feature(destinationPoint, {
                title: "Microsoft",
                icon: "pin-blue"
            });
    ```
    Este código cria dois [GeoJSON objetos](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos inicial e final da rota. 

6. Adicione o seguinte código JavaScript para adicionar camadas de *multipoints* para o controlo de mapa para visualizar as rotas com base no modo de transporte, por exemplo, _carro_ e _camião_.

    ```HTML
            // Place route layers on the map
            var carRouteLayerName = "car-route";
            map.addLinestrings([], {
                name: carRouteLayerName,
                color: "#B76DAB",
                width: 5,
                cap: "round",
                join: "round",
                before: "labels"
            });
    
            var truckRouteLayerName = "truck-route";
            map.addLinestrings([], {
                name: truckRouteLayerName,
                color: "#2272B9",
                width: 9,
                cap: "round",
                join: "round",
                before: carRouteLayerName
            });
    ```

7. Adicione o seguinte código JavaScript para adicionar os pontos inicial e final para o mapa:

    ```HTML
            // Fit the map window to the bounding box defined by the start and destination points
            var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
            var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 100
            });

            // Add pins to the map for the start and end point of the route
            map.addPins([startPin, destinationPin], {
                name: "route-pins",
                textFont: "SegoeUi-Regular",
                textOffset: [0, -20]
            });
    ``` 
    A API **map.setCameraBounds** ajusta a janela de mapa de acordo com as coordenadas dos pontos inicial e final. A API **map.addPins** adiciona os pontos de controlo de mapa como componentes visuais.

8. Guardar o **MapTruckRoute.html** ficheiro no seu computador. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Composição rotas definidas pelo modo de levar

Esta secção mostra como utilizar API do Azure com base dos serviços de localização do serviço de rota para localizar a várias rotas desde um determinado início apontam para um destino, o modo de transporte. O serviço de rota fornece APIs para planear o fastest, mais curto ou eco rota entre duas localizações, considerar as condições de tráfego em tempo real. Também permite que os utilizadores planear rotas no futuro através extensas tráfego históricos da base de dados do Azure e prever durações de rota para qualquer dia e hora. 

1. Abra o **MapTruckRoute.html** ficheiro criado na secção anterior e adicione o seguinte código JavaScript para a *script* bloco, para obter a rota para um camião utilizando o serviço de rota.

    ```HTML
            // Perform a request to the route service and draw the resulting truck route on the map
            var xhttpTruck = new XMLHttpRequest();
            xhttpTruck.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200) {
                    var response = JSON.parse(this.responseText);

                    var route = response.routes[0];
                    var routeCoordinates = [];
                    for (var leg of route.legs) {
                        var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                        routeCoordinates = routeCoordinates.concat(legCoordinates);
                    }

                    var routeLinestring = new atlas.data.LineString(routeCoordinates);
                    map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                        name: truckRouteLayerName
                    });
                }
            };

            var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
            truckRouteUrl += "&api-version=1.0";
            truckRouteUrl += "&subscription-key=" + subscriptionKey;
            truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
            truckRouteUrl += "&travelMode=truck";
            truckRouteUrl += "&vehicleWidth=2";
            truckRouteUrl += "&vehicleHeight=2";
            truckRouteUrl += "&vehicleLength=5";
            truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

            xhttpTruck.open("GET", truckRouteUrl, true);
            xhttpTruck.send();
    ```
    Este fragmento de código cria um [XMLHttpRequest](https://xhr.spec.whatwg.org/), e adiciona um processador de eventos ao analisar a resposta de entrada. Para uma resposta com êxito, cria uma matriz de coordenadas para a rota devolvido e adiciona-o mapa `truckRouteLayerName` camada. 
    
    Este fragmento de código também envia a consulta para o serviço de rota, para obter a rota de início especificado e o ponto final, para a chave de subscrição da sua conta. Os parâmetros opcionais seguintes são utilizados para indicar a rota de um camião pesada:-o parâmetro `travelMode=truck` Especifica o modo da levar como *camião*. Outros modos de levar suportados são *taxi*, *bus*, *van*, *motorcycle*e a predefinição *carro* . 
        -Os parâmetros `vehicleWidth`, `vehicleHeight`, e `vehicleLength` especifique as dimensões do veículo no medidores e são considerados apenas se o modo de levar é *camião*. 
        -O `vehicleLoadType` classifica carga como hazardous e restrito em algumas roads. Isto é considerado também atualmente apenas para o *camião* modo. 

2. Adicione o seguinte código JavaScript para obter a rota de um carro através do serviço de rota:

    ```HTML
            // Perform a request to the route service and draw the resulting car route on the map
            var xhttpCar = new XMLHttpRequest();
            xhttpCar.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200) {
                    var response = JSON.parse(this.responseText);

                    var route = response.routes[0];
                    var routeCoordinates = [];
                    for (var leg of route.legs) {
                        var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                        routeCoordinates = routeCoordinates.concat(legCoordinates);
                    }

                    var routeLinestring = new atlas.data.LineString(routeCoordinates);
                    map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                        name: carRouteLayerName
                    });
                }
            };

            var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
            carRouteUrl += "&api-version=1.0";
            carRouteUrl += "&subscription-key=" + subscriptionKey;
            carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
                destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

            xhttpCar.open("GET", carRouteUrl, true);
            xhttpCar.send();
    ```
    Este fragmento de código cria outra [XMLHttpRequest](https://xhr.spec.whatwg.org/), e adiciona um processador de eventos ao analisar a resposta de entrada. Para uma resposta com êxito, cria uma matriz de coordenadas para a rota devolvido e adiciona-o mapa `carRouteLayerName` camada. 
    
    Este fragmento de código também envia a consulta para o serviço de rota, para obter a rota para o início especificado e o ponto final, para a chave de subscrição da sua conta. Uma vez que não existem outros parâmetros são utilizados, a rota para o modo predefinido da levar *carro* é devolvido. 

3. Guardar o **MapTruckRoute.html** localmente, de ficheiros, em seguida, abra-o num browser da sua preferência e observe o resultado. Para uma ligação com êxito com APIs a localização com base em serviços, deverá ver um mapa semelhante ao seguinte. 

    ![Rotas prioritários com o serviço de rota do Azure](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Tenha em atenção que a rota camião na cor azul, enquanto a rota carro é roxa.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar a sua consulta do serviço de rota
> * Composição rotas definidas pelo modo de levar

Avance para o **conceitos** e **como** artigos para saber o SDK do Azure localização com base em serviços em profundidade. 

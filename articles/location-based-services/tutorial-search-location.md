---
title: "Pesquisa com a localização do Azure com base em serviços | Microsoft Docs"
description: "Procurar próximas pontos de interesse utilizando com base dos serviços de localização do Azure"
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
ms.openlocfilehash: 15afdead60d4c1ee3c7e3c079d43e0651b262ec8
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="search-nearby-point-of-interest-using-azure-location-based-services"></a>Procurar próximas pontos de interesse utilizando com base dos serviços de localização do Azure

Este tutorial mostra como configurar uma conta com base dos serviços de localização do Azure e, em seguida, utilizar as APIs fornecidas para procurar um ponto de interesse. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta com base dos serviços de localização do Azure
> * Obter a chave de subscrição para a sua conta
> * Criar nova página web utilizando a API de controlo de mapa
> * Utilizar o serviço de pesquisa para localizar próximas pontos de interesse

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>Criar uma conta com base dos serviços de localização do Azure

Siga estes passos para criar uma nova conta de localização com base em serviços.

1. No canto superior esquerdo do [portal do Azure](https://portal.azure.com), clique em **crie um recurso**.
2. No *procurar no Marketplace* caixa, escreva **localização com base em serviços**.
3. Do *resultados*, clique em de **com base dos serviços de localização (pré-visualização)**. Clique em **criar** botão aparece abaixo o mapa. 
4. No **criar localização com base em conta serviços** página, introduza os seguintes valores:
    - O *nome* da sua conta nova. 
    - O *subscrição* que pretende utilizar para esta conta.
    - O *grupo de recursos* nome para esta conta. Pode optar por *criar nova* ou *utilizar existente* grupo de recursos.
    - Selecione o *localização do grupo de recursos*.
    - Leia o *termos de pré-visualização* e marque a caixa de verificação para aceitar os termos de licenciamento. 
    - Por fim, clique em de **criar** botão.
   
    ![Criar conta de localização com base em serviços no portal](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-subscription-key-for-your-account"></a>Obter a chave de subscrição para a sua conta

Assim que a sua conta de serviços com base de localização é criada com êxito, siga os passos para ligá-lo para as respetiva APIs de pesquisa do mapa:

1. Abra a sua conta de localização com base em serviços no portal.
2. Navegue até à sua conta **definições**e, em seguida, selecione **chaves**.
3. Copiar o **chave primária** a sua área de transferência. Guardá-lo localmente ao utilizar os passos de continuar. 

    ![Obter a chave primária no portal](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>Criar nova página web utilizando a API de controlo de mapa do Azure
API de controlo de mapa do Azure é uma biblioteca de cliente conveniente que lhe permite facilmente integrar com base dos serviços de localização do Azure na sua aplicação web. Oculta a complexidade das chamadas de serviço REST bare e boosts sua produtividade com componentes styleable e personalizáveis. Os passos seguintes mostram como criar uma página HTML estática incorporada com API de controlo de mapa a localização com base em serviços. 

1. No seu computador local, crie um novo ficheiro e nome **MapSearch.html**. 
2. Adicione os seguintes componentes HTML para o ficheiro:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

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
    Tenha em atenção de que o cabeçalho HTML inclui os ficheiros de recursos CSS e JavaScript hospedados a biblioteca de controlo de mapa do Azure. Tenha em atenção o *script* segmento adicionado para o *corpo* do ficheiro HTML. Este segmento irá conter o código JavaScript para acesso a APIs do Azure localização com base do serviço de inline.
 
3.  Adicione o seguinte código JavaScript para a *script* bloco do ficheiro HTML. Substitua o marcador de posição *< chave de inserção >* com a chave primária da sua conta de localização com base em serviços. 

    ```HTML/JavaScript
            // Instantiate map to the div with id "map"
            var subscriptionKey = "<insert-key>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey
            });

    ```
    Este segmento inicia a API de controlo de mapa para a sua chave de subscrição. **Atlas** é o espaço de nomes que contém a API de controlo de mapa do Azure e os componentes de visual relacionados. **Atlas. Mapa** fornece o controlo de um mapa de interativa e visual web. Pode observar como mapa do aspeto, abrindo a página HTML no browser. 

4. Adicione o seguinte código JavaScript para a *script* bloco, para adicionar uma camada de pins de pesquisa para o controlo de mapa:

    ```HTML/JavaScript
            // Initialize the pin layer for search results to the map
            var searchLayerName = "search-results";
            map.addPins([], {
                name: searchLayerName,
                cluster: false,
                icon: "pin-round-darkblue"
            });
    ```

5. Guarde o ficheiro no seu computador. 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>Utilizar o serviço de pesquisa para localizar próximas pontos de interesse

Esta secção mostra como utilizar API do Azure com base dos serviços de localização do serviço de pesquisa para localizar um ponto de interesse no seu mapa. É uma API RESTful concebido para programadores procurar os endereços, os pontos de interesse e outras informações geográficas. O serviço de pesquisa atribui um latitude e longitude informações para um endereço especificado. 

1. Abra o **MapSearch.html** ficheiro criado na secção anterior e adicione o seguinte código JavaScript para a *script* bloco, para ilustrar o serviço de pesquisa. 
    ```HTML/JavaScript
            // Perform a request to the search service and create a pin on the map for each result
            var xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function () {
                var searchPins = [];

                if (this.readyState === 4 && this.status === 200) {
                    var response = JSON.parse(this.responseText);

                    var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

                    searchPins = poiResults.map((poiResult) => {
                        var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                        return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                            name: poiResult.poi.name,
                            address: poiResult.address.freeformAddress,
                            position: poiResult.position.lat + ", " + poiResult.position.lon
                        });
                    });

                    map.addPins(searchPins, {
                        name: searchLayerName
                    });

                    var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
                    var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

                    var swLon = Math.min.apply(null, lons);
                    var swLat = Math.min.apply(null, lats);
                    var neLon = Math.max.apply(null, lons);
                    var neLat = Math.max.apply(null, lats);

                    map.setCameraBounds({
                        bounds: [swLon, swLat, neLon, neLat],
                        padding: 50
                    });
                }
            };
    ```
    Este fragmento de código cria um [XMLHttpRequest](https://xhr.spec.whatwg.org/), e adiciona um processador de eventos ao analisar a resposta de entrada. Para uma resposta com êxito, recolhe endereços, os nomes de latitude e logitude informações para cada localização devolvida, além de `searchPins` variável. Por fim, é adicionado esta coleção de pontos de localização para o `map` controlo como pins. 

2. Adicione o seguinte código para o *script* bloco, para enviar o XMLHttpRequest para o serviço de pesquisa do Azure com base dos serviços de localização:

    ```HTML/JavaScript
            var url = "https://atlas.microsoft.com/search/fuzzy/json?";
            url += "&api-version=1.0";
            url += "&query=gasoline%20station";
            url += "&subscription-key=" + subscriptionKey;
            url += "&lat=47.6292";
            url += "&lon=-122.2337";
            url += "&radius=100000"

            xhttp.open("GET", url, true);
            xhttp.send();
    ``` 
    Este fragmento utiliza a API de pesquisa básica do serviço de pesquisa, denominado o **pesquisa difusa**. Processa o mais difusa entradas lidar com qualquer combinação de endereço ou *POI* tokens. Este procura para os próximos em **estação gasoline**, para o endereço indicado no latitude e longitude e dentro do raio especificado. Utiliza a chave de subscrição da sua conta fornecido anteriormente no ficheiro de exemplo, para efetuar a chamada para os serviços de localização com base. Devolve os resultados como latitude/longitude pares para as localizações encontradas. Pode observar os pins de pesquisa, abrindo a página HTML no browser. 

3. Adicione as seguintes linhas à *script* bloco, para criar o pop-ups para os pontos de interesse devolvido pelo serviço de pesquisa:

    ```HTML/JavaScript
            // Add a popup to the map which will display some basic information about a search result on hover over a pin
            var popup = new atlas.Popup();
            map.addEventListener("mouseover", searchLayerName, (e) => {
                var popupContentElement = document.createElement("div");
                popupContentElement.style.padding = "5px";

                var popupNameElement = document.createElement("div");
                popupNameElement.innerText = e.features[0].properties.name;
                popupContentElement.appendChild(popupNameElement);

                var popupAddressElement = document.createElement("div");
                popupAddressElement.innerText = e.features[0].properties.address;
                popupContentElement.appendChild(popupAddressElement);

                var popupPositionElement = document.createElement("div");
                popupPositionElement.innerText = e.features[0].properties.position;
                popupContentElement.appendChild(popupPositionElement);

                popup.setPopupOptions({
                    position: e.features[0].geometry.coordinates,
                    content: popupContentElement
                });

                popup.open(map);
            });
    ```
    A API **atlas. Pop-up** fornece informações de uma janela ancorada na posição necessária no mapa. Este fragmento de código define o conteúdo e a posição para o pop-up, bem como adiciona um serviço de escuta de eventos para o `map` controlo, a aguardar o _rato_ para rollover de pop-up. 

4. Guarde o ficheiro e, em seguida, abra o **MapSearch.html** ficheiro num browser da sua preferência e observe o resultado. Neste momento, o mapa no browser mostra pop-ups de informações quando paira o rato sobre qualquer um dos pins a pesquisa mostrados, semelhantes ao seguinte. 

    ![Controlo de mapa do Azure e o serviço de pesquisa](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma conta com base dos serviços de localização do Azure
> * Obter a chave de subscrição para a sua conta
> * Criar nova página web utilizando a API de controlo de mapa
> * Utilizar o serviço de pesquisa para localizar próximas pontos de interesse

Avance para o tutorial [rota para um ponto de interesse com base dos serviços de localização do Azure a utilizar](./tutorial-route-location.md) para saber como utilizar o Azure com base dos serviços de localização para encaminhar para o ponto de interesse. 

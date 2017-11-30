---
title: "Como procurar um endereço utilizando o serviço de pesquisa do Azure com base dos serviços de localização (pré-visualização) | Microsoft Docs"
description: "Saiba como procurar um endereço utilizando os serviços de com base da localização do Azure (pré-visualização) serviço de pesquisa"
services: location-based-services
keywords: "Não adicionar ou editar as palavras-chave sem consultadoria seu perito de SEO."
author: philmea
ms.author: philmea
ms.date: 11/29/2017
ms.topic: how-to
ms.service: location-based-services
ms.openlocfilehash: f7337c1c5821016987096da47dda4ac1124d7910
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="how-to-find-an-address-using-the-azure-location-based-services-preview-search-service"></a>Como localizar um endereço utilizando o serviço de pesquisa do Azure com base dos serviços de localização (pré-visualização)
O serviço de pesquisa é um conjunto de APIs concebidos para programadores procurar endereços, de locais, de pontos de interesse, listagens de empresas e outras informações geográficas RESTful. O serviço de pesquisa atribui um latitude/longitude para um endereço específico, Rua cruzada, funcionalidade geográfica ou ponto de interesse (POI). Os valores de latitude e longitude devolvidos pelas APIs do serviço de pesquisa podem ser utilizados como parâmetros outros do Azure com base os serviços de localização, tais como a rota e APIs de fluxo de tráfego.

## <a name="prerequisites"></a>Pré-requisitos
Instalar o [Postman aplicação](https://www.getpostman.com/apps).

Uma chave de conta e subscrição de com base dos serviços de localização do Azure. Para obter informações sobre como criar uma conta e obter uma chave de subscrição, consulte [como gerir a sua conta com base dos serviços de localização do Azure e as chaves](how-to-manage-account-keys.md). 

## <a name="using-fuzzy-search"></a>Utilizar a pesquisa difusa

A API de predefinido para o serviço de pesquisa é pesquisa difusa, que processa as entradas de qualquer combinação de endereço ou tokens POI. Esta pesquisa API é canónica 'linha única pesquisa' e é útil se não souber as suas entradas do utilizador como uma consulta de pesquisa. A API de pesquisa difusa é uma combinação da codificação geográfica e de pesquisa POI. A API também pode ser avaliada com uma posição contextual (lat./lon. emparelhe), completamente restrita por uma coordenada e radius, ou podem ser executado mais geralmente sem qualquer georreplicação biasing ponto de ancoragem.

A maioria das consultas de pesquisa predefinido para ' maxFuzzyLevel = 1' para obter um desempenho e reduzir os resultados de atividade invulgares. Esta predefinição pode ser substituída, conforme necessário por pedido mediante a transmissão no parâmetro de consulta ' maxFuzzyLevel = 2' ou '3'.

### <a name="search-for-an-address-using-fuzzy-search"></a>Procurar um endereço utilizando pesquisa difusa

1. Abra a aplicação de Postman, clique em novo | Criar novo e selecione **pedido GET**. Introduza um nome de pedido de **pesquisa difusa**, selecione uma coleção ou pasta para guardá-lo e clique em **guardar**.

    Para obter mais informações, consulte a documentação de pedidos do Postman.

2. No separador Builder, selecione o **obter** método HTTP e introduza o URL do pedido para o ponto final de API.

    ![Pesquisa difusa ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do pedido | https://Atlas.microsoft.com/search/Fuzzy/JSON? |
    | Autorização | Sem autenticação |

    O **json** atributo no caminho de URL determina o formato da resposta. Estão a utilizar o json em toda este artigo para facilidade de utilização e o facilitar a leitura. Pode encontrar os formatos de resposta disponíveis no **obter pesquisa difusa** definição [Referência de localização com base em API dos serviços funcional] (https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchfuzzy).

3. Clique em **Params**e introduza a seguinte chave / valor pares a utilizar como parâmetros de consulta ou o caminho no URL do pedido:

    ![Pesquisa difusa ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chave | Valor |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | chave de subscrição | *chave de subscrição* |
    | consulta | pizza |

4. Clique em **enviar** e rever o corpo da resposta. 

    A cadeia de consulta ambígua de "pizza" devolvido 10 ponto dos resultados de interesse (POI) com categorias baixar "pizza" e "restaurante". Cada resultado devolve uma morada, latitude / valores de longitude, ver a porta e pontos de entrada para a localização.
    
    Os resultados são diversificados para esta consulta, não associada a qualquer localização de referência específica. Pode utilizar o **countrySet** parâmetro para especificar apenas países/regiões para as quais a aplicação necessita de cobertura, como é o comportamento predefinido para procurar o universo de todo, potencialmente devolver resultados desnecessários.

5. Adicione o seguinte valor para a cadeia de consulta e clique em **enviar**:
    ```
        ,countrySet=US
    ```
    >[!NOTE] 
    >Certifique-se de que a vírgula-separado o URI adicional parâmetros na cadeia de consulta.
    
    Os resultados são agora tem um vínculo pelo indicativo do país e a consulta devolve pizza restaurants nos Estados Unidos.
    
    Para fornecer resultados orientado para uma localização específica, pode consultar um ponto de interesse e utilizar os valores de longitude e latitude devolvido na sua chamada para o serviço de pesquisa difusa. Neste caso, é utilizado o serviço de pesquisa para devolver a localização de agulha de espaço em Seattle e utilizado o lat. / lon. valores para incluída a pesquisa.
    
4. Os parâmetros, introduza a seguinte chave / valor pares e clique em **enviar**:

    ![Pesquisa difusa ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Chave | Valor |
    |-----|------------|
    | LAT | 47.62039 |
    | lon | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Procurar propriedades de endereço e coordenadas 

Pode passar uma completa ou parcial morada para a API de endereço de pesquisa e receber uma resposta que inclui propriedades de endereço de detalhado, tais como municipality ou subdivision, bem como posicionais valores latitude e longitude.

1. No Postman, clique em **novo pedido** | **pedido GET** e dê-lhe nome **endereço pesquisa**.
2. No separador Builder, selecione o **obter** método HTTP, introduza o URL de pedido para o ponto final de API e selecione um protocolo de autorização, se aplicável.

    ![Pesquisa de endereço ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do pedido | https://Atlas.microsoft.com/search/Address/JSON? |
    | Autorização | Sem autenticação |

2. Clique em **Params**e introduza a seguinte chave / valor pares a utilizar como parâmetros de consulta ou o caminho no URL do pedido:
    
    ![Pesquisa de endereço ](./media/how-to-search-for-address/address_search_params.png)
    
    | Chave | Valor |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | chave de subscrição | *chave de subscrição* |
    | consulta | 400 abrangente St, Seattle, WA 98109 |
    
3. Clique em **enviar** e rever o corpo da resposta. 
    
    Neste caso, pode especificar uma consulta de endereço completo e recebe um resultado único no corpo da resposta. 
    
4. Os parâmetros, edite a cadeia de consulta para o seguinte valor:
    ```
        400 Broad, Seattle
    ```

5. Adicione o seguinte valor para a cadeia de consulta e clique em **enviar**:
    ```
        ,typeahead
    ```

    O **typeahead** sinalizador indica a API de pesquisa de endereço para processar a consulta como uma entrada parcial e devolver uma matriz de valores preditivos.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Procurar uma morada utilizando a pesquisa inversa de endereços
1. No Postman, clique em **novo pedido** | **pedido GET** e dê-lhe nome **pesquisa inversa de endereços**.

2. No separador Builder, selecione o **obter** método HTTP e introduza o URL do pedido para o ponto final de API.
    
    ![URL de pesquisa inversa do endereço ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do pedido | https://Atlas.microsoft.com/search/Address/reverse/JSON? |
    | Autorização | Sem autenticação |
    
2. Clique em **Params**e introduza a seguinte chave / valor pares a utilizar como parâmetros de consulta ou o caminho no URL do pedido:
    
    ![Inverter os parâmetros de pesquisa de endereço ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Chave | Valor |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | chave de subscrição | *chave de subscrição* |
    | consulta | 47.59093,-122.33263 |
    
3. Clique em **enviar** e rever o corpo da resposta. 
    
    A resposta inclui a entrada POI para o campo de Safeco com uma categoria de poi de "stadium". 
    
4. Adicione o seguinte valor para a cadeia de consulta e clique em **enviar**:
    ```
        ,number
    ```
    Se o [número](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parâmetro de consulta é enviado com o pedido, a resposta pode incluir o lado da rua (esquerda/direita) e também uma posição de deslocamento para esse número.
    
5. Adicione o seguinte valor para a cadeia de consulta e clique em **enviar**:
    ```
        ,spatialKeys
    ```

    Quando o [spatialKeys](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parâmetro de consulta está definido, a resposta contém proprietárias georreplicação geográficos as informações de chave numa localização especificada.

6. Adicione o seguinte valor para a cadeia de consulta e clique em **enviar**:
    ```
        ,returnSpeedLimit
    ```
    
    Quando o [returnSpeedLimit](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parâmetro de consulta está definido, a resposta devolver do que o limite de velocidade publicado.

7. Adicione o seguinte valor para a cadeia de consulta e clique em **enviar**:
    ```
        ,returnRoadUse
    ```

    Quando o [returnRoadUse](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parâmetro de consulta está definido, a resposta devolve matriz de utilização da estrada para reversegeocodes nível completo.

8. Adicione o seguinte valor para a cadeia de consulta e clique em **enviar**:
    ```
        ,roadUse
    ```

    Pode restringir a consulta inversa geocode a um tipo específico de utilização de utilização da estrada o [roadUse](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) parâmetro de consulta.
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Procurar Rua cruzada utilizando pesquisa completo entre endereços inversa

1. No Postman, clique em **novo pedido** | **pedido GET** e dê-lhe nome **inverso endereço cruzada pesquisa Rua**.

2. No separador Builder, selecione o **obter** método HTTP e introduza o URL do pedido para o ponto final de API.
    
    ![Inverter os endereços entre Rua pesquisa ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parâmetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | Método HTTP | GET |
    | URL do pedido | https://Atlas.microsoft.com/search/Address/reverse/crossstreet/JSON? |
    | Autorização | Sem autenticação |
    
3. Clique em **Params**e introduza a seguinte chave / valor pares a utilizar como parâmetros de consulta ou o caminho no URL do pedido:
    
    | Chave | Valor |
    |------------------|-------------------------|
    | versão de API | 1.0 |
    | chave de subscrição | *chave de subscrição* |
    | consulta | 47.59093,-122.33263 |
    
4. Clique em **enviar** e rever o corpo da resposta. 

## <a name="next-steps"></a>Passos seguintes
- Explorar o [serviço da Azure localização com base Serices Search](https://docs.microsoft.com/en-us/rest/api/location-based-services/search) documentação da API 
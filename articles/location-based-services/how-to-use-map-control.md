---
title: "Como utilizar o Azure localização com base dos serviços de controlo de mapa | Microsoft Docs"
description: "Saiba como utilizar a biblioteca de Javascript do lado do cliente de controlo de mapa de serviços de com base na localização do Azure."
services: location-based-services
keywords: "Não adicionar ou editar as palavras-chave sem consultadoria seu perito de SEO."
author: philmea
ms.author: philmea
ms.date: 11/22/2017
ms.topic: how-to
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: 9ddd11806accddb41c02c0c6681aac07bba25356
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>Como utilizar o Azure localização com base dos serviços de controlo de mapa
A biblioteca de Javascript do lado do cliente de controlo de mapa permite-lhe compor maps e a funcionalidade de com base dos serviços de localização do Azure incorporada no seu web ou aplicação móvel. 

## <a name="prerequisites"></a>Pré-requisitos
Uma chave de conta e subscrição de com base dos serviços de localização do Azure. Para obter informações sobre como criar uma conta e obter uma chave de subscrição, consulte [como gerir a sua conta com base dos serviços de localização do Azure e as chaves](how-to-manage-account-keys.md). 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>Criar um mapa de novo numa página web utilizando a API de controlo de mapa
Pode incorporar um mapa de uma página web através da biblioteca de Javascript do lado do cliente de controlo de mapa.

1. Criar um novo ficheiro e nome MapSearch.html.

2. Adicione as referências de origem de com base dos serviços de localização do Azure folha de estilos e script, para o `<head>` elemento do ficheiro:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. Para compor um mapa de novo no seu browser, adicione um **#map** referência no `<style>` elemento.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Para inicializar o controlo de mapa, definir uma nova secção no corpo html e criar um script. Utilize a sua própria chave de subscrição da sua conta com base dos serviços de localização do Azure. 

    ```html
    <div id="map">
        <script>
            var subscriptionKey = "<_subscriptionKey_>";
            var map = new atlas.Map("map", {
                "subscription-key": subscriptionKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        <script>
    <div>
    ```
    
5. Abra o ficheiro no seu browser e visualizar o mapa composto.
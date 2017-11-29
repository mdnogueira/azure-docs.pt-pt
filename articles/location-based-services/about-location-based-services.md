---
title: "Descrição geral da localização do Azure com base em serviços | Microsoft Docs"
description: "Uma introdução ao Azure com base dos serviços de localização (pré-visualização)"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c8ebce06a72bcaf769a11ec954702463d7489aa0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Uma introdução ao Azure com base dos serviços de localização (pré-visualização)
Localização com base dos serviços do Azure é um portefólio de serviços de geoespacial que incluem APIs de serviço para o Maps, pesquisa, encaminhamento, o tráfego e fusos horários. O portefólio de serviços compatíveis OneAPI do Azure permite-lhe utilizar ferramentas de programação familiar para desenvolver rapidamente e soluções de escala que integram informações de localização nas suas soluções do Azure. Localização com base dos serviços do Azure fornece aos programadores de todas as indústrias capacidades poderosas geoespacial packed com imperativo de dados de mapeamento de raiz para fornecer contexto geográfico para aplicações móveis e web. Localização com base dos serviços do Azure é um conjunto de em conformidade de uma API do Azure de APIs REST acompanhado com um controlo de JavaScript baseada na web para tornar o desenvolvimento super fácil, flexível e portátil em vários suportes. 

Localização com base dos serviços do Azure é composta por cinco primários dos serviços aumente aplicações do Azure que requerem contexto geográfico. Cada um dos serviços é explicada detalhadamente abaixo.

**Serviço de composição** – o serviço compor foi concebido para que os programadores criem aplicações móveis à volta de mapeamento e web. O serviço utiliza o quadrícula de alta qualidade gráfico imagens, disponíveis no 19 níveis de zoom ou imagens de mapa de formato de vetor totalmente personalizável.

![Localização do Azure com base em Map.png de serviços](media/about-location-based-services/Introduction_Map.png)

**Encaminhar o serviço** – o serviço de rota baseia-se com várias instruções de modo de transportes e o cálculos de geometria de uma infraestrutura robusta do mundo real. O serviço permite aos programadores calcular as direções através de um número dos modos de levar como carro, camião, bicicletas ou walking; bem, um número de entradas, tais como condições de tráfego, restrições de ponderação ou hazardous transporte essenciais.

![Localização do Azure com base em Route.png de serviços](media/about-location-based-services/Introduction_Route.png)

**Serviço de pesquisa** – o serviço de pesquisa foi concebido para programadores procurar endereços, locais, listagens de negócio por nome ou categoria e outras informações geográficas. O serviço de pesquisa pode também [inverso geocode](https://en.wikipedia.org/wiki/Reverse_geocoding) endereços e streets cruzadas com base num latitude/longitude. 

![Localização do Azure com base em Search.png de serviços](media/about-location-based-services/Introduction_Search.png)

**Serviço de fuso horário** – o fuso horário serviço permite-lhe as informações de fuso horário atual, histórico e futuras da consulta utilizando qualquer um dos pares de latitude longitude ou um [IANA ID](http://www.iana.org/). O serviço de fuso horário também permite a conversão de IDs de fuso horário do Microsoft Windows para IANA fusos horários, a obtenção de um desvio de fuso horário UTC e ao obter a hora atual num fuso horário respetivo. Uma resposta JSON típica para uma consulta para o serviço de fuso horário tem o seguinte aspeto:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

**Serviço de tráfego** – o tráfego de serviço é um conjunto de serviços web concebidos para que os programadores criem aplicações móveis que necessitam de tráfego e web. A oferta é dividida no seguinte:
1. Fluxo de tráfego - fornece velocidades observadas em tempo real e viajam vezes para todos os roads chaves na rede; e, 
2. Tráfego incidentes - fornece uma vista exata sobre o tráfego jams e incidentes em torno da rede viagem.

![Localização do Azure com base em tráfego de serviços](media/about-location-based-services/Introduction_Traffic.png)

Localização com base dos serviços do Azure baseia-se para a mobilidade e pode ligar a aplicações de várias plataformas, uma vez que o modelo de programação é agnóstico relativamente e suporta a saída JSON através de REST APIs. Além disso, o Azure LBS oferece um controlo de mapa de JavaScript conveniente com um modelo de programação simple para o desenvolvimento de fácil rápido de aplicações móveis e web. 

Localização com base dos serviços do Azure utiliza um esquema de autenticação baseada em chave, por isso, aceder os serviços é um fim de navegar para o [portal do Azure](http://portal.azure.com) e criar uma conta com base dos serviços de localização do Azure. A conta inclui duas chaves previamente geradas por si. Começar a integrar estas capacidades de localização diretamente para as suas aplicações através das suas chaves nos pedidos para o serviço com base dos serviços de localização do Azure.

Inscrever-se um [com base dos serviços de localização do Azure conta hoje em dia!](http://aka.ms/azurelbsportal)

## <a name="next-steps"></a>Passos seguintes

Agora tem uma descrição geral do Azure com base dos serviços de localização (pré-visualização). O passo seguinte é para experimentar uma aplicação de exemplo que mostram os serviços de localização com base, bem como criar um cenário ponto-a-ponto na sua aplicação web.

> [!div class="nextstepaction"]
> [Inicie uma pesquisa de mapa interativo demonstração através do Azure com base dos serviços de localização (pré-visualização)](quick-demo-map-app.md)
> [pesquisa próximas pontos de interesse utilizando com base dos serviços de localização do Azure](tutorial-search-location.md)
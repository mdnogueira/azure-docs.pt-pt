---
title: "Como aumentar a simultaneidade de um serviço web Azure Machine Learning | Microsoft Docs"
description: "Saiba como aumentar a simultaneidade de um serviço web Azure Machine Learning ao adicionar os pontos finais adicionais."
services: machine-learning
documentationcenter: 
author: neerajkh
manager: srikants
editor: cgronlun
keywords: "do Azure machine learning, serviços web, operationalization, dimensionamento, ponto final, concorrência"
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: neerajkh
ms.openlocfilehash: 2b4fcef51b2704f07f5d1d08a4bd16970864b0fd
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Dimensionar um serviço web Azure Machine Learning adicionando os pontos finais adicionais
> [!NOTE]
> Este tópico descreve as técnicas aplicáveis a um **clássico** serviço Web do Machine Learning. 
> 
> 

Por predefinição, cada serviço Web publicado está configurado para suportar 20 pedidos em simultâneo e pode ser tão elevado como 200 pedidos em simultâneo. O Azure Machine Learning automaticamente otimiza a definição para proporcionar o melhor desempenho para o seu serviço web e o portal valor é ignorado. 

Se o plano para chamar a API com uma carga superior ao valor máx. de chamadas em simultâneo 200 irá suportar, deve criar vários pontos finais no mesmo serviço Web. Em seguida, pode distribuir aleatoriamente a carga em todos eles.

O dimensionamento de um serviço Web é uma tarefa comuns. Algumas razões para dimensionar são suportar mais de 200 pedidos em simultâneo, aumentar a disponibilidade através de vários pontos finais ou forneça pontos finais separados para o serviço web. Pode aumentar a escala adicionando os pontos finais adicionais para o mesmo serviço Web através de [serviço Web do Azure Machine Learning](https://services.azureml.net/) portal.

Para obter mais informações sobre como adicionar novos pontos finais, consulte [criação de pontos finais](create-endpoint.md).

Tenha em atenção que o com uma contagem de concorrência elevado pode ser detrimental se não estiver a chamar a API com uma taxa elevada de proporcionalmente. Poderá ver esporádicas tempos limite e/ou picos de latência de se colocar uma carga relativamente baixa numa API configurada para elevada carga.

As APIs síncronas são normalmente utilizadas em situações em que uma baixa latência for pretendida. Latência aqui implica o tempo que demora para a API concluir um pedido e não uma conta para qualquer atrasos de rede. Imaginemos que tem uma API com uma latência de 50-ms. Para consumir completamente a capacidade disponível com o nível de limitação alta e máx. de chamadas em simultâneo = 20, tem de chamar esta API 20 * 1000 / 50 = 400 vezes por segundo. Expandir esta adicional, um máximo de chamadas em simultâneo de 200 permite-lhe chamar as horas de 4000 de API por segundo, partindo do princípio de uma latência de 50-ms.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png

---
title: "Atualizar e dimensionar uma instância de API Management do Azure | Microsoft Docs"
description: "Este tópico descreve como atualizar e dimensionar uma instância de API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 22cc917eb6f296724bf535e48b0dd6ba8927e5d3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Atualizar e dimensionar uma instância de API Management 

Os clientes podem dimensionar uma instância de gestão de API (APIM) ao adicionar e remover unidades. A **unidade** é composto de recursos do Azure dedicados e tem um determinado carga-efeito capacidade expressada como um número da API de chamadas por mês. Real débito e latência varia amplamente consoante na fatores, tais como o número e a taxa de ligações em simultâneo, o tipo e número de políticas configuradas, pedido e tamanhos de resposta e a latência de back-end. 

Capacidade e o preço de cada unidade depende de um **camada** no qual existe a unidade. Pode escolher entre três camadas: **programador**, **padrão**, **Premium**. Se precisar de aumentar a capacidade para um serviço dentro de uma camada, deve adicionar uma unidade. Se a camada que está atualmente selecionada na sua instância APIM não permitir a adição de mais unidades, terá de atualizar para uma camada de nível mais elevada. 

O preço de cada unidade, a capacidade de adicionar/remover unidades, quer tenha ou não ter determinadas funcionalidades (por exemplo, a implementação de multirregião) depende a camada que escolheu para a instância APIM. O [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artigo, explica que preço por unidade e funcionalidades depara-se em cada camada. 

>[!NOTE]
>O [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artigo mostra o número aproximado de capacidade de unidade em cada camada. Para obter mais exatos números, tem de observar um cenário realista para as suas APIs. Consulte a secção "Como planear a capacidade" que se segue.

## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância APIM. Para obter mais informações, consulte [criar uma instância de API Management do Azure](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Como planear a capacidade?

Para saber se tem suficiente unidades para processar o tráfego, teste em cargas de trabalho que espera. 

Tal como mencionado acima, o número de pedidos por segundo APIM pode processo depende muitas variáveis. Por exemplo, o ligação padrão, o tamanho do pedido e resposta, as políticas que estão configuradas em cada API, o número de clientes que enviam pedidos.

Utilize **métricas** (utiliza as capacidades de monitorização do Azure) para compreender a capacidade é utilizada em qualquer momento.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Utilize o portal do Azure para examinar métricas 

1. Navegue até à sua instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **métricas**.
3. Selecione **capacidade** métrica de **as métricas disponíveis**. 

    A métrica de capacidade, dá-lhe algumas ideia dos quanta capacidade está a ser utilizada no seu inquilino. Pode testar colocando mais e mais carregam e ver o seu escolha de carga. Pode definir um alerta de métrico para informá-lo quando algo que não é esperado está a acontecer. Por exemplo, a instância APIM accedes a capacidade de mais de 5 min. 

    >[!TIP]
    > Pode configurar o alerta para informá-lo quando o serviço que executa baixa na capacidade ou certifique-se chamam para o uma aplicação de lógica será automaticamente dimensionado adicionando uma unidade.

## <a name="upgrade-and-scale"></a>Atualização e escala 

Como mencionado anteriormente, pode escolher entre três camadas: **programador**, **padrão**, **Premium**. O **programador** camada deve ser utilizada para avaliar o serviço; não deve ser utilizada para produção. O **programador** camada não tem o SLA e não é possível aumentar este escalão (unidades de adicionar/remover). 

**Standard** e **Premium** são as camadas de produção tem o SLA e podem ser ampliadas. O **padrão** camada pode ser escalada para até quatro unidades. Pode adicionar qualquer número de unidades para o **Premium** camada. 

O **Premium** camada permite-lhe distribuir uma única instância de gestão de API em qualquer número de regiões do Azure pretendidos. Quando cria um serviço de API Management inicialmente, a instância contém apenas uma unidade e reside numa única região do Azure. A região inicial é designada como o **primário** região. Regiões adicionais podem ser adicionadas facilmente. Ao adicionar uma região, especifique o número de unidades que pretende alocar. Por exemplo, pode ter uma unidade no **primário** região e unidades de cinco por alguma outra região. Pode personalizar qualquer tráfego que tem em cada região. Para obter mais informações, consulte [como implementar uma instância de serviço de API Management do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md).

Pode atualizar e mudar de e para qualquer camada. Tenha em atenção que desatualização pode remover algumas funcionalidades, por exemplo, as VNETs ou implementação de multirregião, quando desatualização para Standard do escalão Premium.

>[!NOTE]
>O processo de atualização ou escala pode demorar entre 15 e 30 minutos para aplicar. Obter a notificação quando terminar.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Utilizar o portal do Azure para atualizar e dimensionar

1. Navegue até à sua instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **escala e preços**.
3. Escolha a camada pretendida.
4. Especifique o número de **unidades** que pretende adicionar. Pode utilizar o controlo de deslize ou escreva o número de unidades.<br/>
    Se escolher o **Premium** camada, primeiro tem de selecionar uma região.
5. Prima **guardar**

## <a name="next-steps"></a>Passos seguintes

[Como implementar uma instância de serviço de API Management do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)


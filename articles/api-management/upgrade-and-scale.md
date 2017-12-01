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
ms.openlocfilehash: e92c1a44b49c64308438184ab8185a90766c5bcf
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Atualizar e dimensionar uma instância de API Management 

Os clientes podem dimensionar uma instância de gestão de API (APIM) ao adicionar e remover unidades. A **unidade** é composto de recursos do Azure dedicados e tem um determinado carga-efeito capacidade expressada como um número da API de chamadas por mês. Este número não representa um limite de chamada, mas sim um valor de débito máximo para permitir o planeamento de capacidade aproximada. Real débito e latência amplamente variam, dependendo de fatores, tais como o número e a taxa de ligações em simultâneo, o tipo e número de políticas configuradas, tamanhos de pedido e resposta e latência de back-end.

Capacidade e o preço de cada unidade depende o **camada** no qual existe a unidade. Pode escolher entre três camadas: **programador**, **padrão**, **Premium**. Se precisar de aumentar a capacidade para um serviço dentro de uma camada, deve adicionar uma unidade. Se a camada que está atualmente selecionada na sua instância APIM não permitir a adição de mais unidades, terá de atualizar para uma camada de nível mais elevada. 

O preço de cada unidade e as funcionalidades disponíveis (por exemplo, a implementação de multirregião) depende a camada que escolheu para a instância APIM. O [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artigo, explica o preço por unidade e funcionalidades que pode obter em cada camada. 

>[!NOTE]
>O [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artigo mostra o número aproximado de capacidade de unidade em cada camada. Para obter mais exatos números, tem de observar um cenário realista para as suas APIs. Consulte a secção "Como planear a capacidade" que se segue.

## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância APIM. Para obter mais informações, consulte [criar uma instância de API Management do Azure](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Como planear a capacidade?

Para saber se tem suficiente unidades para processar o tráfego, teste em cargas de trabalho que espera. 

Tal como mencionado acima, o número de pedidos por segundo que uma unidade APIM pode processar depende muitas variáveis. Por exemplo, o ligação padrão, o tamanho do pedido e resposta, as políticas que estão configuradas em cada API, o número de clientes que enviam pedidos.

Utilize **métricas** (utiliza as capacidades de monitorização do Azure) para compreender a capacidade é utilizada em qualquer momento.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Utilize o portal do Azure para examinar métricas 

1. Navegue até à sua instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **métricas**.
3. Selecione **capacidade** métrica de **as métricas disponíveis**. 

    A métrica de capacidade dá-lhe algumas ideia dos quantidade da capacidade de computação disponíveis que está a ser utilizada no seu inquilino. O valor é derivado de recursos de computação utilizados pelo seu inquilino como memória, CPU e comprimentos de fila de rede. Não é uma medida direta do número de pedidos processados. Pode testar, aumentando a carga de pedido no seu inquilino e de monitorização que valor da métrica capacidade corresponde ao pico de carga. Pode definir um alerta de métrico para informá-lo quando algo de inesperado está a acontecer. Por exemplo, a sua instância APIM excedeu a capacidade máxima esperados durante mais de 10 minutos.

    >[!TIP]
    > Pode configurar alertas para permitem-lhe saber quando o serviço está em execução baixo capacidade ou chamar para uma aplicação lógica que Dimensionar automaticamente através da adição de uma unidade.

## <a name="upgrade-and-scale"></a>Atualização e escala 

Como mencionado anteriormente, pode escolher entre três camadas: **programador**, **padrão**, e **Premium**. O **programador** camada deve ser utilizada para avaliar o serviço; não deve ser utilizada para produção. O **programador** camada não tem o SLA e não é possível aumentar este escalão (unidades de adicionar/remover). 

**Standard** e **Premium** são camadas de produção que tem o SLA e podem ser ampliadas. O **padrão** camada pode ser escalada para até quatro unidades. Pode adicionar qualquer número de unidades para o **Premium** camada. 

O **Premium** camada permite-lhe distribuir uma única instância de gestão de API em qualquer número de regiões do Azure pretendidos. Quando cria um serviço de API Management inicialmente, a instância contém apenas uma unidade e reside numa única região do Azure. A região inicial é designada como o **primário** região. Regiões adicionais podem ser adicionadas facilmente. Ao adicionar uma região, especifique o número de unidades que pretende alocar. Por exemplo, pode ter uma unidade no **primário** região e unidades de cinco por alguma outra região. Pode personalizar o número de unidades para o tráfego que tiver em cada região. Para obter mais informações, consulte [como implementar uma instância de serviço de API Management do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md).

Pode atualizar e mudar de e para qualquer camada. Tenha em atenção que a atualização ou desatualização pode remover algumas funcionalidades - por exemplo, as VNETs ou implementação de multirregião, quando desatualização para Standard do escalão Premium.

>[!NOTE]
>O processo de atualização ou escala pode demorar entre 15 45 minutos para aplicar. Obter a notificação quando terminar.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Utilizar o portal do Azure para atualizar e dimensionar

1. Navegue até à sua instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **escala e preços**.
3. Escolha a camada pretendida.
4. Especifique o número de **unidades** que pretende adicionar. Pode utilizar o controlo de deslize ou escreva o número de unidades.<br/>
    Se escolher o **Premium** camada, primeiro tem de selecionar uma região.
5. Prima **guardar**

## <a name="next-steps"></a>Passos seguintes

[Como implementar uma instância de serviço de API Management do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)


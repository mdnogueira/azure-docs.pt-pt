---
title: Dimensionar a sua capacidade de Power BI Embedded | Microsoft Docs
description: Este artigo explica como dimensionar uma capacidade Power BI Embedded no Microsoft Azure.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: e1ab6a2f52fa56f1e04c6c327796587daf43596e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Dimensionar a sua capacidade de Power BI Embedded

Este artigo explica como dimensionar uma capacidade Power BI Embedded no Microsoft Azure. Dimensionamento permite-lhe aumentar ou diminuir o tamanho da sua capacidade.

Isto pressupõe que criou uma capacidade Power BI Embedded. Se não tiver, consulte [capacidade criar Power BI Embedded no portal do Azure](create-capacity.md) para começar a utilizar.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="scale-a-capacity"></a>Uma capacidade de dimensionamento

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione **mais serviços** > **Power BI Embedded** para ver as suas capacidades.

    ![Mais serviços no portal do Azure](media/scale-capacity/azure-portal-more-services.png)

3. Selecione a capacidade de que pretende dimensionar.

    ![Lista de BI Embedded capacidade Power no portal do Azure](media/scale-capacity/azure-portal-capacity-list.png)

4. Selecione **escalão de preço** em **escala** dentro da sua capacidade.

    ![Opção de escalão de preço em escala](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    O escalão de preço atual é descrito azul.

    ![Atual descrito azul do escalão de preço](media/scale-capacity/azure-portal-current-tier.png)

5. Para aumentar ou reduzir verticalmente, selecione o novo escalão mover para. Se selecionar uma nova camada coloca um contorno azul tracejado à volta a seleção. Selecione **selecione** de dimensionamento para o novo escalão.

    ![Selecione o novo escalão](media/scale-capacity/azure-portal-select-new-tier.png)

    A capacidade de dimensionamento pode demorar um minuto ou dois para concluir.

6. Confirme o escalão visualizando o separador de descrição geral. O escalão de preço atual está listado.

    ![Confirmar escalão atual](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>Passos seguintes

Para interromper ou iniciar a capacidade, consulte [colocar em pausa e iniciar a sua capacidade de Power BI Embedded no portal do Azure](pause-start.md).

Para começar a incorporar o conteúdo do Power BI na sua aplicação, consulte [como incorporar os seus dashboards do Power BI, relatórios e mosaicos](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Mais perguntas? [Tente pedir a Comunidade do Power BI](http://community.powerbi.com/)
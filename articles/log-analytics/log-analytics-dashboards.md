---
title: Criar um dashboard personalizado no Log Analytics do Azure | Microsoft Docs
description: "Este guia ajuda-o a compreender como dashboards de análise de registos podem visualizar a todas as pesquisas de registo guardado, dando-lhe uma lente única para ver o seu ambiente."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a90d9c620221bffbb225fb060b997af2f5e90390
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Criar um dashboard personalizado para ser utilizado na análise de registos

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, não é possível criar novos dashboards ou editar dashboards existentes. 

Este guia ajuda-o a compreender como dashboards de análise de registos podem visualizar a todas as pesquisas de registo guardado, dando-lhe uma lente única para ver o seu ambiente.

![Dashboard de exemplo](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Todos os dashboards personalizados que criar no portal do OMS também estão disponíveis na aplicação móvel OMS. Consulte as páginas seguintes para obter mais informações sobre as aplicações.

* [Aplicação móvel do OMS da Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Aplicação móvel do OMS do iTunes da Apple](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![dashboard móvel](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Como posso criar dashboard?
Para começar, aceda à página de descrição geral do OMS. Verá o **My Dashboard** mosaico no lado esquerdo. Clique nele para desagregar do dashboard.

![Descrição geral](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Adicionar um mosaico
Nos dashboards, mosaicos são ligados à corrente pela sua pesquisas de registo guardado. OMS é fornecido com muitos previamente efetuadas pesquisas de registo guardado, para que possa começar de imediato. Utilize os seguintes passos descrevem como começar.

Na vista de My Dashboard, basta clicar em **personalizar** introduzir modo de personalização.

![Pictorial](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 O painel que abre no lado direito da página mostra todos os pesquisas de registo guardado da sua área de trabalho. Para visualizar uma pesquisa de registo guardado como um mosaico, coloque o cursor sobre uma pesquisa guardada e, em seguida, clique em de **plus** símbolo.

![Adicione peças de mosaicos 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Ao clicar no **plus** símbolo, é apresentado um novo mosaico na vista do My Dashboard.

![Adicione peças de mosaicos 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Editar um mosaico
Na vista de My Dashboard, basta clicar em **personalizar** introduzir modo de personalização. Clique no mosaico que pretende editar. As alterações do painel à direita para editar e fornece uma seleção das opções:

![Editar o mosaico](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Editar o mosaico](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Visualizações de mosaico
Existem três tipos de visualizações de mosaico para escolher entre:

| tipo de gráfico | o que faz |
| --- | --- |
| ![Gráfico de barras](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Apresenta uma linha cronológica de resultados da sua pesquisa de registo guardado como um gráfico de barras ou uma lista de resultados por um campo, dependendo de se a sua pesquisa de registo agrega os resultados por um campo ou não. |
| ![Métrica](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Apresenta os pedidos de resultado de pesquisa do total do registo como um número num mosaico. Métricos mosaicos permitem-lhe definir um limiar que será realce o mosaico quando o limiar for atingido. |
| ![linha](./media/log-analytics-dashboards/oms-dashboards-line.png) |Apresenta uma linha cronológica os pedidos de resultado de pesquisa de registo guardado com valores de como um gráfico de linhas. |

### <a name="threshold"></a>Limiar
Pode criar um limiar num mosaico utilizando a visualização de métrica. Selecione em criar um valor de limiar no mosaico. Escolha se pretende realçar o mosaico quando o valor é sobre ou abaixo do limiar escolhido, em seguida, defina o valor de limiar abaixo.

## <a name="organizing-the-dashboard"></a>Organizar o dashboard
Para organizar os seu dashboard, navegue para a vista de My Dashboard e clique em **personalizar** introduzir modo de personalização. Clique e arraste o mosaico que pretende mover e movê-la para onde pretende que o mosaico para ser.

![Organizar o Dashboard](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Remover um mosaico
Para remover um mosaico, navegue para a vista de My Dashboard e clique em **personalizar** introduzir modo de personalização. Selecione o mosaico que pretende remover, em seguida, no painel direito, selecione **remover mosaico**.

![Remover um mosaico](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Passos seguintes
* Criar [alertas](log-analytics-alerts.md) na análise de registos para gerar notificações e para remediar problemas.

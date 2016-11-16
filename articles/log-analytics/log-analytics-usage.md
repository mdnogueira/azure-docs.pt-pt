---
title: "Analisar a utilização de dados do Log Analytics | Microsoft Docs"
description: "Pode utilizar a página de Utilização do Log Analytics para ver a quantidade de dados que está a ser enviada para o serviço OMS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/11/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4c1d0319e768eec69718d8b49db30069ec1331d9


---
# <a name="analyze-data-usage-in-log-analytics"></a>Analisar a utilização de dados do Log Analytics
O Log Analytics no Operations Management Suite (OMS) recolhe dados e envia-os para o serviço OMS periodicamente.  Pode utilizar a página **Utilização** para ver a quantidade de dados que está a ser enviada para o serviço OMS. A página **Utilização** também apresenta a quantidade de dados que estão a ser enviados diariamente pelas soluções e com que frequência os servidores estão a enviar dados.

> [!NOTE]
> Se tiver uma conta gratuita criada utilizando o [site do OMS](http://www.microsoft.com/oms), está limitado ao envio de 500 MB de dados para o serviço OMS diariamente. Se atingir o limite diário, a análise de dados parará e será retomada no início do dia seguinte. Também será necessário reenviar quaisquer dados que não tenham sido aceites ou processados pelo OMS.
> 
> 

Pode ver a utilização utilizando o mosaico **Utilização** no dashboard **Descrição geral** do OMS.

![mosaico de utilização](./media/log-analytics-usage/usage-tile.png)

Se tiver excedido o limite de utilização diária ou se estiver perto do limite, opcionalmente, pode remover uma solução para reduzir a quantidade de dados que envia para o serviço OMS. Para mais informações sobre a remoção de soluções, veja [Adicionar soluções Log Analytics a partir da Galeria de Soluções](log-analytics-add-solutions.md).

![dashboard de utilização](./media/log-analytics-usage/usage-dashboard.png)

A página **Utilização** apresenta as seguintes informações:

* utilização média por dia
* utilização de dados para cada solução nos últimos 30 dias
* quantidade de dados que os servidores do ambiente estão a enviar para o serviço OMS nos últimos 30 dias
* escalão de preço do plano de dados e custo estimado
* informações sobre o contrato de nível de serviço (SLA), incluindo o período de tempo que o OMS demora a processar os dados

## <a name="to-work-with-usage-data"></a>Trabalhar com dados de utilização
1. Na página **Descrição geral**, clique no mosaico **Utilização**.
2. Na página **Utilização**, veja as categorias de utilização que apresentem áreas com as que está preocupado.
3. Se tiver uma solução que está a consumir demasiada quantidade da quota de carregamento diária, poderá considerar remover essa solução.

## <a name="to-view-your-estimated-cost-and-billing-information"></a>Ver as informações de faturação e o custo estimado
1. Na página **Descrição geral**, clique no mosaico **Utilização**.
2. Na página **Utilização**em **Utilização**, clique na divisa (**>**) junto a **Custo estimado**.
3. Nos detalhes do **Plano de dados** expandidos, pode ver o custo mensal estimado.  
    ![Plano de dados](./media/log-analytics-usage/usage-data-plan.png)
4. Se pretender ver as informações de faturação, clique em **Ver faturação** para ver as informações de subscrição.
   * Na página subscrições, clique na sua subscrição para ver os detalhes e uma lista de itens de linha de utilização.  
       ![subscrição](./media/log-analytics-usage/usage-sub01.png)
   * Na página Resumo relativa à subscrição, pode executar uma variedade de tarefas para gerir e ver mais detalhes sobre a subscrição.  
       ![detalhes da subscrição](./media/log-analytics-usage/usage-sub02.png)

## <a name="to-view-data-batches-for-your-sla"></a>Ver lotes de dados para o SLA
1. Na página **Descrição geral**, clique no mosaico **Utilização**.
2. Em **Contrato de Nível de Serviço**, clique em **Transferir detalhes do SLA**.
3. Um ficheiro XLSX do Excel é transferido para revisão.  
    ![detalhes SLA](./media/log-analytics-usage/usage-sla-details.png)

## <a name="next-steps"></a>Passos seguintes
* Consulte [Pesquisas de registos do Log Analytics](log-analytics-log-searches.md) para ver informações detalhadas recolhidas pelas soluções.




<!--HONumber=Nov16_HO2-->



---
title: "Gerir o custo de retenção de dados no Log Analytics do Azure | Microsoft Docs"
description: "Saiba como alterar a política de retenção de dados e o plano de preços para a área de trabalho de análise de registos no portal do Azure."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 35064c792b72222d59b1d3f0913a92a4a2b34612
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>Gerir o custo de retenção de dados com a sua área de trabalho de análise de registos
Quando se inscreve para análise de registos, consoante o plano de que selecionar, não há um limite de tempo durante o qual os dados gerados pelas suas origens ligadas são armazenados na sua área de trabalho.  Este artigo realça as considerações que podem influenciar os custos para reter estes dados para diferentes períodos de tempo e como configurar esse limite.   

Porque a análise de registos pode consumir grandes quantidades de dados a partir de origens no local, ambientes de nuvem e híbridos, o custo do armazenamento de dados durante um período de tempo podem ser considerável consoante os seguintes fatores:

* Número de sistemas, os componentes de infraestrutura, recursos de nuvem, etc., que está a recolher
* Tipo de dados criados pela origem, tais como filas de mensagens, os registos, eventos, dados relacionados com segurança ou métricas de desempenho
* Volume de dados gerados por estas origens 
* Número de solução de gestão ativado, a origem de dados e a frequência de recolha

> [!NOTE]
> Consulte a documentação de cada solução como fornece uma estimativa da quantidade de dados recolhe.   

Se tiver o *livres* plano, dados estão limitados a retenção de sete dias.  Para o *autónomo* ou *Paid* camada, os dados recolhidos estão disponíveis nos últimos 31 dias.  

Ao utilizar o *livres* planear, se achar que excede consistentemente as quantidades permitidas, pode alterar a sua área de trabalho para um plano pago para recolher dados além este limite. 

> [!NOTE]
> Se optar por selecionar um período de retenção mais longo para a camada paga são aplicáveis encargos. Pode alterar o tipo de plano em qualquer altura e para obter mais informações sobre preços, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/log-analytics/). 

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados 

1. Inicie sessão no [Portal do Azure](http://portal.azure.com). 
2. Clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
3. No painel de subscrições de análise de registos, selecione a área de trabalho para modificar a partir da lista.
4. Na página da área de trabalho, clique em **retenção** do painel da esquerda.
5. No painel de retenção de área de trabalho, mova o controlo de deslize para aumentar ou reduzir o número de dias e, em seguida, clique em **guardar**.  Se tiver o *livre* camada, não será possível modificar o período de retenção de dados e tem de atualizar para o escalão pago para controlar esta definição.<br><br> ![Alteração da definição de retenção de dados de área de trabalho](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>Passos seguintes  

Para determinar a quantidade de dados é recolhido, as origens de estão a enviar e os diferentes tipos de dados enviados para o ajudar a gerir consumo e o custo, consulte [analisar a utilização de dados na análise de registos](log-analytics-usage.md)
---
title: "Recomendações do Advisor custo do Azure | Microsoft Docs"
description: "Utilize o Advisor do Azure para otimizar o custo das implementações do Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 51320d93689da3e37c0946d8877b27a11793d9c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-cost-recommendations"></a>Recomendações de custo do Assistente

Ajuda-o Advisor otimizar e reduzir o Azure geral passam por identificar recursos inativos e subutilizados. Pode obter custo recomendações do **custo** separador no dashboard do Advisor.

![Separador de custo do Advisor](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>Otimizar a máquina virtual passam por redimensionamento subutilizadas instâncias 
Apesar de determinados cenários de aplicação podem resultar numa utilização baixa por predefinição, muitas vezes, pode poupar dinheiro ao gerir o tamanho e número de máquinas virtuais. Advisor monitoriza a utilização da máquina virtual nos últimos 14 dias e, em seguida, identifica as máquinas virtuais de baixa utilização. Máquinas virtuais cuja utilização do CPU é 5 porcento ou menos e utilização de rede é 7 MB ou menos, de quatro ou mais dias são considerados máquinas de virtuais baixa utilização.

Advisor mostra o custo estimado continuar a executar a máquina virtual, para que possa escolher encerrá-la ou redimensioná-la.  

![Advisor custo recomendações para redimensionar máquinas virtuais](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Utilizar uma solução económica para gerir os objetivos de desempenho de várias bases de dados do SQL Server
Advisor identifica as instâncias do SQL Server que podem beneficiar do criar conjuntos de bases de dados elásticas. Conjuntos de bases de dados elásticas fornecem uma solução simple e rentável para gerir os objetivos de desempenho de várias bases de dados que tenham diferentes padrões de utilização. Para obter mais informações sobre conjuntos elásticos do Azure, consulte [o que é um conjunto elástico do Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

![Advisor custo recomendações para conjuntos de bases de dados elásticas](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como as recomendações de custo de acesso no Azure Advisor

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **mais serviços**.

3. No painel de menu de serviço, em **monitorização e gestão**, clique em **Azure Advisor**.  
 É apresentado o dashboard do Advisor.

4. No dashboard do Advisor, clique em de **custo** separador.

5. Selecione a subscrição para o qual pretende receber recomendações e, em seguida, clique em **obter recomendações**.

> [!NOTE]
> Para obter recomendações do Assistente de acesso, primeiro tem *registar a sua subscrição* com o Advisor. Uma subscrição é registada quando um *subscrição proprietário* inicia o dashboard do Advisor e clica no **obter recomendações** botão. Este é um *operação única*. Depois da subscrição está registada, pode aceder recomendações do assistente como *proprietário*, *contribuinte*, ou *leitor* para uma subscrição, um grupo de recursos ou um recurso específico.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de desempenho do Assistente](advisor-cost-recommendations.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-cost-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-cost-recommendations.md)

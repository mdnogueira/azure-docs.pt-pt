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
ms.openlocfilehash: 7b9c7037271fabd67c1ada80420ad72c340e46bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-cost-recommendations"></a>Recomendações de custo do Assistente

Ajuda-o Advisor otimizar e reduzir o Azure geral passam por identificar recursos inativos e subutilizados. Pode obter custo recomendações do **custo** separador no dashboard do Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar a máquina virtual se gasta, redimensionamento ou encerrar subutilizadas instâncias 
Apesar de determinados cenários de aplicação podem resultar numa utilização baixa por predefinição, muitas vezes, pode poupar dinheiro ao gerir o tamanho e número de máquinas virtuais. Advisor monitoriza a utilização da máquina virtual nos últimos 14 dias e, em seguida, identifica as máquinas virtuais de baixa utilização. Máquinas virtuais cuja utilização do CPU é 5 porcento ou menos e utilização de rede é 7 MB ou menos, de quatro ou mais dias são considerados máquinas de virtuais baixa utilização.

Advisor mostra o custo estimado continuar a executar a máquina virtual, para que possa escolher encerrá-la ou redimensioná-la.

Se pretender ser mais agressiva em identificar subutilizadas máquinas virtuais, pode ajustar a regra de utilização da CPU média numa base por subscrição.

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Utilizar uma solução económica para gerir os objetivos de desempenho de várias bases de dados do SQL Server
Advisor identifica as instâncias do SQL Server que podem beneficiar do criar conjuntos de bases de dados elásticas. Conjuntos de bases de dados elásticas fornecem uma solução simple e rentável para gerir os objetivos de desempenho de várias bases de dados que tenham diferentes padrões de utilização. Para obter mais informações sobre conjuntos elásticos do Azure, consulte [o que é um conjunto elástico do Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir os custos, eliminando não aprovisionados circuitos do ExpressRoute
Advisor identifica os circuitos ExpressRoute que tenham sido do Estado de fornecedor de *não aprovisionado* para mais de um mês e recomenda a eliminar o circuito, se não estão a planear para aprovisionar o circuito com a conectividade fornecedor.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como aceder recomendações de custo no Azure Advisor

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2.  No dashboard do Advisor, clique em de **custo** separador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de desempenho do Assistente](advisor-cost-recommendations.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-cost-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-cost-recommendations.md)

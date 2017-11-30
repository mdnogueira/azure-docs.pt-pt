---
title: "Migrar máquinas após a avaliação com o Azure migrar | Microsoft Docs"
description: "Descreve como obter as recomendações para migrar máquinas depois de executar uma avaliação com o serviço Azure migrar."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 809c6e85-0928-45e2-a7c7-6824d860e134
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 083e614628645d2eb94b7d84f4127b690865d770
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-machines-after-assessment"></a>Migrar máquinas após a avaliação


[Migrar do Azure](migrate-overview.md) avalia máquinas no local para verificar se serem adequados para a migração para o Azure e fornece estimativas de dimensionamento e custos de execução da máquina no Azure. Atualmente, Azure migrar apenas avalia máquinas para migração. A migração propriamente dito está a ser efetuada através de outros serviços do Azure.

Este artigo descreve como obter sugestões para uma ferramenta de migração após executar uma avaliação de migração.

## <a name="migration-tool-suggestion"></a>Sugestão da ferramenta de migração

Para obter sugestões sobre as ferramentas de migração, tem de efetuar a deteção profunda do ambiente no local. A deteção profunda é feita através da instalação de agentes nos computadores no local.  

1. Criar um projeto do Azure migrar, detetar máquinas no local e criar uma avaliação de migração. [Saiba mais](tutorial-assessment-vmware.md).
2. Transfira e instale os agentes do Azure migrar em cada máquina no local para o qual pretende ver um método de migração recomendada. [Siga este procedimento](how-to-create-group-machine-dependencies.md#prepare-machines-for-dependency-mapping) para instalar os agentes.
2. Identifica as máquinas no local que são adequadas para a migração de comparação de precisão e shift. Estas são as VMs que não requerem quaisquer alterações nas aplicações executadas nos mesmos e podem ser migradas conforme está.
3. Para a migração de comparação de precisão e shift, sugerimos utilizando o Azure Site Recovery. [Saiba mais](../site-recovery/tutorial-migrate-on-premises-to-azure.md). Em alternativa, pode utilizar ferramentas de terceiros que suportam a migração para o Azure.
4. Se tiver máquinas no local que não são adequadas para uma migração de comparação de precisão e shift, ou seja, se pretender migrar aplicação específica em vez de uma toda a VM, pode utilizar outras ferramentas de migração. Por exemplo, sugerimos que o [serviço de migração de base de dados do Azure](https://azure.microsoft.com/campaigns/database-migration/) se pretender migrar no local as bases de dados deste tipo um SQL Server, MySQL ou Oracle para o Azure.


## <a name="review-suggested-migration-methods"></a>Reveja os métodos de migração sugerida

1. Antes de poder obter um método de migração sugeridos, terá de criar um projeto do Azure migrar, detetar máquinas no local e executar uma avaliação de migração. [Saiba mais](tutorial-assessment-vmware.md).
2. Depois de criada a avaliação, vê-la no projeto > **descrição geral** > **Dashboard**. Clique em **preparação de avaliação**

    ![Preparação de avaliação](./media/tutorial-assessment-vmware/assessment-report.png)  

3. No **sugerida ferramenta**, reveja as sugestões para ferramentas que pode utilizar para a migração.

    ![Ferramenta sugerida](./media/tutorial-assessment-vmware/assessment-suitability.png) 




## <a name="next-steps"></a>Passos seguintes

[Saiba mais](concepts-assessment-calculation.md) sobre como avaliações são calculadas.

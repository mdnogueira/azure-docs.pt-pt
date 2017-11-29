---
title: "Visualização de dependência no Azure migrar | Microsoft Docs"
description: "Fornece uma descrição geral de cálculos de avaliação no serviço Azure migrar."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 78e52157-edfd-4b09-923f-f0df0880e0e0
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: a8a8cee327dac8adfb0ae53d101c382ef20599d2
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="dependency-visualization"></a>Visualização de dependência

O [Azure migrar](migrate-overview.md) serviços avalia os grupos de computadores no local para a migração para o Azure. Para agrupar máquinas, pode utilizar a visualização de dependência. Este artigo fornece informações sobre esta funcionalidade.


## <a name="overview"></a>Descrição geral

Visualização de dependência na migrar do Azure permite-lhe criar grupos para avaliar a migração com maior confiança. Utilizar a visualização de dependência pode ver as dependências de rede de máquinas específicas ou através de um grupo de computadores. Isto é útil para se certificar de que nenhuma funcionalidade ou perdido (ou máquinas esquecidas) no processo de migração, quando as aplicações e cargas de trabalho executadas em várias máquinas.  

## <a name="how-does-it-work"></a>Como funciona?

Azure migrar utiliza o [mapa de serviço](../operations-management-suite/operations-management-suite-service-map.md) solução [Log Analytics](../log-analytics/log-analytics-overview.md) para visualização de dependência.
- Quando cria um projeto de migração do Azure, é criada uma área de trabalho de análise de registos do OMS na sua subscrição.
- O nome da área de trabalho é o nome que especificar para o projeto de migração, o prefixo **migrar-**e, opcionalmente, o sufixo com um número. 
- Navegue para a área de trabalho de análise de registos do **Essentials** secção do projeto **descrição geral** página.
- A área de trabalho criada é etiquetada com a chave **MigrateProject**e o valor **nome do projeto**. Pode utilizá-las para procurar no portal do Azure.  

    ![Área de trabalho de análise de registo](./media/concepts-dependency-visualization/oms-workspace.png)

Para utilizar a visualização de dependência, terá de transferir e instalar agentes em cada máquina no local que pretende analisar.  

## <a name="do-i-need-to-pay-for-it"></a>É necessário pagar para o mesmo?

Sim. A área de trabalho de análise de registos é criada por predefinição, mas não é utilizada a menos que utilize a visualização de dependência no Azure migrar. Se utilizar a visualização de dependência (ou utilizar a área de trabalho fora do Azure migrar), são-lhe cobrados para utilização de área de trabalho.  [Saiba mais](https://azure.microsoft.com/pricing/details/insight-analytics/) sobre os preços da solução de mapa de serviço. 

## <a name="how-do-i-manage-the-workspace"></a>Como gerir a área de trabalho?

Pode utilizar a área de trabalho do Log Analytics fora do Azure migrar. Não é eliminada se eliminar o projeto de migração no qual foi criado. Se já não necessita de área de trabalho, [eliminá-la](../log-analytics/log-analytics-manage-access.md) manualmente.

Não elimine a área de trabalho criada pelo Azure migrar, a menos que elimine o projeto de migração. Se o fizer, dependências não funcionam conforme esperado.

## <a name="next-steps"></a>Passos seguintes

[Máquinas de grupo utilizando dependências de máquina](how-to-create-group-machine-dependencies.md)
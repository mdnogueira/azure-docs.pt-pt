---
title: "Recuperação de continuidade e desastre de negócio (BCDR): Azure emparelhado regiões | Microsoft Docs"
description: "Saiba mais sobre o emparelhamento regional do Azure, para assegurar que aplicações são resilientes durante falhas de centro de dados."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: c2d0a21c-2564-4d42-991a-bc31723f61a4
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: raynew
ms.openlocfilehash: 4a846cc3e2f06199bdef9e597198f309801d5c75
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Recuperação de continuidade e desastre de negócio (BCDR): Azure emparelhado regiões

## <a name="what-are-paired-regions"></a>Quais são emparelhados regiões?

Azure funciona em várias localizações geográficas em todo o mundo. Uma geografia do Azure é uma área definida o mundo que contenha pelo menos uma região do Azure. Uma região do Azure é uma área dentro de uma geografia, que contém um ou mais dos centros de dados.

Cada região do Azure se encontra emparelhado com outro região dentro da mesma geografia, em conjunto, tornando um par regional. A exceção é sul do Brasil, que se encontra emparelhado com uma região fora do respetiva geografia.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1 – diagrama par regional do Azure

| Geografia | Regiões emparelhadas |  |
|:--- |:--- |:--- |
| Ásia |Ásia Oriental |Sudeste Asiático |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Canadá |Canadá Central |Leste do Canadá |
| China |China Norte |Leste da China|
| Índia |Índia Central |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia |Coreia Central |Coreia do Sul |
| América do Norte |EUA Centro-Norte |EUA Centro-Sul |
| América do Norte |EUA Leste |EUA Oeste |
| América do Norte |EUA Leste 2 |EUA Central |
| América do Norte |EUA Oeste 2 |EUA Centro-Oeste |
| Europa |Europa do Norte |Europa Ocidental |
| Japão |Leste do Japão |Oeste do Japão |
| Brasil |Sul do Brasil (1) |EUA Centro-Sul |
| Governo dos Estados Unidos da América |E.u. a US Iowa (2) |Gov (US) - Virginia |
| Governo dos Estados Unidos da América |E.u. a US Virginia (3) |Gov (US) - Texas |
| Governo dos Estados Unidos da América |Gov (US) - Arizona |Gov (US) - Texas |
| E.u. a departamento da defesa |US DoD Leste |US DoD Centro |
| RU |Reino Unido Oeste |Reino Unido Sul |
| Alemanha |Alemanha Central |Alemanha Nordeste |

Tabela 1 - mapeamento de pares de regionais do Azure

> (1) sul do Brasil é exclusivo, porque esta se encontra emparelhada com uma região fora da sua própria geografia. Região secundária do Sul do Brasil for Sul Central dos EUA, mas da Sul Central-nos região secundária não sul do Brasil.
>
> (2) região secundária da e.u. a US Iowa for Virginia us-nos, mas região secundária de US-nos Virginia não Iowa us-nos.
> 
> (3) região secundária de Virginia us e.u. a for us-nos Texas, mas região secundária dos EUA us Texas não Virginia us-nos.


Recomendamos que replicar as cargas de trabalho em pares regionais beneficiar das políticas de isolamento e de disponibilidade do Azure. Por exemplo, as atualizações do sistema do Azure planeada são implementadas sequencialmente (e não ao mesmo tempo) em regiões emparelhadas. Isto significa que mesmo no evento raro de uma atualização defeituoso, ambas as regiões não serão afetadas em simultâneo. Além disso, na improvável eventualidade de uma falha de abrangente, recuperação de, pelo menos, uma região sem cada par é definida.

## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhados
Figura 2 abaixo mostra uma aplicação hipotético que utiliza o par regional para recuperação após desastre. Os números verdes realce as atividades por várias regiões de três serviços do Azure (Azure computação, armazenamento, base de dados e) e como estão configuradas para replicar em regiões. As vantagens exclusivas da implementação em regiões emparelhadas são realçadas por laranja números.

![Descrição geral dos benefícios de região emparelhado](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – hipotético par regional do Azure

## <a name="cross-region-activities"></a>Atividades por várias regiões
Como referido na figura 2.

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **(PaaS) de computação do Azure** – terá de aprovisionar os recursos de computação adicionais com antecedência para garantir que estão disponíveis recursos noutra região durante um desastre. Para obter mais informações, consulte [orientações técnica do Azure resiliência](resiliency/resiliency-technical-guidance.md).

![Armazenamento](./media/best-practices-availability-paired-regions/2Green.png) **Storage do Azure** -armazenamento Georredundante (GRS) está configurado por predefinição quando é criada uma conta de armazenamento do Azure. Com a GRS, os dados automaticamente são replicados três vezes numa região principal e três vezes na região emparelhada. Para obter mais informações, consulte [as opções de redundância do armazenamento do Azure](storage/common/storage-redundancy.md).

![SQL do Azure](./media/best-practices-availability-paired-regions/3Green.png) **bases de dados do Azure SQL** – com o Azure SQL Server Standard Georreplicação, pode configurar a replicação assíncrona de transações para uma região emparelhada. Com premium georreplicação, pode configurar a replicação para qualquer região do mundo; No entanto, é recomendável que implementar estes recursos numa região emparelhada para a maioria dos cenários de recuperação de desastre. Para obter mais informações, consulte [Georreplicação na SQL Database do Azure](sql-database/sql-database-geo-replication-overview.md).

![Gestor de recursos](./media/best-practices-availability-paired-regions/4Green.png) **do Azure Resource Manager** -Resource Manager fornece inerentemente isolamento lógico, gestão de componentes do serviço em regiões. Isto significa lógicas falhas numa região são menos provável é que a afetar a outra.

## <a name="benefits-of-paired-regions"></a>Benefícios das regiões emparelhados
Como referido na figura 2.  

![Isolamento](./media/best-practices-availability-paired-regions/5Orange.png)
**isolamento físico** – quando possível, Azure prefers pelo menos 300 quilómetros de separação entre centros de dados num par de regional, embora isto não é possível ou práticas em todas as localizações geográficas. Separação do Centro de dados físico reduz a probabilidade de perante desastres naturais, unrest civis, falhas de energia ou falhas de rede físico que afetam as regiões de uma só vez. Isolamento está sujeita as restrições na geografia (tamanho de geografia, disponibilidade da infraestrutura de rede/energia, regulamentos, etc.).  

![Replicação](./media/best-practices-availability-paired-regions/6Orange.png)
**replicação plataforma fornecidos pelo** -alguns serviços, como o armazenamento Georredundante proporciona replicação automática para a região emparelhada.

![Recuperação](./media/best-practices-availability-paired-regions/7Orange.png)
**ordem de recuperação de região** – nos eventos de uma falha de abrangente, a recuperação de uma região é definida fora cada par. As aplicações que são implementadas em regiões emparelhadas garantidas ter uma das regiões recuperadas com prioridade. Se uma aplicação é implementada em regiões que não estão emparelhadas, recuperação pode sofrer um atraso – na pior das hipóteses que as regiões escolhidas poderão ser as duas última ser recuperados.

![Atualizações](./media/best-practices-availability-paired-regions/8Orange.png)
**atualizações sequenciais** – atualizações do sistema planeada do Azure estiverem implementadas para regiões emparelhadas sequencialmente (e não ao mesmo tempo) para minimizar o período de indisponibilidade, o efeito de erros e falhas lógicas no evento raro de uma atualização incorreta.

![Dados](./media/best-practices-availability-paired-regions/9Orange.png)
**residency dados** – uma região reside dentro da mesma geografia como respetivo par (à exceção do Sul do Brasil) para satisfazer os requisitos de residency de dados para efeitos de jurisdiction imposição dedução dos impostos e pela lei.

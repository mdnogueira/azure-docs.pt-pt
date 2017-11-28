---
title: "Os cálculos de avaliação no Azure migrar | Microsoft Docs"
description: "Fornece uma descrição geral de cálculos de avaliação no serviço Azure migrar."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 39a63769-31eb-49f9-9089-4d3e4e88a412
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: f00825ff9a5018e67672ce452f01130f84919e52
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="assessment-calculations"></a>Cálculos de avaliação

[Migrar do Azure](migrate-overview.md) avalia cargas de trabalho no local para a migração para o Azure. Este artigo fornece informações sobre como avaliações são calculadas.



## <a name="overview"></a>Descrição geral

Uma avaliação de Azure migrar tem três fases. Avaliação começa com uma análise adequabilidade, seguida de estimativas de dimensionamento com base no desempenho, e por último, mensais custo estimativa. Uma máquina apenas move ao longo para posterior se tenha sido enviado anterior. Por exemplo, se um computador falhar a verificação de adequabilidade do Azure, está marcada como unsuitable para o Azure e dimensionamento e custos a não ser calculado. 


## <a name="azure-suitability-analysis"></a>Análise de adequabilidade do Azure

As máquinas que pretende migrar para o Azure tem de cumprir os requisitos do Azure e limitações. Por exemplo, se um disco da VM no local é mais de 4 TB, não pode ser alojado no Azure. As verificações de conformidade do Azure estão resumidas na tabela seguinte. 

**Verificação** | **Detalhes**
--- | ---
**Tipo de arranque** | O tipo de arranque do disco de SO convidado tem de ser BIOS e UEFI não.
**Núcleos** | O número de núcleos nas máquinas tem de ser igual a (ou inferior a) o número máximo de núcleos (32) suportado para uma VM do Azure.<br/><br/> Se está disponível o histórico de desempenho, Azure migrar considera os núcleos sobreutilizados para comparação. Se não for especificado um fator de comfort nas definições de avaliação, o número de núcleos sobreutilizados é multiplicado pelo fator de comfort.<br/><br/> Se não houver nenhum histórico de desempenho, migrar do Azure utiliza os núcleos alocados, sem aplicar o fator de comfort.
**Memória** | O tamanho de memória da máquina tem de ser igual a (ou inferior a) o máximo de memória (448 GB) permitido para uma VM do Azure. <br/><br/> Se está disponível o histórico de desempenho, Azure migrar considera a memória sobreutilizada para comparação. Se não for especificado um fator de comfort, a memória sobreutilizada é multiplicada pelo fator de comfort.<br/><br/> Se não houver nenhum histórico é utilizada a memória alocada, sem aplicar o fator de comfort.<br/><br/> 
**Windows Server 2003-2008 R2** | suporte de 32 bits e 64 bits.<br/><br/> O Azure oferece melhor suporte de esforço apenas.
**Windows Server 2008 R2 com todos os SPs** | suporte de 64 bits.<br/><br/> O Azure oferece suporte completo.
**Windows Server 2012 e SPs todos os** | suporte de 64 bits.<br/><br/> O Azure oferece suporte completo.
**Windows Server 2012 R2 e SPs todos os** | suporte de 64 bits.<br/><br/> O Azure oferece suporte completo.
**Windows Server 2016 & todos os SPs** | suporte de 64 bits.<br/><br/> O Azure oferece suporte completo.
**Cliente Windows 7 e posterior** | suporte de 64 bits.<br/><br/> O Azure oferece suporte com apenas a subscrição do Visual Studio.
**Linux** | suporte de 64 bits.<br/><br/> O Azure oferece suporte completo para estes [sistemas operativos](../virtual-machines/linux/endorsed-distros.md).
**Disco de armazenamento** | Tamanho alocado de um disco tem de ser 4 TB (4096 GB) ou menos.<br/><br/> O número de discos anexados à máquina tem de ser 65 ou inferior, incluindo o disco do SO. 
**Redes** | Uma máquina tem de ter 32 ou menos NICs anexados ao mesmo.


## <a name="performance-based-sizing"></a>Dimensionamento com base no desempenho

Depois de uma máquina está marcada como adequada para o Azure, Azure migrar mapeia-lo para um tamanho VM no Azure, utilizando os seguintes critérios:

- **Verificação de armazenamento**: Migrar Azure tenta mapear todos os discos ligados à máquina a um disco no Azure:-Azure migrar multiplica a e/s por segundo (IOPS), o fator de comfort. -Múltiplos também o débito (em MBps) de cada disco, o fator de comfort. Isto fornece o eficaz IOPS e débito do disco. Com base nisso, Azure migrar mapeia o disco para um padrão ou disco premium no Azure.
    - Se o serviço não é possível localizar um disco com o IOPS & débito necessário, marca a máquina como unsuitable para o Azure.
    - Se encontrar um conjunto de discos adequados, Azure migrar seleciona aqueles que suportam o método de redundância do armazenamento e a localização especificada nas definições de avaliação.
    - Se existirem vários discos elegíveis, seleciona um com o menor custo.
- **Débito do disco de armazenamento**: [mais](../azure-subscription-service-limits.md#storage-limits) sobre o Azure limita por disco e a VM.
- **Tipo de disco**: Migrar do Azure suporta apenas discos geridos.
- **Verificação de rede**: Migrar Azure tenta localizar uma VM do Azure que consiga suportar o número de NICs na máquina no local.
    - Para tal, agrega os dados transmitidos por segundo (MBps) fora da máquina (rede de saída) em todos os NICs e aplica-se o fator de comfort para o número de agregados. Este número se utilizadas para localizar uma VM do Azure que consiga suportar o desempenho de rede necessários.
    - Migrar do Azure utiliza as definições de rede da VM e parte do princípio de que seja uma rede fora do datacenter.
    - Se não existem dados de desempenho de rede estiver disponíveis, a contagem de NIC é considerada para dimensionamento de VM.
- **Verificação de computação**: depois dos requisitos de armazenamento e rede são calculados, Azure migrar considera que a computação requisitos:
    - Se os dados de desempenho estão disponíveis para a VM, analisa os núcleos sobreutilizados e memória e aplica-se o fator de comfort. Com base nesse número, tenta localizar um tamanho adequado do VM no Azure.
    - Não se for encontrado nenhum tamanho adequado, a máquina está marcada como unsuitable para o Azure.
    - Se for encontrado um tamanho adequado, Azure migrar aplica-se os cálculos de armazenamento e de rede. Em seguida, aplica a localização e as definições de camada, para a recomendação de tamanho VM final de preços.


## <a name="monthly-cost-estimation"></a>Estimativa de custo mensal

Depois de concluídas as recomendações de dimensionamento, o Azure migrar calcula os custos de armazenamento e computação de pós-migração.

- **Custos de computação**: utilizar o tamanho recomendado da VM do Azure, Azure migrar utiliza a API de faturação para calcular o custo mensal para a VM. O cálculo demora o sistema operativo, software assurance da, localização e as definições de moeda na conta. Agrega o custo em todas as máquinas, para calcular o custo mensal total de computação.
- **Custo de armazenamento**: O armazenamento mensal de custos para uma máquina é calculada ao agregar o custo mensal de todos os discos ligados à máquina. Migrar do Azure calcula os custos de armazenamento mensal total por agregar os custos de armazenamento de todas as máquinas. Atualmente, o cálculo não demorar ofertas especificadas nas definições de avaliação na conta.

Os custos são apresentados na moeda especificada nas definições de avaliação. 


## <a name="next-steps"></a>Passos seguintes

[Configurar uma avaliação para VMs de VMware no local](tutorial-assessment-vmware.md)

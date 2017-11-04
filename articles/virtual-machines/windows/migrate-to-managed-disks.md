---
title: Migrar VMs do Azure para discos geridos | Microsoft Docs
description: "Migre máquinas virtuais do Azure criadas utilizando discos não geridos em contas de armazenamento para utilizar discos geridos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: b389151b8a1dd0c7a367f83db968bac7b832897a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs do Azure para discos geridos no Azure

Discos gerida do Azure simplifica a gestão de armazenamento, removendo a necessidade de gerir separadamente as contas de armazenamento.  Também pode migrar as suas VMs do Azure existente para discos geridos para beneficiar do melhor fiabilidade das VMs num conjunto de disponibilidade. Garante que os discos de VMs diferentes num conjunto de disponibilidade será suficientemente isolados umas das outras para evitar ponto único de falhas. Coloca automaticamente discos de VMs diferentes num conjunto de disponibilidade em diferentes unidades de escala de armazenamento (carimbos) que limita o impacto de falhas de unidade de escala de armazenamento únicas provocadas por hardware e falhas de software.
Com base nas suas necessidades, pode escolher entre dois tipos de opções de armazenamento:

- [Os discos Premium geridos](premium-storage.md) -se a unidade de estado sólido (SSD) baseado no suporte de dados de armazenamento que disponibiliza highperformance, suporte de disco de baixa latência para máquinas virtuais em execução I/O intensivo cargas de trabalho. Pode tirar partido da velocidade e o desempenho destes discos ao migrar discos geridos de Premium.

- [Discos padrão geridos](standard-storage.md) utilizar suportes de dados de armazenamento de disco rígido (HDD) com base e melhor se adequam Dev/Test e outras cargas de trabalho de acesso influxo são menos sensíveis a variabilidade de desempenho.

Pode migrar discos geridos nos seguintes cenários:

| Migre...                                            | Ligação de documentação                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Converter autónomo VMs e VMs um conjunto de disponibilidade para discos geridos   | [Converter VMs para utilizar discos geridos](convert-unmanaged-to-managed-disks.md) |
| Uma única VM do clássico para o Gestor de recursos nos discos geridos     | [Migrar uma única VM](migrate-single-classic-to-resource-manager.md)  | 
| Todas as VMs numa vNet do clássico para o Gestor de recursos nos discos geridos     | [Migrar os recursos IaaS do clássico para o Resource Manager](migration-classic-resource-manager-ps.md) e, em seguida, [converter uma VM de discos não geridos para gerido discos](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Plano para a conversão para discos geridos

Esta secção ajuda-o a tomar a decisão de melhor em tipos de disco e de VM.


## <a name="location"></a>Localização

Escolha uma localização onde discos gerida do Azure estão disponíveis. Se estiver a mover os discos Premium gerido, certifique-se também que armazenamento Premium está disponível na região onde estiver a planear mover para. Consulte [serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas em localizações disponíveis.

## <a name="vm-sizes"></a>Tamanhos de VM

Se estiver a migrar discos geridos de Premium, terá de atualizar o tamanho da VM para Premium com capacidade de tamanho de armazenamento disponível na região onde está localizada a VM. Reveja os tamanhos VM que fazem com capacidade de armazenamento Premium. As especificações de tamanho da VM do Azure estão listadas na [tamanhos das virtual machines](sizes.md).
Reveja as características de desempenho de máquinas virtuais que funcionam com o Premium Storage e escolha o tamanho VM mais adequado que melhor se adapta às sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível na VM para o tráfego de disco da unidade.

## <a name="disk-sizes"></a>Tamanhos de disco

**Premium discos geridos**

Existem sete tipos de discos premium gerido que podem ser utilizados com a VM e cada um tem IOPs específicos e débito limites. Tenha em consideração estes limites quando escolher o tipo de disco Premium para VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade e carrega das horas de ponta.

| Tipo de discos Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPs por disco       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Débito por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

**Discos geridos padrão**

Existem sete tipos de discos padrão geridos que podem ser utilizados com a VM. Cada um deles tem capacidade de diferentes, mas tem o mesmo IOPS e limites de débito. Escolha o tipo de discos padrão geridos com base nas necessidades de capacidade da sua aplicação.

| Tipo de Disco Standard  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPs por disco       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Débito por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 

## <a name="disk-caching-policy"></a>Política de colocação em cache em disco

**Premium discos geridos**

Por predefinição, o disco de política de colocação em cache é *só de leitura* para todos os dados discos Premium, e *leitura e escrita* para o disco de sistema operativo Premium ligados à VM. Esta definição de configuração é recomendada para alcançar o desempenho ideal para IOs a aplicação. Pesado de escrita ou só de escrita para discos de dados (por exemplo, ficheiros de registo do SQL Server), desative a colocação em cache no disco, de modo a que pode alcançar um melhor desempenho de aplicações.

## <a name="pricing"></a>Preços

Reveja o [preços para discos geridos](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Preços dos discos de geridos Premium é a mesmo discos Premium não gerido. Mas preços para os discos padrão geridos são diferente de discos padrão de não gerido.



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [discos geridos](managed-disks-overview.md)

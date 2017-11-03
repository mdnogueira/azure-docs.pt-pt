---
title: Migrar do AWS e outras plataformas para discos geridos no Azure | Microsoft Docs
description: "Criar VMs no Azure utilizando VHDs carregados a partir de outras nuvens como AWS ou outras plataformas de Virtualização e tirar partido dos discos gerida do Azure."
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
ms.date: 10/07/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3b427556c589c7cc5205bfda16edc8d891814326
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrar do Amazon Web Services (AWS) e outras plataformas para discos geridos no Azure

Pode carregar ficheiros VHD soluções de Virtualização do AWS ou no local ao Azure para criar as VMs que tirar partido dos discos geridos. Discos do Azure gerida remove a necessidade de gerir contas de armazenamento para VMs IaaS do Azure. Tem de especificar apenas o tipo (Premium ou Standard) e o tamanho do disco tem e o Azure cria e gere o disco para si. 

Pode carregar os VHDs generalizados e especializados. 
- **Generalizado VHD** -apresentou todas as informações da sua conta pessoal removidas utilizando Sysprep. 
- **Especializada VHD** -mantém as contas de utilizador, aplicações e outros dados de estado de original VM. 

> [!IMPORTANT]
> Antes de carregar qualquer VHD para o Azure, deve seguir [preparar um VHD do Windows ou o VHDX para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Cenário                                                                                                                         | Documentação                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Tiver instâncias AWS EC2 existentes que pretende migrar para as VMs do Azure utilizando discos geridos                              | [Mover uma VM do Amazon Web Services (AWS) para o Azure](aws-to-azure.md)                           |
| Tiver uma VM a partir de outra plataforma de Virtualização que pretende utilizar como uma imagem para criar várias VMs do Azure. | [Carregar um VHD generalizado e utilizá-la para criar uma nova VM no Azure](upload-generalized-managed.md) |
| Tiver uma VM de forma exclusiva personalizada que gostaria de recriá-lo no Azure.                                                      | [Carregar um VHD especializado para o Azure e criar uma nova VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Descrição geral dos discos geridos

Discos gerida do Azure simplifica a gestão de VM, removendo a necessidade de gerir contas de armazenamento. Discos geridos pelo também benefício da melhor fiabilidade das VMs num conjunto de disponibilidade. Garante que os discos de VMs diferentes num conjunto de disponibilidade são suficientemente isolados umas das outras para evitar um ponto único de falha. Coloca automaticamente discos de VMs diferentes num conjunto de disponibilidade em diferentes unidades de escala de armazenamento (carimbos) que limita o impacto de falhas de unidade de escala de armazenamento únicas provocadas por hardware e falhas de software. Com base nas suas necessidades, pode escolher entre dois tipos de opções de armazenamento: 
 
- [Os discos Premium geridos](premium-storage.md) são a unidade de estado sólido (SSD) em suportes de dados de armazenamento, que disponibiliza suporte de disco de baixa latência para máquinas virtuais em execução I/O intensivo cargas de trabalho de elevado desempenho. Pode tirar partido da velocidade e o desempenho destes discos ao migrar discos geridos de Premium.  

- [Discos padrão geridos](standard-storage.md) utilizar suportes de dados de armazenamento de disco rígido (HDD) com base e melhor se adequam Dev/Test e outras cargas de trabalho de acesso influxo são menos sensíveis a variabilidade de desempenho.  

## <a name="plan-for-the-migration-to-managed-disks"></a>Planear a migração para discos geridos

Esta secção ajuda-o a tomar a decisão de melhor em tipos de disco e de VM.


### <a name="location"></a>Localização

Escolha uma localização onde discos gerida do Azure estão disponíveis. Se estiver a migrar para os discos Premium gerido, certifique-se também que armazenamento Premium está disponível na região onde estiver a planear migrar para o. Consulte [serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas em localizações disponíveis.

### <a name="vm-sizes"></a>Tamanhos de VM

Se estiver a migrar discos geridos de Premium, terá de atualizar o tamanho da VM para Premium com capacidade de tamanho de armazenamento disponível na região onde está localizada a VM. Reveja os tamanhos VM que fazem com capacidade de armazenamento Premium. As especificações de tamanho da VM do Azure estão listadas na [tamanhos das virtual machines](sizes.md).
Reveja as características de desempenho de máquinas virtuais que funcionam com o Premium Storage e escolha o tamanho VM mais adequado que melhor se adapta às sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível na VM para o tráfego de disco da unidade.

### <a name="disk-sizes"></a>Tamanhos de disco

**Premium discos geridos**

Existem três tipos de discos Premium gerido que podem ser utilizados com a VM e cada um tem IOPs específicos e débito limites. Quando escolher o tipo de disco Premium para VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade, considere estes limites e carrega das horas de ponta.

| Tipo de discos Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Tamanho do disco           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| IOPs por disco       | 500               | 2300              | 5000              |
| Débito por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo |

**Discos geridos padrão**

Existem cinco tipos de discos padrão gerido que podem ser utilizados com a VM. Cada um deles tem capacidade de diferentes, mas tem o mesmo IOPS e limites de débito. Escolha o tipo de discos padrão geridos com base nas necessidades de capacidade da sua aplicação.

| Tipo de Disco Standard  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| IOPs por disco       | 500              | 500              | 500              | 500              | 500              |
| Débito por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo |

### <a name="disk-caching-policy"></a>Política de colocação em cache em disco 

**Premium discos geridos**

Por predefinição, o disco de política de colocação em cache é *só de leitura* para todos os dados discos Premium, e *leitura e escrita* para o disco de sistema operativo Premium ligados à VM. Esta definição de configuração é recomendada para alcançar o desempenho ideal para IOs a aplicação. Pesado de escrita ou só de escrita para discos de dados (por exemplo, ficheiros de registo do SQL Server), desative a colocação em cache no disco, de modo a que pode alcançar um melhor desempenho de aplicações.

### <a name="pricing"></a>Preços

Reveja o [preços para discos geridos](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Preços dos discos de geridos Premium é a mesmo discos Premium não gerido. Mas preços para os discos padrão geridos são diferente de discos padrão de não gerido.


## <a name="next-steps"></a>Passos Seguintes

- Antes de carregar qualquer VHD para o Azure, deve seguir [preparar um VHD do Windows ou o VHDX para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

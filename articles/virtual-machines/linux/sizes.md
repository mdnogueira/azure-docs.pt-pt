---
title: Tamanhos de VM com Linux no Azure | Microsoft Docs
description: "Lista os tamanhos diferentes disponíveis para computadores virtuais Linux no Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 7c6f2e83e5763f3cf8f305c73c0ad74fa8b37c00
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Tamanhos de máquinas virtuais do Linux no Azure
Este artigo descreve os tamanhos disponíveis e as opções para as máquinas virtuais do Azure, que pode utilizar para executar as aplicações de Linux e cargas de trabalho. Também fornece considerações de implementação a ter em consideração quando estiver a planear utilizar estes recursos. Este artigo também está disponível para [máquinas virtuais Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](sizes-general.md)          | B (pré-visualização), Dsv3, Dv3, série DSv2, Dv2, DS, D, Av2, A0-7  | Relação CPU/memória equilibrada. Ideal para teste e desenvolvimento, bases de dados pequenas a médias e servidores Web com tráfego baixo a médio. |
| [Com otimização de computação](sizes-compute.md)        | Fsv2, Fs, F             | Relação CPU/memória elevada. Ideal para servidores Web com tráfego médio, aplicações de rede, processos em lote e servidores de aplicações.        |
| [Com otimização de memória](sizes-memory.md)         | Esv3, Ev3, M, GS, G, série DSv2, DS, Dv2, D   | Rácio de memória a CPU elevado. Ideal para servidores de base de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](sizes-storage.md)        | Ls                | Débito e E/S de disco elevados. Ideal para bases de dados de Macrodados, SQL e NoSQL.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC            | Especializadas máquinas virtuais de destino para composição de gráfico pesada e edição de vídeo. Disponível com GPUs único ou vários.       |
| [Computação de elevado desempenho](sizes-hpc.md) | H, A8-11          | As nossas máquinas virtuais com CPU mais rápidas e poderosas com interfaces de rede de alto débito (RDMA) opcionais. 

<br>

- Para obter informações sobre preços dos tamanhos diversos, consulte [preços das Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Para disponibilidade de tamanhos de VM em regiões do Azure, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/).
- Para ver os limites gerais em VMs do Azure, consulte [subscrição do Azure e limites de serviço, quotas e restrições](../../azure-subscription-service-limits.md).
- Saiba mais sobre como [unidades (ACU) de computação do Azure](../windows/acu.md) podem ajudar a comparar o desempenho de computação em SKUs do Azure.


## <a name="rest-api"></a>REST API

Para obter informações sobre como utilizar a API REST para a consulta para tamanhos de VM, consulte o seguinte:

- [Listar tamanhos de máquina virtual disponíveis para redimensionar](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)
- [Listar tamanhos de máquina virtual disponíveis para uma subscrição](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)
- [Listar tamanhos de máquina virtual disponíveis num conjunto de disponibilidade](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)

## <a name="acu"></a>ACU

Saiba mais sobre como [unidades (ACU) de computação do Azure](acu.md) podem ajudar a comparar o desempenho de computação em SKUs do Azure.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os diferentes tamanhos VM que estão disponíveis:
- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)




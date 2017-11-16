---
title: "Débito de rede de máquina virtual do Azure | Microsoft Docs"
description: "Saiba mais sobre o débito de rede de máquina virtual do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 536a779d7de51180aa6410911dea2b6c47780c2f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="virtual-machine-network-throughput"></a>Débito de rede de máquina virtual

O Azure oferece uma variedade de tamanhos de máquina virtual e tipos, cada um com uma combinação de diferentes das capacidades de desempenho. Entre as capacidades de desempenho é débito (ou de rede largura de banda), medida em megabits por segundo (Mbps). Uma vez que as máquinas virtuais são alojadas no hardware partilhado, a capacidade de rede tem de ser partilhada bastante entre as máquinas virtuais que partilham o mesmo hardware. Máquinas virtuais maiores são alocadas relativamente mais largura de banda inferior das máquinas virtuais.
 
A largura de banda de rede atribuída a cada máquina virtual é limitada no tráfego (saída) de saída da máquina virtual. Todo o tráfego de rede se deixar a máquina virtual é contabilizado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de Mbps 1.000, esse limite aplica-se quer o tráfego de saída é destinado a outra máquina virtual na mesma rede virtual ou fora do Azure.
 
Entrada não limitada ou não está limitada diretamente. No entanto, existem outros fatores, tais como CPU e limites de armazenamento, que podem afetar a capacidade de uma máquina virtual para processar os dados de entrada.

[Acelerados redes](virtual-network-create-vm-accelerated-networking.md) é uma funcionalidade que foi concebida para melhorar o desempenho de rede, incluindo a utilização da CPU, o débito e latência. Enquanto redes na melhoria podem melhorar o débito de uma máquina virtual, esta pode fazê-apenas até a máquina virtual está alocada a largura de banda.
 
Máquinas virtuais do Azure tem de ter um, mas pode ter várias, ligadas aos mesmos de interfaces de rede. Largura de banda atribuída a uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede ligadas a uma máquina virtual. Por outras palavras, a largura de banda alocada é por máquina virtual, independentemente de quantas interfaces de rede ligadas à máquina virtual. Para obter suporte de tamanhos de VM do Azure diferente das interfaces de rede quantos, consulte Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tamanhos de VM. 

## <a name="expected-network-throughput"></a>Débito de rede esperado

Débito de saída esperado e o número de interfaces de rede suportadas por cada tamanho da VM está detalhada na Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tamanhos de VM. Selecione um tipo, tais como objetivo geral, em seguida, selecione uma série de tamanho na página resultante, tais como a série Dv2. Cada série tem uma tabela com as especificações na última coluna com o título da rede **máximo NICs / esperado de desempenho de rede (Mbps)**. 

O limite de débito aplica-se à máquina virtual. Débito não é afetado pelos seguintes fatores:
- **Número de interfaces de rede**: O limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.
- **Acelerados redes**: Embora a funcionalidade pode ser útil alcançar o limite de publicado, não se altera o limite.
- **Destino do tráfego**: todos os destinos contam para o limite de saída.
- **Protocolo**: todo o tráfego de saída através de todos os protocolos conta para o limite.

## <a name="next-steps"></a>Passos seguintes

- [Otimizar o débito de rede para um sistema operativo da máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Débito de rede de teste](virtual-network-bandwidth-testing.md) para uma máquina virtual.
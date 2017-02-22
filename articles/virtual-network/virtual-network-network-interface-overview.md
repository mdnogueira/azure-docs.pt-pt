---
title: Interfaces de rede no Azure | Microsoft Docs
description: "Saiba mais sobre as interfaces de rede do Azure no modelo de implementação Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 3244d5b52785d820698bf26f9bf189de93ef64e4
ms.openlocfilehash: 691b79d7739246dad7191195fa049fd58340c8ff


---
# <a name="network-interfaces-in-azure"></a>Interfaces de rede no Azure
Uma interface de rede (NIC) é a interligação entre uma Máquina Virtual (VM) e a rede de software subjacente. Este artigo explica o que são as interfaces de rede e como são utilizadas no modelo de implementação Azure Resource Manager.

A Microsoft recomenda implementar recursos novos com o modelo de implementação do Resource Manager, mas também pode implementar VMs com a conectividade de rede no modelo de implementação [clássica](virtual-network-ip-addresses-overview-classic.md). Se estiver familiarizado com o modelo clássico, existem diferenças importantes nas redes de VMs no modelo de implementação Resource Manager. Leia o artigo [Virtual machine networking - Classic (Redes de máquinas virtuais - Implementação Clássica)](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) para saber mais sobre as diferenças.

No Azure, uma interface de rede:

1. É um recurso que pode ser criado e eliminado e que tem as suas próprias definições configuráveis.
2. Tem de estar ligado a uma sub-rede numa Rede Virtual do Azure (VNet) quando é criado. Se não estiver familiarizado com asVNets, leia o artigo [Descrição Geral da Rede Virtual](virtual-networks-overview.md) para saber mais. A NIC deve estar ligada a uma VNet que exista na mesma [localização](https://azure.microsoft.com/regions) e [subscrição](../azure-glossary-cloud-terminology.md#subscription) do Azure que essa NIC. Depois de a NIC ser criada, pode alterar a sub-rede à qual está ligada, mas não a VNet a que está ligada.
3. É-lhe atribuído um nome que não pode ser alterado depois de ser criada a NIC. O nome tem de ser exclusivo dentro de um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) do Azure, mas não na subscrição, na localização do Azure nem na VNet à qual está ligado. Regra geral, são criadas várias NICs numa subscrição do Azure. Recomenda-se que crie uma convenção de nomenclatura que torne mais fácil a gestão de várias NICs do que através da utilização de nomes predefinidos. Veja o artigo [Recommended naming conventions for Azure resources (Convenções de nomenclatura recomendadas para recursos do Azure)](../guidance/guidance-naming-conventions.md) para obter sugestões.
4. Pode ser ligada a uma VM, mas só pode ser ligada a uma VM individual que exista na mesma localização que a NIC.
5. Tem um endereço MAC, que é persistido com a NIC enquanto esta permanecer ligada a uma VM. O endereço MAC é persistido, quer a VM seja reiniciada (a partir do sistema operativo) ou parada (desalocada) e iniciada com o portal do Azure, o Azure PowerShell ou a Interface de Linha de Comandos do Azure. Se for desligada de uma VM e ligada a outra, a NIC recebe um endereço MAC diferente. Se a NIC for eliminada, o endereço MAC é atribuído a outras NICs.
6. Tem de ter atribuído um endereço IP dinâmico ou estático *IPv4* **privado** primário.
7. Pode ter um recurso de endereço IP público associado a si.
8. Suporta rede acelerada com virtualização de E/S de raiz única (SR-IOV) para tamanhos de VMs específicos que executem versões específicas do sistema operativo Microsoft Windows Server. Para saber mais sobre esta funcionalidade de PR´R-VISUALIZAÇÃO, leia o artigo [Accelerated networking for a virtual machine (Rede acelerada para máquinas virtuais)](virtual-network-accelerated-networking-powershell.md).
9. Pode receber tráfego não destinado a endereços IP privados atribuídos à mesma, se o reencaminhamento de IP estiver ativado na NIC. Se, por exemplo, uma VM estiver a executar software de firewall, encaminha os pacotes não destinados para os respetivos endereços IP. A VM continua a ter de executar software capaz de encaminhar ou reencaminhar tráfego, embora, para tal, o reencaminhamento de IPs tenha de estar ativado numa NIC.
10. Muitas vezes, é criada no mesmo grupo de recursos que a VM ou a VNet à qual está ligada, mas não é necessário que assim seja.

## <a name="vms-with-multiple-network-interfaces"></a>VMs com várias interfaces de rede
Podem ser ligadas várias NICs a uma VM, mas tenha em atenção o seguinte quando o fizer:  

* O tamanho da VM tem de suportar várias NICs. Para saber mais sobre os tamanhos de VMs que suportam várias NICs, leia os artigos [Windows Server VM sizes (Tamanhos de VMs do Windows Server)](../virtual-machines/virtual-machines-windows-sizes.md) ou [Linux VM sizes (Tamanhos de VMs do Linux)](../virtual-machines/virtual-machines-linux-sizes.md).
* A VM tem de ser criada com dois NICs, no mínimo. Se a VM for criada com apenas uma NIC, mesmo se o tamanho da VM suportar mais de uma, não pode ligar mais NICs à VM depois de esta ser criada. Desde que a VM tenha sido criada com um mínimo de duas NICs, pode ligar NICs adicionais à mesma depois de ter sido criada, caso a VM suporte mais de duas NICs.  
* Pode desligar os NICs secundários (a NIC primária não pode ser desligada) de uma VM se esta tiver, pelo menos, três NICs ligadas a si. Se a VM tiver duas ou menos NICs ligadas, não pode desligar as NICs.  
* A ordem das NICs dentro da VM será aleatória e também pode mudar entre atualizações de infraestrutura do Azure. No entanto, os endereços IP, e os endereços MAC de ethernet correspondentes, permanecem os mesmos. Por exemplo, suponha que o sistema operativo identifica Azure NIC1 como Eth1. Eth1 tem o endereço IP 10.1.0.100 e o endereço MAC 00-0D-3A-B0-39-0D. Depois de uma infraestrutura do Azure ser atualizada e reiniciada, o sistema operativo poderá identificar Azure NIC1 como Eth2, mas os endereços IP e MAC são os mesmos de quando o sistema operativo identificava Azure NIC1 como Eth1. Quando for iniciado um reinício por parte do cliente, a ordem das NICs é mantida no sistema operativo.  
* Se a VM for membro de um [conjunto de disponibilidade](../azure-glossary-cloud-terminology.md#availability-set), todas as VMs nesse conjunto têm de ter uma ou várias NICs. Se as VMs tiverem várias NICs, não é necessário que tenham o mesmo número, desde que cada uma tenha, pelo menos, duas NICs.

## <a name="next-steps"></a>Passos seguintes
* Leia o artigo [Criar uma VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para saber como criar uma VM só com uma NIC.
* Leia o artigo [Deploy a VM with multiple NIC (Implementar uma VM com várias NICs)](virtual-network-deploy-multinic-arm-ps.md) para saber como criar uma VM com várias NICs.
* Leia o artigo [Multiple IP addresses for Azure virtual machines (Vários endereços IP para máquinas virtuais do Azure)](virtual-network-multiple-ip-addresses-powershell.md) para saber como criar uma NIC com várias configurações de IP.




<!--HONumber=Jan17_HO5-->



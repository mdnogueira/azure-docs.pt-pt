---
title: "SAP HANA operações no Azure | Microsoft Docs"
description: "Operações de SAP HANA em VMs nativas do Azure"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA no guia de operações do Azure
Este guia fornece orientações para sistemas de SAP HANA que foram implementados em Azure Virtual Machines operativos. Este documento destina-se não substituir qualquer um dos documentations de SAP padrão. Guias SAP e notas podem ser encontradas nas seguintes localizações:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação do SAP](https://service.sap.com/instguides)
- [Nota SAP](https://sservice.sap.com/notes)

Pré-requisito é que tem conhecimentos básicos sobre os diferentes componentes do Azure do:

- [Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Redes do Azure e as VNets](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Documentação adicional sobre SAP NetWeaver e outros componentes SAP no Azure podem ser encontrados no [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) secção o [documentação do Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações sobre a configuração básica
### <a name="connecting-into-azure"></a>Ligar-se no Azure
Conforme documentado no [Virtual Machines do Azure de planeamento e implementação de SAP NetWeaver] [-guia de planeamento], existem dois métodos básicos para ligar em máquinas virtuais do Azure. 

- Ligar através da Internet e os pontos finais públicos numa VM ir ou a VM em execução SAP HANA
- Ligar através de um [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

Para produção cenários ou cenários em que não a produção cenários que o feed para cenários de produção em conjunto com o software SAP, tem de ter uma conectividade de site a site através de VPN ou ExpressRoute conforme mostrado neste gráfico:

![Conetividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Escolha dos tipos de VM do Azure
Tipos VM do Azure que podem ser utilizados para cenários de produção podem ser pesquisados [aqui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, pode escolher uma vasta variedade de nativa VMs do Azure. No entanto deve restringir por si para os tipos VM que estão [SAP nota #1928533](https://launchpad.support.sap.com/#/notes/1928533). Implementação dessas VMS no Azure pode ser feita através de:

- Portal do Azure
- Cmdlets do Powershell do Azure
- CLI do Azure

Também pode implementar uma plataforma de SAP HANA instalada completa para os serviços de Máquina Virtual do Azure através de [plataforma em nuvem do SAP](https://cal.sap.com/) conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choice-of-azure-storage"></a>Opção de armazenamento do Azure
O Azure oferece dois tipos de armazenamento principal adequados para VMs do Azure a executar o SAP HANA

- [Armazenamento padrão do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Armazenamento Premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

O Azure disponibiliza dois métodos de implementação para os VHDs no Azure Standard e Premium Storage. Se o cenário geral permite, recomenda-se partido [disco gerida do Azure](https://azure.microsoft.com/services/managed-disks/) implementações.

Para os tipos de armazenamento exata e SLAs em torno os tipos de armazenamento, consulte [esta documentação](https://azure.microsoft.com/pricing/details/managed-disks/)

Recomenda-se para utilizar os discos Premium do Azure, para /hana/data /hana/log volumes. É possível criar um RAID LVM através de vários discos de armazenamento Premium e utilizar os volumes RAID como /hana/data e /hana/log volumes.

Uma configuração possíveis para diferentes tipos VM comuns que os clientes até ao momento a utilizar para o alojamento de SAP HANA em VMs do Azure pode ter o seguinte aspeto:

| SKU DE VM | RAM | hana/dados e de hana/registo<br /> repartidos com LVM ou MDADM | hana/partilhado | /root volume | usr/sap | Hana/cópia de segurança |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8 TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Redes do Azure
Partindo do princípio que tiver uma conectividade de site para site VPN ou ExpressRoute no Azure, no mínimo seria tem um [Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) que está ligado através de um Gateway Virtual ao circuito VPN ou ExpressRoute. O Gateway Virtual se encontra numa sub-rede na Vnet do Azure. Para instalar o HANA, poderá criar outro duas sub-redes dentro da VNet. Uma sub-rede que os anfitriões de VM que executam as instâncias de SAP HANA e outra sub-rede que executa eventual Jumpbox ou VM de gestão que podem alojar SAP HANA Studio ou outro software de gestão.
Quando instala as VMs que devem executar HANA, devem ter as VMs:

- Duas NICs virtuais instalados do qual um estabelece ligação à sub-rede de gestão e um NIC é utilizada para ligar à instância do SAP HANA na VM do Azure a partir de no local ou outras redes.
- Endereços IP privados estáticos implementados para ambos os vNICs

Uma descrição geral das possibilidades de atribuição de endereços IP diferentes pode ser encontrada [aqui](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

O encaminhamento de tráfego para diretamente para a instância de SAP HANA ou para o jumpbox é direcionado por [os grupos de segurança de rede do Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg) que estão associados à sub-rede HANA e a sub-rede de gestão.

Em geral o esquema de implementação aproximada seria ter o seguinte aspeto:

![Esquema de implementação aproximada para SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Se implementar apenas SAP HANA no Azure sem ter de uma site a site (VPN ou ExpressRoute no Azure), acederia a instância de SAP HANA apesar de um endereço IP público que está atribuído à VM do Azure que executa a VM Jumpbox. No caso simple, é também baseiam-se nos serviços de DNS incorporados do Azure para resolver os nomes de anfitrião. Especialmente quando utilizar endereços IP destinado ao públicos, que pretende utilizar os grupos de segurança de rede do Azure para limitar as abrir portas ou intervalos de endereços IP que estão autorizados a estabelecer ligação com as sub-redes do Azure com recursos de endereços IP destinado ao públicos. O esquema do tipo de implementação pode ter o seguinte aspeto:
  
![Esquema de implementação aproximada para SAP HANA sem ligação site a site](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>Operações
### <a name="backup-and-restore-operations-on-azure-vms"></a>Cópia de segurança e as operações de restauro em VMs do Azure
As possibilidades de cópia de segurança do SAP HANA e restauro estão documentadas nestes documentos:

- [Descrição geral de cópia de segurança do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Cópia de segurança ao nível do ficheiro do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Benchmark de instantâneo de SAP HANA armazenamento](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>Início e de reinício de VMs que contém o SAP HANA
Uma da força da codificação de nuvem pública do Azure é o facto de que lhe é cobrados apenas os minutos de computação que é gastos. Isto significa que o se encerrar uma VM com o SAP HANA em execução no mesmo, apenas os custos de armazenamento são faturados durante esse período. À medida que começa a VM com o SAP HANA na mesma novamente, a VM se inicie novamente e vai ter os mesmos endereços IP (se for implementada com endereços IP estáticos). 


### <a name="saprouter-enabling-sap-remote-support"></a>SAPRouter ativar SAP remota de suporte
Se tiver uma ligação site a site entre o seu location(s) no local e o Azure e executar componentes SAP já, é muito provável que já execute SAProuter já. Neste caso, não há nada que precisa de fazer com instâncias de SAP HANA que implementar no Azure. Exceto para manter o endereço IP estático e privado da VM que aloja HANA na configuração SAPRouter e ter o NSG de alojamento de sub-rede HANA VM adaptada descritos (tráfego através da porta de TCP/IP porta 3299 permitida).

Se estiver a implementar o SAP HANA e ligar ao Azure através da Internet e não tiver um Router de SAP instalado na Vnet que executa uma VM com o SAP HANA, deve instalar o SAPRouter numa VM separada da sub-rede de gestão, conforme mostrado aqui :


![Esquema de implementação aproximada para SAP HANA sem ligação site a site e SAPRouter](media/hana-vm-operations/hana-simple-networking3.PNG)

Deve instalar SAPRouter numa VM separada e não na Jumpbox VM. A VM separada tem um endereço IP estático. Para poder estabelecer ligação SAPRouter para o SAPRouter que aloja SAP (homólogo da instância SAPRouter instalar VM), terá de contactar o SAP para obter um endereço IP do SAP que precisa de configurar a sua instância SAPRouter. A porta apenas necessária é a porta TCP 3299.
Para obter mais informações sobre como configurar e manter as ligações de remota de suporte através de SAPRouter, selecionar esta opção [origem SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Elevada disponibilidade com SAP HANA em VMs nativas do Azure
Executar o SUSE Linux 12 SP1 e mais recente pode estabelecer um cluster de Pacemaker com dispositivos STONITH para configurar uma configuração de SAP HANA que utiliza a replicação síncrona com HANA sistema replicação e ativação pós-falha automática. O procedimento do programa de configuração está descrito no artigo [disponibilidade elevada do SAP HANA em Azure Virtual Machines](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

 











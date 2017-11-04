---
title: "Rede virtual para serviços do Azure | Microsoft Docs"
description: "Saiba mais sobre as vantagens de implementar recursos numa rede virtual. Recursos na redes virtuais podem comunicar entre si e recursos no local, sem o tráfego que atravessa a Internet."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 2c3ffb8432fae41b376cc71bb600a0b1c490f345
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-integration-for-azure-services"></a>Integração de rede virtual para os serviços do Azure

Integração de serviços do Azure a uma rede virtual do Azure permite acesso privado de instâncias de um serviço implementado na rede virtual.

Pode integrar os serviços do Azure com a sua rede virtual com as seguintes opções:
- Implementar diretamente dedicadas instâncias do serviço para uma rede virtual. As instâncias dedicadas destes serviços podem ser acedidas em privado dentro da rede virtual e de redes no local.
- Ao alargar uma rede virtual para o serviço, através de pontos finais de serviço. Pontos finais de serviço permitem que os recursos do serviço individual ser protegido para a rede virtual.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Implementar serviços do Azure em redes virtuais

Pode comunicar com os recursos de mais do Azure através da Internet através de endereços IP públicos. Ao implementar os serviços do Azure num [rede virtual](virtual-networks-overview.md), pode comunicar com os recursos do serviço em privado, através de endereços IP privados.

![Serviços implementados numa rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Implementar serviços dentro de uma rede virtual fornece as seguintes capacidades:

- Recursos dentro da rede virtual podem comunicar entre si em privado, através de endereços IP privados. Exemplo transferir diretamente dados entre HDInsight e SQL Server em execução numa máquina virtual, na rede virtual.
- No local recursos podem aceder a recursos numa rede virtual utilizando endereços IP privados através de um [VPN de Site para Site (Gateway de VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Redes virtuais podem estar [em modo de peering](virtual-network-peering-overview.md) para ativar a recursos nas redes virtuais para comunicar entre si, privada endereços IP.
- Instâncias de serviço numa rede virtual são geridas completamente pelo serviço do Azure, para monitorizar o estado de funcionamento das instâncias e fornecer escala necessária, com base na carga.
- Instâncias de serviço são implementadas para uma sub-rede dedicada numa rede virtual. Entrada e o acesso de rede de saída têm de ser aberto através de [grupos de segurança de rede](security-overview.md#network-security-groups) para a sub-rede, com orientação fornecida pelos serviços.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Serviços que podem ser implementados numa rede virtual

Cada serviço implementado diretamente numa rede virtual tem requisitos específicos para o encaminhamento e os tipos de tráfego que tem de ser permitido para dentro e fora sub-redes. Para obter mais informações, consulte: 
 
- Máquinas virtuais: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Recursos de infraestrutura de serviço](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Conjuntos de dimensionamento de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Ambiente do Serviço de Aplicações](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gestão de API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de aplicação (interno)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Motor do serviço de contentor do Azure](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json): O serviço de contentor do Azure cria uma rede virtual predefinido. Pode criar uma rede virtual personalizada para utilizar com o [motor do serviço de contentor do Azure](https://github.com/Azure/acs-engine/tree/master/examples/vnet).
- [Os serviços de domínio do Active Directory do Azure](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): (Virtual network clássica) apenas
- [O Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration): (Virtual network clássica) apenas
- [Serviços em nuvem](https://msdn.microsoft.com/library/azure/jj156091): (Virtual network clássica) apenas

Pode implementar um [Balanceador de carga do Azure interno](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) carregar equilibrar muitos dos recursos na lista anterior. Em alguns casos, o serviço cria e implementa automaticamente um balanceador de carga, quando cria um recurso.

## <a name="service-endpoints-for-azure-services"></a>Pontos finais de serviço para os serviços do Azure

Alguns serviços do Azure não podem ser implementados em redes virtuais. Pode restringir o acesso a alguns dos recursos do serviço para sub-redes da rede virtual só específico, se optar por ativar um ponto final de serviço de rede virtual. Saiba mais sobre [pontos finais do serviço de rede virtual](virtual-network-service-endpoints-overview.md).

Atualmente, os pontos finais de serviço são suportados para os seguintes serviços: 
- **Armazenamento do Azure**: [contas de armazenamento do Azure de proteger a redes virtuais](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- **Base de dados SQL do Azure**: [proteger Azure base de dados SQL para redes virtuais](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integração de rede virtual em vários serviços do Azure

Pode implementar um serviço do Azure a uma sub-rede numa rede virtual e serviço críticos seguro recursos a essa sub-rede. Por exemplo, pode implementar o HDInsight para a rede virtual e proteger uma conta de armazenamento para a sub-rede do HDInsight.






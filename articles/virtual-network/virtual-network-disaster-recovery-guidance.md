---
title: "O que fazer em caso de uma interrupção do serviço do Azure com impacto na redes virtuais do Azure | Microsoft Docs"
description: "Saiba o que fazer em caso de uma interrupção do serviço do Azure com impacto na redes virtuais do Azure."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 4e125406d2e798138c45e3fbbf61a610afab69fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network--business-continuity"></a>Rede virtual – continuidade do negócio
## <a name="overview"></a>Descrição geral
Uma rede Virtual (VNet) é uma representação lógica da rede na nuvem. Permite-lhe definir o seu próprio espaço de endereços IP privados e o segmento de rede em sub-redes. As VNets serve como um limite de fidedignidade para alojar os recursos de computação, tais como máquinas virtuais do Azure e serviços em nuvem (funções da web/trabalho). Uma VNet permite a comunicação de IP privada direta entre os recursos alojados no mesmo. Também pode ser associada uma rede Virtual a uma rede no local através de uma das opções híbrida como um Gateway de VPN ou ExpressRoute.

É criada uma VNet dentro do âmbito de uma região. Pode criar as VNets com o mesmo espaço de endereços em duas regiões diferentes (ou seja, EUA leste e dos EUA oeste, mas não é possível ligá-los para outro diretamente). 

## <a name="business-continuity"></a>Continuidade do Negócio
Podem existir várias maneiras diferentes que a aplicação foi interrompida. Uma determinada região foi completamente cortar devido a um desastre natural ou um desastre parcial devido a uma falha de vários dispositivos/serviços. O impacto no serviço de VNet é diferente em cada uma destas situações.

**P: o que fazer em caso de indisponibilidade para uma região toda? ou seja, se uma região é completamente serem devido a um desastre natural? O que acontece para as redes virtuais alojados na região?**

R: a rede Virtual e os recursos na região afetado permanece inacessível durante o período de tempo de interrupção do serviço.

![Diagrama de rede Virtual simples](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: o que posso recriar a mesma rede Virtual numa região diferente?**

R: rede virtual (VNet) é bastante simples recurso. Pode invocar as APIs do Azure para criar uma VNet com o mesmo espaço de endereços numa região diferente. Para voltar a criar o ambiente mesmo que estava presente na região afetada, tem de efetuar chamadas de API para voltar a implementar os serviços de nuvem (funções da web/trabalho) e as máquinas virtuais que tiver. Também terá de rotação configurar um Gateway de VPN e estabelecer ligação à sua rede no local, se tiver conectividade no local (como uma implementação híbrida).

Pode encontrar instruções para criar uma VNet [aqui](virtual-networks-create-vnet-arm-pportal.md). 

**P: uma réplica de uma VNet uma determinada região ser recriada noutra região antecedência?**

R: Sim, pode criar duas VNets utilizando o mesmo espaço de endereços IP privados e recursos em duas regiões diferentes antecedência. Se um cliente foi que aloja a internet com serviços na VNet, estes foi definiu cópias de segurança Gestor de tráfego para georreplicação-encaminhar o tráfego para a região que está ativa. No entanto, um cliente não é possível ligar duas VNets com o mesmo espaço de endereços a respetiva rede no local, como faria com que problemas de encaminhamento. O tempo de um desastre e perda de uma VNet na região de um, um cliente pode ligar a outra VNet na região disponível com correspondência de espaço de endereço para a respetiva rede no local.

Pode encontrar instruções para criar uma VNet [aqui](virtual-networks-create-vnet-arm-pportal.md).


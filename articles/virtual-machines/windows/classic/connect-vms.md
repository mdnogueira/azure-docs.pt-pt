---
title: "Ligar as VMs do Windows num serviço em nuvem | Microsoft Docs"
description: "Ligar máquinas virtuais do Windows criadas com o modelo de implementação clássica para um serviço em nuvem do Azure ou de rede virtual."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: 0c7a21461e5bb111c4359df8e949d48382b591c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Ligar máquinas virtuais do Windows criadas com o modelo de implementação clássica com um serviço de nuvem ou de rede virtual
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Máquinas virtuais do Windows criadas com o modelo de implementação clássica são sempre colocadas num serviço em nuvem. O serviço em nuvem age como um contentor e fornece um nome exclusivo de DNS público, um endereço IP público e um conjunto de pontos finais para aceder à máquina virtual através da Internet. O serviço em nuvem pode ser numa rede virtual, mas que não é um requisito. Também pode [ligar máquinas virtuais do Linux com um serviço de nuvem ou de rede virtual](../../linux/classic/connect-vms.md).

Se não for um serviço em nuvem numa rede virtual, é chamado um *autónomo* serviço em nuvem. As máquinas virtuais no serviço autónomo em nuvem comunicar com outras máquinas virtuais utilizando nomes DNS públicos a outras máquinas virtuais e mudar o tráfego através da Internet. Se um serviço em nuvem numa rede virtual, as máquinas virtuais no serviço em nuvem pode comunicar com todas as outras máquinas virtuais na rede virtual sem enviar o tráfego através da Internet.

Se colocar as máquinas virtuais no mesmo serviço de nuvem de autónomo, ainda pode utilizar o balanceamento de carga e conjuntos de disponibilidade. Para obter mais informações, consulte [máquinas virtuais de balanceamento de carga](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gerir a disponibilidade das máquinas virtuais](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). No entanto, não é possível organizar as máquinas virtuais em sub-redes ou ligar um serviço de nuvem autónomo à sua rede no local. Eis um exemplo:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de criar uma máquina virtual, é uma boa ideia [adicionar um disco de dados](attach-disk.md) para que os seus serviços e as cargas de trabalho tem uma localização para armazenar dados.

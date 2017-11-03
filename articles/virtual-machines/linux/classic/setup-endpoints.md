---
title: "Configurar pontos finais de uma VM com Linux clássico | Microsoft Docs"
description: "Saiba como configurar pontos finais para uma VM com Linux no portal clássico do Azure para permitir a comunicação com uma máquina virtual Linux no Azure"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: 4fd8b847b0f60648d1661ce5a8667c641e616ed4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Como configurar pontos finais numa máquina virtual clássico Linux no Azure
Todas as máquinas virtuais do Linux que criar no Azure utilizando o modelo de implementação clássica automaticamente podem comunicar através de um canal de rede privada com outras máquinas virtuais no mesmo serviço em nuvem ou de rede virtual. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos finais para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais Windows](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

No **Resource Manager** modelo de implementação, pontos finais são configurados utilizando **grupos de segurança de rede (NSGs)**. Para obter mais informações, consulte [abrir portas e os pontos finais](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Quando cria uma máquina virtual Linux no portal do Azure, um ponto final de Secure Shell (SSH) é normalmente criado automaticamente. Pode configurar os pontos finais adicionais ao criar a máquina virtual ou posteriormente, conforme necessário.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Passos seguintes
* Também pode criar um ponto final da VM utilizando o [Interface de linha de comandos do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Execute o **criar o ponto final de vm do azure** comando.
* Se tiver criado uma máquina virtual no modelo de implementação Resource Manager, pode utilizar a CLI do Azure no modo Resource Manager para [criar grupos de segurança de rede](../../../virtual-network/virtual-networks-create-nsg-arm-cli.md) para controlar tráfego para a VM.

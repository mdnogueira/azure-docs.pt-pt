---
title: "Configurar pontos finais numa VM Windows clássico | Microsoft Docs"
description: "Saiba como configurar pontos finais para uma VM do Windows no portal clássico do Azure para permitir a comunicação com a máquina virtual do Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: bea2a30d67e47342616bbe00946b7c559c5e277b
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Como configurar pontos finais na máquina virtual clássico do Windows no Azure
Todas as janelas de máquinas virtuais que criar no Azure utilizando o modelo de implementação clássica automaticamente pode comunicam através de uma privada canal de rede com outras máquinas virtuais no mesmo serviço em nuvem ou de rede virtual. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos finais para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais do Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

No **Resource Manager** modelo de implementação, pontos finais são configurados utilizando **grupos de segurança de rede (NSGs)**. Para obter mais informações, consulte [permitir o acesso externo à VM através do portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando cria uma máquina virtual do Windows no portal do Azure, pontos finais comuns, como os referentes ao ambiente de trabalho remoto e a comunicação remota do Windows PowerShell são normalmente criados por si automaticamente. Pode configurar os pontos finais adicionais ao criar a máquina virtual ou posteriormente, conforme necessário.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Passos seguintes
* Para utilizar um cmdlet do Azure PowerShell para configurar um ponto final VM, consulte [adicionar AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Para utilizar um cmdlet do PowerShell do Azure para gerir uma ACL num ponto final, consulte [gerir acesso listas de controlo (ACLs) para pontos finais utilizando o PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Se tiver criado uma máquina virtual no modelo de implementação Resource Manager, pode utilizar o Azure PowerShell para [criar grupos de segurança de rede](../../../virtual-network/virtual-networks-create-nsg-arm-ps.md) para controlar tráfego para a VM.

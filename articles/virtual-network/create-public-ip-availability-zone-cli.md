---
title: "Criar um endereço IP público zoned com a CLI do Azure | Microsoft Docs"
description: "Crie um IP público numa zona de disponibilidade com a CLI do Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Criar um endereço IP público numa zona de disponibilidade com a CLI do Azure

Pode implementar um endereço IP público numa zona de disponibilidade do Azure (pré-visualização). Uma zona de disponibilidade é uma zona separada fisicamente numa região do Azure. Aprenda a:

> * Criar um endereço IP público numa zona de disponibilidade
> * Identificar recursos relacionados criados na zona de disponibilidade

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar uma versão da CLI do Azure superior à versão 2.0.17. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Zonas de disponibilidade estão em pré-visualização e estão prontas para o seu desenvolvimento e teste cenários. O suporte está disponível para famílias de tamanho VM, selecionados de recursos do Azure e regiões. Para obter mais informações sobre como começar a utilizar e os recursos do Azure, regiões e famílias de tamanho VM pode tentar zonas de disponibilidade com, consulte [zonas de descrição geral da disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="create-a-zonal-public-ip-address"></a>Criar um endereço IP público zonal

Crie um endereço IP público numa zona de disponibilidade com o seguinte comando:


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.

## <a name="get-zone-information-about-a-public-ip-address"></a>Obter informações de zona sobre um endereço IP público

Obter as informações de zona de um endereço IP público utilizando o seguinte comando:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Saiba mais sobre [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) 

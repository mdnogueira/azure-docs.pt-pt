---
title: "Criar um endereço IP público zoned com o PowerShell | Microsoft Docs"
description: "Crie um IP público numa zona de disponibilidade com o PowerShell."
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
ms.openlocfilehash: bfeba57036338b4e325d2f122443f2cde0373eed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>Criar um endereço IP público numa zona de disponibilidade com o PowerShell

Pode implementar um endereço IP público numa zona de disponibilidade do Azure (pré-visualização). Uma zona de disponibilidade é uma zona separada fisicamente numa região do Azure. Aprenda a:

> * Criar um endereço IP público numa zona de disponibilidade
> * Identificar recursos relacionados criados na zona de disponibilidade
  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este artigo requer que tem a versão 4.4.0 ou superior do módulo AzureRM instalado. Para localizar a versão, execute `Get-Module -ListAvailable AzureRM`. Se precisar de instalar ou atualizar, instale a versão mais recente do módulo de AzureRM o [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> Zonas de disponibilidade estão em pré-visualização e estão prontas para o seu desenvolvimento e teste cenários. O suporte está disponível para famílias de tamanho VM, selecionados de recursos do Azure e regiões. Para obter mais informações sobre como começar a utilizar e os recursos do Azure, regiões e famílias de tamanho VM pode tentar zonas de disponibilidade com, consulte [zonas de descrição geral da disponibilidade](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Para obter suporte, pode contactar-nos no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abrir um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado no *westeurope* região. *westeurope* é uma das regiões do Azure suporta zonas de disponibilidade em pré-visualização.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>Criar um endereço IP público zonal

Crie um endereço IP público numa zona de disponibilidade com o seguinte comando:

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> Quando atribui um endereço IP público de SKU standard a uma interface de rede de máquina virtual, tem de permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups). A comunicação com o recurso falha até criar e associar um grupo de segurança de rede e permitir explicitamente o tráfego pretendido.

## <a name="get-zone-information-about-a-public-ip-address"></a>Obter informações de zona sobre um endereço IP público

Obter as informações de zona de um endereço IP público utilizando o seguinte comando:

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [zonas de disponibilidade](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- Saiba mais sobre [endereços IP públicos](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
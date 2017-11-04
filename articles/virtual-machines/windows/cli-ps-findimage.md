---
title: Selecione as imagens de VM do Windows no Azure | Microsoft Docs
description: "Saiba como utilizar o Azure PowerSHell para determinar o publicador, oferta, SKU e versão para imagens de VM do Marketplace."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: c9b35ff5f3fbd33639805b5a4f105df32562a691
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Como localizar as imagens de VM do Windows no Azure Marketplace com o Azure PowerShell

Este tópico descreve como utilizar o Azure PowerShell para localizar as imagens VM no Azure Marketplace. Utilize estas informações para especificar uma imagem do Marketplace, quando criar uma VM do Windows.

Certifique-se de que é instalado e configurado a versão mais recente [módulo Azure PowerShell](/powershell/azure/install-azurerm-ps).



## <a name="table-of-commonly-used-windows-images"></a>Tabela de imagens do Windows utilizadas normalmente
| Nome do Editor | Oferta | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |Centro de dados de 2016 |
| MicrosoftWindowsServer |WindowsServer |2016 Centro de dados-Server Core |
| MicrosoftWindowsServer |WindowsServer |2016 Datacenter com contentores |
| MicrosoftWindowsServer |WindowsServer |Servidor de Nano de 2016 |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016 WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2 WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>Encontrar imagens específicas


Ao criar uma nova máquina virtual com o Azure Resource Manager, em alguns casos tem de especificar uma imagem com a combinação das seguintes propriedades de imagem:

* Publicador
* Oferta
* SKU

Por exemplo, utilize estes valores com o [conjunto AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) cmdlet do PowerShell, ou com um modelo de grupo de recursos no qual tem de especificar o tipo de VM a ser criado.

Se precisar de determinar estes valores, pode executar o [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), e [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) cmdlets para navegar as imagens. Determinar estes valores:

1. Listar os publicadores de imagem.
2. Para um determinado publicador, liste as respetivas ofertas.
3. Para uma determinada oferta, liste as respetivas SKUs.

Em primeiro lugar, liste os publicadores com os seguintes comandos:

```azurepowershell-interactive
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Preencha o nome do publicador escolhido e execute os seguintes comandos:

```azurepowershell-interactive
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Preencha o nome da oferta escolhida e execute os seguintes comandos:

```azurepowershell-interactive
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Da saída do `Get-AzureRMVMImageSku` comando, que tem todas as informações de que tem de especificar a imagem para uma nova máquina virtual.

O seguinte mostra um exemplo completo:

```azurepowershell-interactive
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Saída:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Para o publicador "MicrosoftWindowsServer":

```azurepowershell-interactive
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Saída:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

Para a oferta "WindowsServer":

```azurepowershell-interactive
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Saída:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

A partir da lista, copie o nome do SKU escolhido e tem todas as informações para o cmdlet do PowerShell `Set-AzureRMVMSourceImage` ou para um modelo de grupo de recursos.

## <a name="next-steps"></a>Passos seguintes
Já pode escolher com precisão a imagem que pretende utilizar. Para criar uma máquina virtual rapidamente utilizando as informações de imagem, que apenas encontrado, consulte [criar máquina virtual do Windows com o PowerShell](quick-create-powershell.md).

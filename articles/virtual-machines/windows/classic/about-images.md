---
title: "Sobre imagens de máquinas virtuais do Windows | Microsoft Docs"
description: "Saiba mais sobre como as imagens são utilizadas com máquinas virtuais do Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
ms.openlocfilehash: d421cee0becabdf81d865036d0c98b12b077152b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="about-images-for-windows-virtual-machines"></a>Sobre imagens de máquinas virtuais do Windows
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para obter informações sobre como localizar e utilizar imagens no modelo do Resource Manager, consulte [aqui](../../virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Trabalhar com imagens

Pode utilizar o módulo Azure PowerShell e o portal do Azure para gerir as imagens disponíveis para a sua subscrição do Azure. O módulo PowerShell do Azure fornece mais opções de comando, pelo que pode identificar exatamente o que pretende ver ou fazer. O portal do Azure fornece um GUI para muitas das tarefas administrativas uso corrente.

Seguem-se alguns exemplos que utilizam o módulo Azure PowerShell.

* **Obter todas as imagens**:`Get-AzureVMImage`devolve uma lista de todas as imagens disponíveis na sua subscrição atual: as imagens e aos fornecidos pelo Azure ou parceiros. A lista resultante pode ser elevada. Os exemplos que se seguem mostra como obter uma lista mais curta.
* **Obter famílias de imagem**:`Get-AzureVMImage | select ImageFamily` obtém uma lista de famílias de Imagem mostrando cadeias **ImageFamily** propriedade.
* **Obter todas as imagens de uma família específica**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Localizar as imagens de VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` este cmdlet funciona com a filtragem a propriedade de DataDiskConfiguration, que só se aplica a imagens de VM. Este exemplo também filtra a saída para apenas o nome de etiqueta e imagem.
* **Guardar uma imagem generalizada**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Guardar uma imagem especializada**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > O parâmetro OSState é necessário para criar uma imagem VM, que inclui o disco do sistema operativo e dados discos ligados. Se não utilizar o parâmetro, o cmdlet cria uma imagem do SO. O valor do parâmetro indica se a imagem é generalizada ou especializada, com base em se o disco do sistema operativo tenha sido preparado para reutilização.

* **Eliminar uma imagem**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Passos Seguintes
Também pode [criar uma máquina Windows com o portal do Azure](tutorial.md).

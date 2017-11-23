---
title: "Benefício de híbridos do Azure para o Windows Server | Microsoft Docs"
description: "Saiba como maximizar as vantagens do Windows Software Assurance para colocar licenças no local do Azure"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: c2b406530aec60299ea2db38ad9e34895fe36dcd
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Benefício Híbrido do Azure para o Windows Server
Para clientes com Software Assurance, o Azure híbrida benefício para o Windows Server permite-lhe utilizar as licenças do Windows Server no local e executar máquinas virtuais do Windows no Azure um custo reduzido. Pode utilizar o Azure híbrida benefício para o Windows Server para implementar novas máquinas virtuais de qualquer suportados pelo Azure imagem do Windows Server de plataforma ou imagens personalizadas do Windows. Este artigo vai sobre os passos sobre como implementar novas VMs com o Azure híbrida benefício para o Windows Server e como pode atualizar existentes VMs em execução. Para obter mais informações sobre o Azure híbrida benefício para o Windows Server reduções de custos e licenciamento, consulte o [página de licenciamento do Azure híbrida benefício para o Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> As imagens [HUB] Windows Server legadas que foram publicadas para clientes com contrato Enterprise no Azure Marketplace foi retirado partir 9/11/2017, utilize o Windows Server standard com a opção "Dinheiro guardar" no portal para o benefício de híbrida do Azure para o Windows Server. Para obter mais informações, consulte este [artigo.](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)
>

> [!NOTE]
> Utilizar o Azure híbrida benefício para o Windows Server com as VMs que são-lhe cobradas de software adicional, tais como o SQL Server ou qualquer uma das imagens do marketplace de terceiros está a ser implementado. Se obtiver um erro de 409, tais como: alterar a propriedade 'LicenseType' não é permitido. em seguida, está a tentar converter ou implementar uma nova VM do Windows Server que tem o software adicional de custos, que pode não ser suportado nessa região. Mesmo se que tenta parecer para a configuração do portal de opção para efetuar a conversão e não é possível vê-lo relativamente a essa VM.
>


> [!NOTE]
> Para VMs clássicas, implementar apenas uma nova VM de imagens personalizadas no local é suportada. Para tirar partido das capacidades suportadas neste artigo, tem de migrar VMs clássicas para o modelo do Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Formas de utilizar o Azure híbrida benefício para o Windows Server
Existem algumas formas de utilizar máquinas virtuais do Windows com a vantagem de híbrida do Azure:

1. É possível implementar VMs de uma das fornecido [imagens do Windows Server no Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Pode [carregar uma VM personalizada](#upload-a-windows-vhd) e [implementar com um modelo do Resource Manager](#deploy-a-vm-via-resource-manager) ou [Azure PowerShell](#detailed-powershell-deployment-walkthrough)
3. Pode alternar e converter VM existente entre em execução com o benefício de híbrida do Azure ou paga um custo a pedido para o Windows Server
4. Também pode implementar um novo dimensionamento da máquina virtual com o Azure híbrida benefício para o Windows Server

> [!NOTE]
> Converter um dimensionamento da máquina virtual existente configurado para utilizar o Azure híbrida benefício para o Windows Server não é suportada
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Implementar uma VM a partir de uma imagem de mercado de servidor do Windows
Todas as imagens do Windows Server que estão disponíveis no Azure Marketplace estão ativadas com o Azure híbrida benefício para o Windows Server. Por exemplo, Windows Server 2016, Windows Server 2012R2, Windows Server 2012 e Windows Server 2008SP1 e muito mais. Pode utilizar estas imagens para implementar VMs diretamente a partir do portal do Azure, modelos do Resource Manager, do Azure PowerShell ou outros SDKs.

Pode implementar estas imagens diretamente a partir do portal do Azure. Para utilização em modelos do Resource Manager e com o Azure PowerShell, ver a lista de imagens da seguinte forma:

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
Pode seguir os passos para [criar máquina virtual do Windows com o PowerShell](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) e passar LicenseType = "Windows_Server". Esta opção permite-lhe utilizar a licença do Windows Server existente no Azure.

### <a name="portal"></a>Portal
Pode seguir os passos para [criar máquina virtual do Windows com o portal do Azure](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) e selecione a opção para utilizar a sua licença do Windows Server existente.

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Converter VM existente com o Azure híbrida benefício para o Windows Server
Se tiver uma VM existente que pretende converter para tirar partido do Azure híbrida benefício para o Windows Server, pode atualizar o tipo de licença da VM da seguinte forma:

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Converter a utilização do Azure híbrida benefício para o Windows Server
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>Converter para prestar medida que avança
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>Portal
No painel VM do portal, pode atualizar a VM para utilizar o benefício de híbrida do Azure, selecionando a opção "Configuração de" e ative a opção "beneficiar híbridos do Azure"

> [!NOTE]
> Se não vir a opção para alternar o "benefício híbridos do Azure" em "Configuration", é porque a conversão ainda não é suportada para o tipo VM selecionado (por exemplo uma VM criada de imagem personalizada ou de uma imagem com o software pago adicionais, como o SQL Server ou Software de terceiros de Azure Marketplace).
>

## <a name="upload-a-windows-server-vhd"></a>Carregar um VHD do Windows Server
Para implementar uma VM do Windows Server no Azure, terá primeiro de criar um VHD que contém a base compilação do Windows. Este VHD tem de ser preparado adequadamente através de Sysprep antes de carregar para o Azure. Pode [Leia mais informações sobre os requisitos de VHD e processos de Sysprep](upload-generalized-managed.md) e [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Cópia de segurança da VM antes de executar o Sysprep. 

Uma vez preparou o VHD, carrega o VHD à sua conta do Storage do Azure da seguinte forma:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server e Dynamics também pode utilizar o licenciamento do Software Assurance. Deverá terá de preparar a imagem do Windows Server ao instalar os componentes da aplicação e fornecer chaves de licenciamento em conformidade, em seguida, carregar a imagem de disco para o Azure. Consulte a documentação adequada para a executar o Sysprep com a sua aplicação, tais como [considerações para instalar o SQL Server com o Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [construir uma imagem de referência 2016 (Sysprep) do SharePoint Server](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

Também pode ler mais sobre [carregar o VHD para o processo do Azure](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)

## <a name="deploy-a-vm-via-resource-manager-template"></a>Implementar uma VM através do modelo do Resource Manager
Dentro os modelos do Resource Manager, um parâmetro adicional `licenseType` tem de ser especificado. Pode ler mais sobre [criação de modelos Azure Resource Manager](../../resource-group-authoring-templates.md). Assim que tiver o VHD carregado para o Azure, edite modelo do Resource Manager para incluir o tipo de licença como parte do fornecedor de computação e implementar o modelo como normal:

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Implementar uma VM através do início rápido do PowerShell
Quando implementar a VM do Windows Server através do PowerShell, tem um parâmetro adicional `-LicenseType`. Assim que tiver o VHD carregado para o Azure, criar uma VM utilizando `New-AzureRmVM` e especifique o tipo de licenciamento da seguinte forma:

Para o Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Pode ler mais descritivo guia em vários passos para [criar uma VM do Windows utilizando o Gestor de recursos e o PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Certifique-se de que a VM está a utilizar o benefício de licenciamento
Depois de ter implementado a sua VM através do ou PowerShell, o modelo do Resource Manager ou do portal, pode verificar o tipo de licença com `Get-AzureRmVM` da seguinte forma:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

O resultado será semelhante ao seguinte exemplo para o Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Saída Isto contrasta com a seguinte VM implementada sem licenciamento do Azure híbrida benefício para o Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Lista todas as VMs do Azure híbrida benefício para Windows Server numa subscrição

Para ver e contagem de todas as máquinas virtuais implementadas com o Azure híbrida benefício para o Windows Server, pode executar o seguinte comando da sua subscrição:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Implementar um conjunto com o Azure híbrida benefício para o Windows Server de dimensionamento de máquina virtual
A máquina virtual de conjunto de dimensionamento de modelos do Resource Manager, um parâmetro adicional `licenseType` tem de ser especificado. Pode ler mais sobre [criação de modelos Azure Resource Manager](../../resource-group-authoring-templates.md). Edite modelo do Resource Manager para incluir a propriedade licenseType como parte do virtualMachineProfile o conjunto de dimensionamento e implementar o modelo como normal - consulte o seguinte exemplo, utilizando a imagem do Windows Server 2016:


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Também pode [criar e implementar um conjunto de dimensionamento de máquina virtual](#https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) e defina a propriedade LicenseType

## <a name="next-steps"></a>Passos seguintes
Leia mais sobre [como poupar dinheiro com a vantagem de híbrida do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

Saiba mais sobre [Azure híbrida benefício para o Windows Server orientações detalhadas de licenciamento](https://docs.microsoft.com/en-us/windows-server/get-started/azure-hybrid-benefit)

Saiba mais sobre [modelos utilizando o Resource Manager](../../azure-resource-manager/resource-group-overview.md)

Saiba mais sobre [Azure híbrida benefício para o Windows Server e o Azure Site Recovery disponibilizar aplicações migrar para o Azure ainda mais económico](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)

Saiba mais sobre [Windows 10 no Azure com o direito de alojamento de multi-inquilino](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)

Leia mais sobre [perguntas mais frequentes](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)

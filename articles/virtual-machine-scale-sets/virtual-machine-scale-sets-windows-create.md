---
title: "Criar um Conjunto de Dimensionamento de Máquinas Virtuais do Azure com o PowerShell | Microsoft Docs"
description: "Criar um Conjunto de Dimensionamento de Máquinas Virtuais do Azure com o PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 1f8e66fac5b82698525794f0486dd0432c7421a7
ms.openlocfilehash: 7286fed39839675eb960b749f3235f83e36c5e9a


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Criar um conjunto de dimensionamento da máquina virtual do Windows com o Azure PowerShell
Estes passos seguem uma abordagem completa para criar um conjunto de dimensionamento da máquina virtual do Azure. Consulte [Virtual Machine Scale Sets Overview (Descrição Geral dos Conjuntos de Dimensionamento da Máquina Virtual)](virtual-machine-scale-sets-overview.md), para saber mais sobre conjuntos de dimensionamento.

Deve demorar cerca de 30 minutos para realizar os passos neste artigo.

## <a name="step-1-install-azure-powershell"></a>Passo 1: instalar o Azure PowerShell
Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.

## <a name="step-2-create-resources"></a>Passo 2: Criar recursos
Crie os recursos que são necessários para o novo conjunto de dimensionamento.

### <a name="resource-group"></a>Grupo de recursos
Um conjunto de dimensionamento da máquina virtual tem de estar contido num grupo de recursos.

1. Obtenha uma lista de localizações disponíveis onde os recursos podem ser colocados:
   
        Get-AzureLocation | Sort Name | Select Name
2. Selecione uma localização que funcione melhor para si, substitua o valor do **$locName** por esse nome da localização e, em seguida, crie a variável:
   
        $locName = "location name from the list, such as Central US"
3. Substitua o valor do **$rgName** pelo nome que pretende utilizar para o novo grupo de recursos e, em seguida, crie a variável: 
   
        $rgName = "resource group name"
4. Crie o grupo de recursos:
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    Deverá ver algo semelhante a este exemplo:
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="virtual-network"></a>Rede virtual
Uma rede virtual é necessária para as máquinas virtuais no conjunto de dimensionamento.

1. Substitua o valor do **$rgName** pelo nome que pretende utilizar para a sub-rede na rede virtual e, em seguida, crie a variável: 
   
        $subnetName = "subnet name"
2. Criar as configurações de sub-rede:
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    O prefixo do endereço poderá ser diferente na sua rede virtual.
3. Substitua o valor do **$netName** pelo nome que pretende utilizar para a rede virtual e, em seguida, crie a variável: 
   
        $netName = "virtual network name"
4. Criar a rede virtual:
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configuração do conjunto de dimensionamento
Tem todos os recursos que precisa para a configuração do conjunto de dimensionamento, por isso vamos criá-lo.  

1. Substitua o valor do **$ipName** pelo nome que pretende utilizar para a configuração do IP e, em seguida, crie a variável: 
   
        $ipName = "IP configuration name"
2. Criar a configuração do IP:
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. Substitua o valor do **$vmssConfig** pelo nome que pretende utilizar para a configuração do conjunto de dimensionamento e, em seguida, crie a variável:   
   
        $vmssConfig = "Scale set configuration name"
4. Crie a configuração para o conjunto de dimensionamento:
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    Este exemplo mostra um conjunto de dimensionamento a ser criado com três máquinas virtuais. Consulte [Virtual Machine Scale Sets Overview (Descrição Geral dos Conjuntos de Dimensionamento da Máquina Virtual)](virtual-machine-scale-sets-overview.md), para mais informações sobre a capacidade dos conjuntos de dimensionamento. Este passo também inclui a definição do tamanho (denominado SkuName) das máquinas virtuais no conjunto. Para encontrar um tamanho que satisfaça as suas necessidades, consulte os [Tamanhos das máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Adicione a configuração de interface de rede para a configuração do conjunto de dimensionamento:
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    Deverá ver algo semelhante a este exemplo:
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Perfil do sistema operativo
1. Substitua o valor do **$computerName** pelo prefixo do nome de computador que pretende utilizar e, em seguida, crie a variável: 
   
        $computerName = "computer name prefix"
2. Substitua o valor do **$adminName** o nome da conta do administrador nas máquinas virtuais e, em seguida, crie a variável:
   
        $adminName = "administrator account name"
3. Substitua o valor do **$adminPassword** pela palavra-passe da conta e, em seguida, crie a variável:
   
        $adminPassword = "password for administrator accounts"
4. Crie o perfil do sistema operativo:
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Perfil de armazenamento
1. Substitua o valor do **$storageProfile** pelo nome que pretende utilizar para o perfil de armazenamento e, em seguida, crie a variável:  
   
        $storageProfile = "storage profile name"
2. Crie as variáveis que definem a imagem a utilizar:  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    Para encontrar as informações sobre outras imagens a utilizar, consulte [Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI (Navegue e selecione imagens da máquina virtual do Azure com o Windows PowerShell e o CLI do Azure)](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

3. Crie o perfil de armazenamento:
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Conjuntos de dimensionamento de máquinas virtuais
Por fim, pode criar o conjunto de dimensionamento.

1. Substitua o valor do **$vmssName** pelo nome do conjunto de dimensionamento da máquina virtual e, em seguida, crie a variável:
   
        $vmssName = "scale set name"
2. Crie o conjunto de dimensionamento:
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    Verá algo semelhante a este exemplo que mostra uma implementação efetuada com êxito:
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Passo 3: Explorar recursos
Utilize estes recursos para explorar o conjunto de dimensionamento da máquina virtual que criou:

* Portal do Azure - está disponível uma quantidade limitada de informação através do portal.
* [Explorador de Recursos do Azure](https://resources.azure.com/) – Esta ferramenta é a melhor forma de explorar o estado atual do seu conjunto de dimensionamento.
* Azure PowerShell - Utilize este comando para obter informações:
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>Passos seguintes
* Gira o conjunto de dimensionamento que acabou de criar com as informações em [Manage virtual machines in a Virtual Machine Scale Set (Gerir máquinas virtuais num Conjunto de Dimensionamento da Máquina Virtual)](virtual-machine-scale-sets-windows-manage.md)
* Considere configurar o dimensionamento automático do seu conjunto de dimensionamento através de informações em [Automatic scaling and virtual machine scale sets (Dimensionamento automático e conjuntos de dimensionamento da máquina virtual)](virtual-machine-scale-sets-autoscale-overview.md)
* Saiba mais sobre o dimensionamento vertical ao consultar [Vertical autoscale with Virtual Machine Scale sets (Dimensionamento automático vertical com conjuntos de Dimensionamento da Máquina Virtual)](virtual-machine-scale-sets-vertical-scale-reprovision.md)




<!--HONumber=Feb17_HO4-->



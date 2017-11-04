---
title: "Filtrar o tráfego de rede com o Azure grupos de segurança de rede e de aplicação (pré-visualização) | Microsoft Docs"
description: "Saiba como criar grupos de segurança de rede e de aplicação (pré-visualização) para restringir o tipo de tráfego de rede que entra e sai de máquinas virtuais."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 19f94f009aac53baca31dcb6973a8aff3f4f5ab9
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Filtrar o tráfego de rede com grupos de segurança de rede e de aplicação (pré-visualização)

Neste tutorial, saiba como criar grupos de segurança de rede para filtrar o tráfego de rede e de grupos de máquinas virtuais com grupos de segurança de aplicações. Para obter mais informações sobre grupos de segurança de rede e de grupos de segurança de aplicações, consulte o artigo [descrição geral de segurança](security-overview.md). 

As secções seguintes incluem passos que pode tomar para criar grupos de segurança utilizando a interface de linha de comandos do Azure de rede ([CLI do Azure](#azure-cli)) e [Azure PowerShell](#powershell). O resultado é o mesmo, independentemente da ferramenta que utilizar para criar grupos de segurança de rede. Clica numa ligação de ferramenta para aceder à secção do tutorial. 

Este artigo fornece os passos para criar grupos de segurança através do modelo de implementação Resource Manager, que é o modelo de implementação, que recomendamos a utilização quando criar grupos de segurança de rede de rede. Se precisar de criar um grupo de segurança de rede (clássica), consulte [criar um grupo de segurança de rede (clássica)](virtual-networks-create-nsg-classic-ps.md). Se não estiver familiarizado com os modelos de implementação do Azure, consulte [modelos de implementação do Azure compreender](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> Este tutorial utiliza funcionalidades de grupo de segurança de rede que estão atualmente na versão de pré-visualização. Funcionalidades de pré-lançamento não têm o mesmo disponibilidade e fiabilidade como funcionalidades de versão em geral. Enquanto na pré-visualização, as funcionalidades estão disponíveis apenas nas seguintes regiões: WestCentralUS. Se pretender implementar a versão a utilizar funcionalidades apenas em geral de grupos de segurança de rede, consulte [criar um grupo de segurança de rede](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>CLI do Azure

Comandos da CLI do Azure são os mesmos, se executar os comandos do Windows, Linux ou macOS. No entanto, existem diferenças scripts shells de sistema operativo. Executar scripts nos passos seguintes na shell de deteção. 

1. [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Certifique-se de que está a utilizar a versão 2.0.18 ou superior da CLI do Azure, introduzindo o `az --version` comando. Se não tiver, instale a versão mais recente.
3. Inicie sessão no Azure com o `az login` comando.
4. Registar-se para a pré-visualização, introduzindo os seguintes comandos:
    
    ```azurecli-interactive
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. Confirme que são registadas para a pré-visualização, introduzindo o seguinte comando:

    ```azurecli-interactive
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    Não continue com os restantes passos até *registada* aparece para **estado** no resultado devolvido pelo comando anterior. Se continuar antes de estiver registado, os restantes passos falharem.
6. Execute o seguinte script de deteção para criar um grupo de recursos:

    ```azurecli-interactive
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Crie três grupos de segurança de aplicação, um para cada tipo de servidor:

    ```azurecli-interactive
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Crie um grupo de segurança de rede:

    ```azurecli-interactive
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Crie regras de segurança no NSG, definir os grupos de segurança de aplicações como o destino:
    
    ```azurecli-interactive    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Criar uma rede virtual: 
    
    ```azurecli-interactive
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Associe o grupo de segurança de rede para a sub-rede na rede virtual:

    ```azurecli-interactive
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Crie três interfaces de rede, um para cada tipo de servidor: 

    ```azurecli-interactive
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    Apenas a segurança regra correspondente que criou no passo 9 é aplicada a interface de rede, baseada no grupo de segurança de aplicação, que a interface de rede é um membro do. Por exemplo, apenas o *WebRule* Efetivo para o *myWebNic*, porque a interface de rede é um membro do *servidores Web* grupo de segurança de aplicação e a regra Especifica o *servidores Web* grupo de segurança de aplicações como respectivo destino. O *AppRule* e *DatabaseRule* regras não são aplicadas para o *myWebNic*, porque a interface de rede não é um membro do *AppServers*e *DatabaseServers* grupos de segurança de aplicações.

13. Crie uma máquina virtual para cada tipo de servidor, anexar a interface de rede correspondente a cada máquina virtual. Este exemplo cria máquinas virtuais do Windows, mas pode alterar *win2016datacenter* para *UbuntuLTS* para criar máquinas virtuais do Linux em vez disso.

    ```azurecli-interactive
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Opcional**: a eliminar os recursos que criou neste tutorial, efetuando os passos em [eliminar recursos](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Instalar e configurar [PowerShell](/powershell/azure/install-azurerm-ps).
2. Certifique-se de que tem a versão 4.4.0 ou superior do módulo AzureRm instalado. Pode verificar a versão atualmente instalada introduzindo o `Get-Module -ListAvailable AzureRM` comando. Se precisar de instalar ou atualizar, instale a versão mais recente do módulo de AzureRM o [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureRM).
3. Numa sessão do PowerShell, inicie sessão no Azure com o seu [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) utilizando o `login-azurermaccount` comando.
4. Registar-se para a pré-visualização, introduzindo os seguintes comandos:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Confirme que são registadas para a pré-visualização, introduzindo o seguinte comando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    Não continue com os restantes passos até *registada* aparece no **RegistrationState** coluna do resultado devolvido pelo comando anterior. Se continuar antes de estiver registado, os restantes passos falharem.
        
6. Criar um grupo de recursos:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Crie três grupos de segurança de aplicação, um para cada tipo de servidor:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Crie regras de segurança para cada tipo de servidor.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80  

    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443 

    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336    
    ``` 

9. Crie um grupo de segurança de rede:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Criar uma configuração de sub-rede e associar o grupo de segurança de rede ao mesmo:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Criar uma rede virtual: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Crie três interfaces de rede, um para cada tipo de servidor. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $databaseAsg
    ```

    Apenas a segurança regra correspondente que criou no passo 8 é aplicada a interface de rede, baseada no grupo de segurança de aplicação, que a interface de rede é um membro do. Por exemplo, apenas o *WebRule* Efetivo para o *myWebNic*, porque a interface de rede é um membro do *servidores Web* grupo de segurança de aplicação e a regra Especifica o *servidores Web* grupo de segurança de aplicações como respectivo destino. O *AppRule* e *DatabaseRule* regras não são aplicadas para o *myWebNic*, porque a interface de rede não é um membro do *AppServers*e *DatabaseServers* grupos de segurança de aplicações.

13. Crie uma máquina virtual para cada tipo de servidor, anexar a interface de rede correspondente a cada máquina virtual. Este exemplo cria máquinas virtuais do Windows, mas antes de executar o script, pode alterar *-Windows* para *- Linux*, *MicrosoftWindowsServer* para *Canónico*, *WindowsServer* para *UbuntuServer* e *2016 Datacenter* para *14.04.2-LTS*para criar máquinas virtuais do Linux em vez disso.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Opcional**: a eliminar os recursos que criou neste tutorial, efetuando os passos em [eliminar recursos](#delete-cli).

## <a name="delete"></a>Eliminar recursos

Quando concluir este tutorial, pode querer eliminar os recursos que criou, para que não implicar custos de utilização. Também eliminar um grupo de recursos elimina todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa de portal, introduza **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. No **myResourceGroup** painel, clique em de **eliminar** ícone.
3. Para confirmar a eliminação no **tipo o nome de grupo de recursos** box, introduza **myResourceGroup**e, em seguida, clique em **eliminar**.

### <a name="delete-cli"></a>CLI do Azure

Numa sessão CLI, introduza o seguinte comando:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Numa sessão do PowerShell, introduza o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```


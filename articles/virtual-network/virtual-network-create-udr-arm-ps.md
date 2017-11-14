---
title: "Criar um tráfego de rede de rota a rota definida pelo utilizador através de uma aplicação virtual de rede - PowerShell | Microsoft Docs"
description: "Saiba como criar uma rota definida pelo utilizador para substituir predefinição do Azure encaminhamento por encaminhamento de tráfego de rede através de uma aplicação virtual de rede."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 70ddec1c7ba76ef7f42048896079e5c5fa2bf60c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---powershell"></a>Criar uma rota definida pelo utilizador - do PowerShell

Neste tutorial, saiba como criar rotas definidas pelo utilizador para encaminhar o tráfego entre duas [rede virtual](virtual-networks-overview.md) sub-redes através de uma aplicação virtual de rede. Uma aplicação virtual de rede é uma máquina virtual que executa uma aplicação de rede, tais como uma firewall. Para obter mais informações sobre aplicações virtuais de rede previamente configuradas que pode implementar numa rede virtual do Azure, consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Quando criar sub-redes na rede virtual, o Azure cria predefinido [rotas de sistema](virtual-networks-udr-overview.md#system-routes) que permitem que os recursos em todas as sub-redes para comunicar entre si, conforme mostrado na imagem seguinte:

![Rotas predefinidas](./media/create-user-defined-route/default-routes.png)

Neste tutorial, crie uma rede virtual com o público, privado e sub-redes da rede de Perímetro, conforme mostrado na imagem que se segue. Normalmente, os servidores web poderão ser implementados para uma sub-rede pública e uma aplicação ou um servidor de base de dados poderá ser implementada para uma sub-rede privada. Criar uma máquina virtual para agir como uma aplicação virtual de rede na sub-rede DMZ e, opcionalmente, criar uma máquina virtual em cada sub-rede que comunicam através da aplicação virtual de rede. Todo o tráfego entre as sub-redes públicas e privadas é encaminhado através de aplicação, conforme mostrado na imagem seguinte:

![Rotas definidas pelo utilizador](./media/create-user-defined-route/user-defined-routes.png)

Este artigo fornece os passos para criar uma rota definida pelo utilizador através do modelo de implementação Resource Manager, que é o modelo de implementação, que recomendamos a utilização quando criar rotas definidas pelo utilizador. Se precisar de criar uma rota definida pelo utilizador (clássica), consulte [criar uma rota definida pelo utilizador (clássica)](virtual-network-create-udr-classic-ps.md). Se não estiver familiarizado com os modelos de implementação do Azure, consulte [modelos de implementação do Azure compreender](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para saber mais sobre as rotas definidas pelo utilizador, consulte o artigo [descrição geral de rotas definidas pelo utilizador](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Criar rotas e dispositivo de rede virtual

Pode instalar e configurar a versão mais recente do PowerShell [AzureRM](https://www.powershellgallery.com/packages/AzureRM/) módulo no seu PC ou basta clicar o **experimente** botão em nenhum dos scripts a executar os scripts na Shell de nuvem do Azure. A Shell de nuvem tem o módulo do PowerShell AzureRM instalado.
 
1. **Pré-requisitos**: criar uma rede virtual com duas sub-redes, efetuando os passos em [criar uma rede virtual](#create-a-virtual-network).
2. Se executar o PowerShell a partir do seu computador, inicie sessão no Azure com o seu [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) utilizando o `login-azurermaccount` comando. Se utilizar a Shell de nuvem, pode ainda automaticamente sessão iniciada. A Shell de nuvem poderá ter de reiniciar para alterar da shell de deteção utilizado ao criar a rede virtual pré-requisitos.
3. Defina variáveis alguns utilizadas em todos os passos:

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. Crie uma sub-rede de rede de Perímetro na rede virtual exiting:

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. Crie um endereço IP público estático para a máquina de virtual da aplicação virtual de rede.

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. Crie a máquina virtual NVA. A NVA pode ser uma máquina virtual com o sistema operativo Linux ou do Windows. Para criar a máquina virtual, copie o script para o sistema operativo e cole-o sua sessão do PowerShell. Se criar uma VM do Windows, cole o script para um editor de texto, altere o "valor" para a variável de $cred, em seguida, cole o texto de modificação para a sua sessão do PowerShell:

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. Por predefinição, o tráfego de rotas do Azure entre todas as sub-redes dentro de uma rede virtual. Criar uma rota para alterar a predefinição do Azure encaminhamento para que o tráfego do *pública* sub-rede é encaminhada para a NVA, em vez de diretamente para o *privada* sub-rede.

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. Criar uma tabela de rota para o *pública* sub-rede.

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. Associe a tabela de rotas para a sub-rede pública. Associar uma tabela de rota para uma sub-rede faz com que o Azure para encaminhar todo o tráfego de saída da sub-rede, de acordo com as rotas na tabela de rota. Uma tabela de rota pode ser associada a sub-redes zero ou vários, enquanto uma sub-rede pode ter zero ou uma tabela de rotas associada ao mesmo.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. Criar uma rota para o tráfego do *privada* sub-rede para o *pública* sub-rede através da máquina virtual NVA.

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. Criar a tabela de rota para o *privada* sub-rede.

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. Associar a tabela de rotas para o *privada* sub-rede.

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **Opcional:** criar uma máquina virtual nas sub-redes pública e privada e confirme que a comunicação entre as máquinas virtuais é encaminhada através de aplicação virtual de rede, efetuando os passos em [validarEncaminhamento](#validate-routing).
15. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-resources).

## <a name="validate-routing"></a>Validar o encaminhamento

1. Se ainda não o fez, concluir os passos no [criar rotas e dispositivo de rede virtual](#create-routes-and-network-virtual-appliance).
2. Clique em de **experimente** botão na caixa de que se segue, que abre o Shell de nuvem do Azure. Se lhe for solicitado, inicie sessão no Azure com o [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). A Shell de nuvem do Azure é uma shell de deteção livre com a interface de linha de comandos do Azure pré-instalado. 

    Os scripts seguintes criar duas máquinas virtuais, um no *pública* sub-rede e um no *privada* sub-rede. Os scripts também ativar o IP de reencaminhamento para a interface de rede no sistema operativo da NVA para ativar o sistema operativo encaminhar o tráfego através da interface de rede. Uma produção NVA normalmente inspeciona o tráfego antes de encaminhamento, mas neste tutorial, a NVA simple encaminha apenas o tráfego sem inspecioná-lo. 

    Clique em de **cópia** clique no botão no **Linux** ou **Windows** scripts que sigam e colagem o conteúdo do script para um editor de texto. Alterar a palavra-passe para o *adminPassword* variável, em seguida, cole o script para a Shell de nuvem do Azure. Execute o script para o sistema operativo que selecionou quando criou a aplicação virtual de rede no passo 7 [criar rotas e dispositivo de rede virtual](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Valide a comunicação entre as máquinas virtuais nas sub-redes pública e privada. 

    - Abra um [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) ou [ambiente de trabalho remoto](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) ligação (Windows) para o endereço IP público do *myVm público* máquina virtual.
    - Numa linha de comandos no *myVm público* máquina virtual, introduza `ping myVm-Private`. Receber respostas porque a NVA encaminha o tráfego do público para a sub-rede privada.
    - Do *myVm público* máquina virtual, execute uma rota de rastreio entre as máquinas virtuais nas sub-redes públicas e privadas. Introduza o comando adequado que se segue, consoante o sistema operativo instalado nas máquinas virtuais nas sub-redes pública e privada:
        - **Windows**: a partir de uma linha de comandos, execute o `tracert myvm-private` comando.
        - **Ubuntu**: executar o `tracepath myvm-private` comando.
      Tráfego atravessa 10.0.2.4 (a NVA) antes de atingir 10.0.1.4 (a máquina virtual na sub-rede privada). 
    - Concluir os passos anteriores, ligando-se para o *myVm privada* máquina virtual e efetuando o ping a *myVm público* máquina virtual. A rota de rastreio mostra comunicação estiverem em deslocação através de 10.0.2.4 antes de atingir 10.0.0.4 (a máquina virtual na sub-rede pública).
    
      > [!NOTE]
      > Os passos anteriores permitem-lhe confirmar o encaminhamento entre os endereços IP privados do Azure. Se pretender direta ou proxy, o tráfego para o IP público endereços através de uma aplicação virtual de rede:
      > - Tem de fornecer o dispositivo de tradução de endereços de rede ou a capacidade de proxy. Se tradução de endereços de rede, o dispositivo tem traduzir a origem para a sua própria de endereço IP e, em seguida, reencaminhe esse pedido para o endereço IP público. Se o dispositivo tem endereços de rede traduzido o endereço de origem ou é a funcionalidade de proxy, Azure traduz os endereços IP privados a rede aplicação virtual para um endereço IP público. Para obter mais informações sobre os diferentes métodos Azure utiliza a tradução de endereços IP privados para endereços IP públicos, consulte [Noções sobre ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Uma rota adicional na tabela de rota, tais como prefixo: 0.0.0.0/0, o tipo de próximo salto VirtualAppliance e o IP do próximo salto endereços 10.0.2.4 (no script de exemplo anterior).
      >
    - **Opcionalmente,**: utilizar a capacidade de salto seguinte do observador de rede do Azure para validar o salto seguinte entre as duas máquinas virtuais no Azure. Antes de utilizar o observador de rede, deve primeiro [criar uma instância de observador de rede de Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para a região que pretende utilizá-la no. Neste tutorial, é utilizada a região EUA Leste. Assim que tiver ativada uma instância de observador de rede para a região, introduza o seguinte comando para ver as informações de salto seguintes entre as máquinas virtuais nas sub-redes pública e privada:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       Devolve o resultado *10.0.2.4* como o **o nextHopIpAddress** e *VirtualAppliance* como o **nextHopType**.

> [!NOTE]
> Para ilustrar os conceitos neste tutorial, endereços IP públicos são atribuídos a máquinas virtuais a pública e privada sub-redes e todos os acessos de porta de rede está ativado no Azure para máquinas virtuais. Ao criar máquinas virtuais para utilização em produção, não poderá atribuir endereços IP públicos aos mesmos e pode filtrar o tráfego de rede para a sub-rede privada ao implementar uma aplicação virtual de rede existentes à frente dele ou através da atribuição de um grupo de segurança de rede para as sub-redes, interface de rede, ou ambos. Para obter mais informações sobre grupos de segurança de rede, consulte [grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Este tutorial requer uma rede virtual existente com duas sub-redes. Clique em de **experimente** botão na caixa que se segue, para criar rapidamente uma rede virtual. Ao clicar no **experimente** botão abre a [Shell de nuvem do Azure](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Embora a Shell de nuvem com o PowerShell ou numa shell de deteção, nesta secção, a shell de deteção é utilizada para criar a rede virtual. A shell de deteção tem interface de linha de comandos do Azure instalada. Se lhe for pedido, a Shell de nuvem, inicie sessão no Azure com o [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Para criar a rede virtual que utilizou neste tutorial, clique o **cópia** botão na caixa seguinte, em seguida, cole o script de Shell de nuvem do Azure:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Para obter mais informações sobre como utilizar o portal, PowerShell ou um modelo Azure Resource Manager para criar uma rede virtual, consulte o artigo [criar uma rede virtual](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Eliminar recursos

Quando concluir este tutorial, pode querer eliminar os recursos que criou, para que não implicar custos de utilização. Também eliminar um grupo de recursos elimina todos os recursos que estão no grupo de recursos. No PowerShell, introduza o seguinte comando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

- Criar um [aplicação virtual de rede altamente disponível](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Os dispositivos de rede virtuais têm, muitas vezes, várias interfaces de rede e endereços IP atribuídos aos mesmos. Saiba como [adicionar interfaces de rede para uma máquina virtual existente](virtual-network-network-interface-vm.md#vm-add-nic) e [adicionar endereços IP para uma interface de rede existente](virtual-network-network-interface-addresses.md#add-ip-addresses). Apesar de todos os tamanhos de máquina virtual podem ter, pelo menos, duas interfaces de rede ligados aos mesmos, cada tamanho da máquina virtual suporta um número máximo de interfaces de rede. Para saber como várias interfaces de rede virtuais de cada computador tamanho suporta, consulte [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tamanhos de máquina virtual. 
- Criar uma rota definida pelo utilizador para forçar o túnel tráfego no local através de um [ligação de VPN de site para site](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

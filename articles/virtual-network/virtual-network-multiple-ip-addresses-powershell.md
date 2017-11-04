---
title: "Vários endereços IP para máquinas virtuais do Azure - PowerShell | Microsoft Docs"
description: "Saiba como atribuir vários endereços IP a uma máquina virtual utilizando o PowerShell | Gestor de recursos."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: jdial;annahar
ms.openlocfilehash: b3690ec991add437afdaba3ef22022d49c962b34
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Atribuir vários endereços IP para máquinas virtuais utilizando o PowerShell

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação Azure Resource Manager com o PowerShell. Vários endereços IP não não possível atribuir recursos criados através do modelo de implementação clássica. Para saber mais sobre modelos de implementação do Azure, leia o [compreender os modelos de implementação](../resource-manager-deployment-model.md) artigo.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Os passos que se seguem explicam como criar um VM de exemplo com vários endereços IP, conforme descrito no cenário. Alterar valores das variáveis conforme necessário para a implementação.

1. Abra uma linha de comandos do PowerShell e conclua os restantes passos nesta secção dentro de uma única sessão do PowerShell. Se ainda não tiver PowerShell instalada e configurada, concluir os passos a [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) artigo.
2. Início de sessão à sua conta com o `login-azurermaccount` comando.
3. Substitua *myResourceGroup* e *westus* com um nome e localização da sua escolha. Crie um grupo de recursos. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

    ```powershell
    $RgName   = "MyResourceGroup"
    $Location = "westus"

    New-AzureRmResourceGroup `
    -Name $RgName `
    -Location $Location
    ```

4. Crie uma rede virtual (VNet) e a sub-rede na mesma localização que o grupo de recursos:

    ```powershell
    
    # Create a subnet configuration
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name MySubnet `
    -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $VNet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyVNet `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $subnetConfig

    # Get the subnet object
    $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
    ```

5. Crie um grupo de segurança de rede (NSG) e uma regra. O NSG protege a VM com regras de entrada e saídas. Neste caso, é criada uma regra de entrada para a porta 3389, que permite ligações de ambiente de trabalho remotas recebidas.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzureRmNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definir a configuração de IP primária para a NIC. Alterar 10.0.0.4 para um endereço válido da sub-rede que criou, se utilizou o valor definido anteriormente. Antes de atribuir um endereço IP estático, recomenda-se que tem de confirmar primeiro não estiver já em utilização. Introduza o comando `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Se o endereço está disponível, o resultado devolve *verdadeiro*. Se não estiver disponível, o resultado devolve *falso* e uma lista de endereços que estão disponíveis. 

    Nos seguintes comandos, **substitua < substituir com-your-exclusivo-nome-> com o nome DNS exclusivo a utilizar.** O nome tem de ser exclusivo em todos os endereços IP públicos dentro de uma região do Azure. Este é um parâmetro opcional. Pode ser removido se pretender ligar à VM utilizando o endereço IP público.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Quando atribui várias configurações de IP a um NIC, uma configuração tem de ser atribuída como a *-primário*.

    > [!NOTE]
    > Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereço IP, leia o [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Não há um limite ao número de endereços IP públicos que podem ser utilizadas numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

7. Definir as configurações de IP secundárias para a NIC. Pode adicionar ou remover as configurações conforme necessário. Cada configuração de IP tem de ter um endereço IP privado atribuído. Cada configuração, opcionalmente, pode ter um endereço IP público atribuído.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. A NIC de criar e associar as três configurações de IP ao mesmo:

    ```powershell
    
    $NIC = New-AzureRmNetworkInterface `
    -Name MyNIC `
    -ResourceGroupName $RgName `
    -Location $Location `
    -NetworkSecurityGroupId $NSG.Id `
    -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```

    >[!NOTE]
    >Apesar de todas as configurações estão atribuídas a um NIC neste artigo, pode atribuir várias configurações de IP a cada NIC anexado a VM. Para saber como criar uma VM com vários NICs, leia o [criar uma VM com vários NICs](../virtual-machines/windows/multiple-nics.md) artigo.

9. Crie a VM, introduzindo os seguintes comandos:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a sername and password for the VM you're reating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzureRmVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzureRmVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Adicionar os endereços IP privados para o sistema operativo VM, efetuando os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo. Adicione os endereços IP públicos para o sistema operativo.

## <a name="add"></a>Adicionar endereços IP para uma VM

Pode adicionar endereços IP públicos e privados a um NIC, efetuando os passos que se seguem. Os exemplos das secções seguintes partem do princípio de que já tem uma VM com as três configurações de IP descritas no [cenário](#Scenario) deste artigo, mas não é necessário que efetuar.

1. Abra uma linha de comandos do PowerShell e conclua os restantes passos nesta secção dentro de uma única sessão do PowerShell. Se ainda não tiver PowerShell instalada e configurada, concluir os passos a [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) artigo.
2. Altere os "valores" a $Variables seguintes para o nome do NIC que pretende adicionar endereço IP e o grupo de recursos e a localização que a NIC existe no:

    ```powershell
    $NicName  = "MyNIC"
    $RgName   = "MyResourceGroup"
    $Location = "westus"
    ```

    Se não souber o nome de NIC que pretende alterar, introduza os seguintes comandos, em seguida, altere os valores das variáveis anteriores:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. Criar uma variável e defina-o para o NIC existente, escrevendo o seguinte comando:

    ```powershell
    $MyNIC = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName
    ```
4. Nos seguintes comandos, altere *MyVNet* e *MySubnet* para os nomes de VNet e sub-rede a NIC está ligada. Introduza os comandos para obter os objetos de VNet e sub-rede que a NIC está ligada a:

    ```powershell
    $MyVNet = Get-AzureRMVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
    $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
    ```
    Se não souber o nome VNet ou sub-rede que a NIC está ligada a, introduza o seguinte comando:
    ```powershell
    $MyNIC.IpConfigurations
    ```
    Na saída, procure texto semelhante ao seguinte exemplo de saída:
    
    ```
    "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
    ```
    Neste resultado, *MyVnet* é a VNet e *MySubnet* é a NIC está ligada à sub-rede.

5. Conclua os passos de uma das seguintes secções, com base nos seus requisitos:

    **Adicionar um endereço IP privado**

    Para adicionar um endereço IP privado a um NIC, tem de criar uma configuração de IP. O comando seguinte cria uma configuração com um endereço IP estático de 10.0.0.7. Quando especificar um endereço IP estático, tem de ser um endereço não utilizado para a sub-rede. Recomenda-se que teste primeiro o endereço está disponível ao introduzir o `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` comando. Se o endereço IP estiver disponível, o resultado devolve *verdadeiro*. Se não estiver disponível, o resultado devolve *falso*e uma lista de endereços que estão disponíveis.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    Crie configurações de tantos conforme for necessário, utilizando nomes de configuração exclusivo e endereços IP privados (para as configurações com endereços IP estáticos).

    Adicione o endereço IP privado para o sistema operativo VM, efetuando os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo.

    **Adicionar um endereço IP público**

    Um endereço IP público é adicionado ao associar um recurso de endereço IP público para uma nova configuração de IP ou uma configuração de IP existente. Conclua os passos de uma das seguintes secções que se seguem, forem necessárias.

    > [!NOTE]
    > Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereço IP, leia o [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Não há um limite ao número de endereços IP públicos que podem ser utilizadas numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
    >

    - **Associe o recurso de endereço IP público para uma nova configuração de IP**
    
        Sempre que adicionar um endereço IP público uma nova configuração de IP, também tem de adicionar um endereço IP privado, porque todas as configurações de IP tem de ter um endereço IP privado. Pode adicionar um recurso de endereço IP público existente ou crie um novo. Para criar uma nova, introduza o seguinte comando:
    
        ```powershell
        $myPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "myPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location `
        -AllocationMethod Static
        ```

        Para criar uma nova configuração de IP com um endereço IP privado estático e associada *myPublicIp3* IP público endereços de recursos, introduza o seguinte comando:

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig `
        -Name IPConfig-4 `
        -NetworkInterface $myNIC `
        -Subnet $Subnet `
        -PrivateIpAddress 10.0.0.7 `
        -PublicIpAddress $myPublicIp3
        ```

    - **Associe o recurso de endereço IP público para uma configuração de IP existente**

        Só pode ser associado a uma configuração de IP que já não tem um associado um recurso de endereço IP público. Pode determinar se uma configuração de IP tem um endereço IP público associado ao introduzir o seguinte comando:

        ```powershell
        $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
        ```

        Poderá ver um resultado semelhante ao seguinte:

        ```     
        Name       PrivateIpAddress PublicIpAddress                                           Primary
        
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False
        ```

        Uma vez que o **PublicIpAddress** coluna *IpConfig 3* está em branco, nenhum recurso de endereço IP público do está atualmente associado a ele. Pode adicionar um recurso de endereço IP público existente para IpConfig 3, ou introduza o seguinte comando para criar um:

        ```powershell
        $MyPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "MyPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location -AllocationMethod Static
        ```

        Introduza o seguinte comando para associar o recurso de endereço IP público à configuração de IP existente com o nome *IpConfig 3*:
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig `
        -Name IpConfig-3 `
        -NetworkInterface $mynic `
        -Subnet $Subnet `
        -PublicIpAddress $myPublicIp3
        ```

6. Defina a NIC com a nova configuração de IP, introduzindo o seguinte comando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $MyNIC
    ```

7. Ver os endereços IP privados e os recursos de endereço IP públicos atribuídos para o NIC introduzindo o seguinte comando:

    ```powershell   
    $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. Adicione o endereço IP privado para o sistema operativo VM, efetuando os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo. Adicione o endereço IP público para o sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

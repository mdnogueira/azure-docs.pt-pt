---
title: "Estático clássico IP - VM do Azure - privado interno"
description: "Noções sobre IPs interno estático (DIPs) e como geri-los"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
ms.openlocfilehash: cf9ee59ca4e44ed01836c2efb1f4df5f073bf6e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Como definir um endereço IP de privada interno estático através do PowerShell (clássica)
Na maioria dos casos, não terá de especificar um endereço IP estático interno para a máquina virtual. As VMs numa rede virtual serão recebem automaticamente um endereço IP de um intervalo que especificou. Mas em certos casos, a especificação de um endereço IP estático para uma VM específica faz sentido. Por exemplo, se a VM estiver prestes a executar DNS ou serão um controlador de domínio. Um endereço IP estático interno permanece com a VM, mesmo através de um Estado de paragem/desaprovisionamento. 

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que a maioria das implementações novas utilizem o [modelo de implementação do Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Como verificar se está disponível um endereço IP específico
Para verificar se o endereço IP *10.0.0.7* está disponível numa vnet com o nome *TestVnet*, execute o seguinte comando do PowerShell e certifique-se o valor para *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Se pretender testar o comando acima num ambiente seguro, siga as diretrizes no [criar uma rede virtual (clássica)](virtual-networks-create-vnet-classic-pportal.md) para criar uma vnet com o nome *TestVnet* e certifique-se de que utiliza o *10.0.0.0/8* espaço de endereços.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Como especificar um IP estático interno ao criar uma VM
O script do PowerShell abaixo cria um novo serviço em nuvem com o nome *TestService*, em seguida, obtém uma imagem do Azure, em seguida, cria uma VM chamada *TestVM* no novo serviço de nuvem utilizando a imagem obtida, define a VM ser numa sub-rede com o nome *sub-rede 1*e define *10.0.0.7* como um IP estático interno para a VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Como obter informações de IP internas estáticos para uma VM
Para ver as informações de IP estáticas internas para a VM criada com o script acima, execute o seguinte comando do PowerShell e observar os valores para *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Como remover um IP internos estático a partir de uma VM
Para remover o IP estático interno adicionado para a VM com o script acima, execute o seguinte comando do PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Como adicionar um IP estático interno para uma VM existente
Para adicionar um estático interno IP para a VM criada ao utilizar o script acima, runt tados os seguintes comandos:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Passos seguintes
[IP reservado](virtual-networks-reserved-public-ip.md)

[IP público de nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md)

[APIs REST do IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx)


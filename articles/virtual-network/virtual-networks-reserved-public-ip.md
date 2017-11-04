---
title: "Gerir o Azure endereços IP reservados (clássica) - PowerShell | Microsoft Docs"
description: "Compreender os endereços IP reservados (clássica) e como geri-los através do PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 5e9c83cebec96c6bc8afd53b0c637d7af899746f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reserved-ip-addresses-classic"></a>Endereços IP reservados (clássico)

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI do Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modelo](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

Endereços IP no Azure enquadram-se em duas categorias: dinâmicos e reservado. Endereços IP públicos geridos pelo Azure são dinâmicos por predefinição. Isto significa que o endereço IP utilizado para um serviço de nuvem especificada (VIP) ou aceder a uma VM ou instância de função diretamente (ILPIP) pode ser alteradas ocasionalmente, quando recursos são encerrar ou parados (desalocados).

Para impedir a alteração de endereços IP, pode reservar um endereço IP. IPs reservados pode ser utilizado apenas como um VIP, garantindo que o endereço IP para o serviço em nuvem permaneça a mesma, mesmo que recursos são encerrar ou parados (desalocados). Além disso, pode converter os IPs dinâmicos existente utilizado como um VIP para um endereço IP reservado.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como reservar um estático público endereço IP com o [modelo de implementação do Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Para saber mais sobre os endereços IP no Azure, leia o [endereços IP](virtual-network-ip-addresses-overview-classic.md) artigo.

## <a name="when-do-i-need-a-reserved-ip"></a>Quando precisa de um IP reservado?
* **Pretender certificar-se de que o IP está reservado na sua subscrição**. Se pretende reservar um endereço IP que não é libertado da sua subscrição, sob qualquer circunstância, deve utilizar um IP público reservado.  
* **Pretende que o IP para se manter com o serviço de nuvem mesmo across parado ou desalocada Estado (VMs)**. Se pretender que o seu serviço para ser acedido através da utilização de um endereço IP que não altera, mesmo quando as VMs do serviço em nuvem estão a ser encerradas ou pare a (desalocadas).
* **Pretender certificar-se de que o tráfego de saída a partir do Azure utiliza um endereço IP previsível**. Pode ter a firewall no local, configurada para permitir apenas o tráfego de endereços IP específicos. Ao reservar a um IP, conhecer o endereço IP de origem e não precisa de atualizar as regras de firewall devido a uma alteração IP.

## <a name="faq"></a>FAQ
1. Pode utilizar um IP reservado para todos os serviços do Azure? <br>
    Não. IPs reservados só pode ser utilizado para VMs e funções de instância de serviço na nuvem expostas através de um VIP.
2. O número de IPs reservados posso ter? <br>
    Para obter mais informações, consulte o [Azure limita](../azure-subscription-service-limits.md#networking-limits) artigo.
3. É cobrada uma taxa de IPs reservados? <br>
    Por vezes. Para detalhes de preços, consulte o [detalhes de preços de endereço de IP reservado](http://go.microsoft.com/fwlink/?LinkID=398482) página.
4. Como posso reservar um endereço IP? <br>
    Pode utilizar o PowerShell, o [API de REST de gestão do Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx), ou o [portal do Azure](https://portal.azure.com) reservar um endereço IP numa região do Azure. Um endereço IP reservado está associado à sua subscrição.
5. Pode utilizar um IP reservado com baseado no grupo de afinidade VNets? <br>
    Não. IPs reservados só são suportados em VNets regionais. IPs reservados não são suportadas para VNets que estão associadas a grupos de afinidades. Para obter mais informações sobre a associação de uma VNet com uma região ou grupo de afinidade, consulte o [sobre VNets regionais e de grupos de afinidades](virtual-networks-migrate-to-regional-vnet.md) artigo.

## <a name="manage-reserved-vips"></a>Gerir VIPs reservados

Certifique-se de que instalou e configurou o PowerShell, efetuando os passos a [instalar e configurar o PowerShell](/powershell/azure/overview) artigo. 

Antes de poder utilizar IPs reservados, deve adicioná-lo à sua subscrição. Para criar um IP reservado do conjunto de endereços IP públicos disponíveis no *EUA Central* localização, execute o seguinte comando:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Tenha em atenção, no entanto, que não é possível especificar o IP está a ser reservada. Para ver os endereços IP estão reservados na sua subscrição, execute o seguinte comando do PowerShell e, tenha em atenção os valores para *ReservedIPName* e *endereço*:

```powershell
Get-AzureReservedIP
```

Resultado esperado:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Quando criar um endereço IP reservado com o PowerShell, não é possível especificar um grupo de recursos para criar o IP reservado no. Locais do Azure, para um grupo de recursos denominado *predefinido redes* automaticamente. Se criar o IP reservado com o [portal do Azure](http://portal.azure.com), pode especificar qualquer grupo de recursos que escolher. Se criar o IP reservado num grupo de recursos diferente de *predefinido redes* no entanto, sempre referencia o IP reservado com comandos, tal como `Get-AzureReservedIP` e `Remove-AzureReservedIP`, tem de referenciar o nome *reservado nome de grupo de recursos-ip-nome do grupo*.  Por exemplo, se criar um IP reservado com o nome *myReservedIP* num grupo de recursos denominado *myResourceGroup*, tem de referenciar o nome do IP reservado como *grupo myResourceGroup myReservedIP*.   

Depois de um IP é reservado, continua a ser associado à sua subscrição até que eliminá-lo. Para eliminar um IP reservado, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>O endereço IP de um serviço em nuvem existente de reserva
Pode reservar o endereço IP de um serviço em nuvem existente, adicionando o `-ServiceName` parâmetro. Reservar o endereço IP de um serviço em nuvem *TestService* no *EUA Central* localização, execute o seguinte comando do PowerShell:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associar um IP reservado para um novo serviço em nuvem
O script seguinte cria um IP reservado nova, em seguida, associa-a para um novo serviço em nuvem com o nome *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Quando cria um IP reservado para utilizar com um serviço em nuvem, ainda referir-se a VM utilizando *VIP:&lt;número da porta >* para comunicação de entrada. Reservar um IP não significa que pode ligar diretamente à VM. O IP reservado está atribuído ao serviço em nuvem que a VM foi implementada. Se pretender ligar diretamente a uma VM por IP, terá de configurar um IP público de nível de instância. Um IP público de nível de instância é um tipo de IP público (denominado uma ILPIP) que está atribuído diretamente à VM. Não é reservado. Para obter mais informações, leia o [IP público de nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md) artigo.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Remover um IP reservado de uma implementação em execução
Para remover um IP reservado adicionado a um novo serviço em nuvem, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Remover um IP reservado a partir de uma implementação em execução não remove a reserva da sua subscrição. É simplesmente liberta o IP para ser utilizado por outro recurso na sua subscrição.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associar um IP reservado para uma implementação em execução
Os comandos seguintes criam um serviço em nuvem com o nome *TestService2* com uma nova VM com o nome *TestVM2*. Existente IP com o nome reservado *MyReservedIP* está associado a, em seguida, o serviço em nuvem.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associar um IP reservado para um serviço em nuvem, utilizando um ficheiro de configuração de serviço
Também pode associar um IP reservado para um serviço em nuvem, utilizando um ficheiro de configuração (. CSCFG) do serviço. O xml de exemplo seguintes mostra como configurar um serviço em nuvem para utilizar um VIP reservado com o nome *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Passos seguintes
* Compreender como [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implementação clássica.
* Saiba mais sobre [reservado de endereços IP privados](virtual-networks-reserved-private-ip.md).
* Saiba mais sobre [endereços IP de público ao nível do instância (ILPIP)](virtual-networks-instance-level-public-ip.md).


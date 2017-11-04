---
title: "Endereços de IP público (clássica) de nível de instância do Azure | Microsoft Docs"
description: "Compreender a instância de endereços IP públicos nível (ILPIP) e como geri-los através do PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 773043f2841ec7539b0d49357dec6bcb9f4f78a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="instance-level-public-ip-classic-overview"></a>Instância pública IP (clássica) Descrição geral detalhada
Uma instância IP público ao nível (ILPIP) é um endereço IP público que pode atribuir diretamente a uma instância de função VM ou Cloud Services, em vez de para o serviço em nuvem que a VM ou instância de função residem no. Um ILPIP não tem o local de IP virtual (VIP) que é atribuído ao seu serviço em nuvem. Em vez disso, é um endereço IP adicional que pode utilizar para ligar diretamente à sua VM ou instância de função.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda a criação de VMs através do Resource Manager. Certifique-se de que compreende como [endereços IP](virtual-network-ip-addresses-overview-classic.md) trabalho no Azure.

![Diferença entre ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Como é mostrado na figura 1, o serviço em nuvem é acedido através de um VIP, enquanto as VMs individuais normalmente são acedidas através de VIP:&lt;número de porta&gt;. Ao atribuir um ILPIP para uma VM específica, que a VM pode ser acedida diretamente com esse endereço IP.

Quando cria um serviço em nuvem no Azure, correspondentes DNS registos são criados automaticamente para permitir o acesso ao serviço através de um nome de domínio completamente qualificado (FQDN), em vez de utilizar o VIP real. O mesmo processo ocorrerá para um ILPIP, permitir o acesso de VM ou instância de função através do FQDN, em vez do ILPIP. Por exemplo, se criar um serviço em nuvem com o nome *contosoadservice*, e configurar uma função da web com o nome *contosoweb* com duas instâncias, Azure regista os seguintes registos para as instâncias:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Pode atribuir ILPIP apenas um para cada VM ou instância de função. Pode utilizar até 5 ILPIPs por subscrição. ILPIPs não são suportadas para as VMs de vários NICs.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Por que motivo seria a pedir uma ILPIP?
Se pretender ser capaz de ligar à sua instância de função VM ou um endereço IP atribuído diretamente ao mesmo, em vez de utilizar a nuvem de serviço VIP:&lt;número de porta&gt;, pedir um ILPIP para a VM ou instância de função.

* **Active Directory FTP** -atribuindo um ILPIP a uma VM, pode receber tráfego em qualquer porta. Pontos finais não são necessários para a VM receber tráfego.  Consulte [geral do protocolo FTP] (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) para obter detalhes sobre o protocolo FTP.
* **Saída IP** - provenientes de VM de tráfego de saída está mapeado para o ILPIP como origem e o ILPIP identifica exclusivamente a VM para as entidades externas.

> [!NOTE]
> No passado, um endereço ILPIP foi referido como um endereço IP (PIP) público.
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Gerir um ILPIP para uma VM
As seguintes tarefas permitem-lhe criar, atribuir e remover ILPIPs VMs:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Como pedir um ILPIP durante a criação da VM com o PowerShell
O script do PowerShell seguinte cria um serviço em nuvem com o nome *FTPService*, obtém uma imagem do Azure, cria uma VM chamada *FTPInstance* utilizando a imagem obtida, define a VM para utilizar um ILPIP e adiciona a VM para o novo serviço:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Como obter as informações de ILPIP para uma VM
Para ver as informações de ILPIP para a VM criada com o script anterior, execute o seguinte comando do PowerShell e observar os valores para *PublicIPAddress* e *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Resultado esperado:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Como remover um ILPIP de uma VM
Para remover o ILPIP adicionada para a VM com o script anterior, execute o seguinte comando do PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Como adicionar um ILPIP para uma VM existente
Para adicionar um ILPIP para a VM criada ao utilizar o script anterior, execute o seguinte comando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Gerir um ILPIP para uma instância de função de serviços Cloud

Para adicionar um ILPIP a uma instância de função de serviços em nuvem, conclua os seguintes passos:

1. Transfira o ficheiro. cscfg para o serviço em nuvem, efetuando os passos a [como configurar os serviços em nuvem](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artigo.
2. Atualize o ficheiro. cscfg ao adicionar o `InstanceAddress` elemento. O exemplo seguinte adiciona um ILPIP denominado *MyPublicIP* para uma instância de função com o nome *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Carregar o ficheiro. cscfg para o serviço em nuvem, efetuando os passos a [como configurar os serviços em nuvem](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artigo.

## <a name="next-steps"></a>Passos seguintes
* Compreender como [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implementação clássica.
* Saiba mais sobre [reservado IPs](virtual-networks-reserved-public-ip.md).

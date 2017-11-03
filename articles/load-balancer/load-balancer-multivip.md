---
title: "Mutiple VIPs num serviço em nuvem"
description: "Descrição geral dos multiVIP e como definir vários VIPs num serviço em nuvem"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 85f6d26a-3df5-4b8e-96a1-92b2793b5284
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b6b7b0b2d7a7f33facaf72bbd2d7937364770673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurar vários VIPs num serviço em nuvem

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Pode aceder a serviços em nuvem do Azure através da Internet pública, utilizando um endereço IP fornecido pelo Azure. Este endereço IP público é referido como um VIP (virtual IP), uma vez que está associada ao balanceador de carga do Azure e não a Máquina Virtual (VM) instâncias do serviço em nuvem. Pode aceder a qualquer instância VM dentro de um serviço em nuvem através da utilização de um único VIP.

No entanto, existem cenários em que poderá ser necessário mais do que um VIP como uma entrada de apontar para o mesmo serviço de nuvem. Por exemplo, o serviço em nuvem pode alojar vários sites que requeiram conectividade SSL com a porta predefinida 443, como cada site é alojada para um cliente diferente ou inquilino. Neste cenário, tem de ter um público destinado ao endereço IP diferente para cada site. O diagrama abaixo mostra uma típica web do multi-inquilino de alojamento com uma necessidade para vários certificados SSL na mesma porta pública.

![Cenário de várias VIP SSL](./media/load-balancer-multivip/Figure1.png)

No exemplo acima, todos os VIPs utilizam a mesma porta pública (443) e o tráfego é redirecionado para um ou VMs de uma porta privada exclusiva para o endereço IP interno do serviço em nuvem que aloja todos os Web sites com balanceamento de carga mais.

> [!NOTE]
> Outra situação exigir a utilização de vários VIPs está a alojar vários escuta de grupo de disponibilidade SQL AlwaysOn no mesmo conjunto de máquinas virtuais.

VIPs são dinâmicas por predefinição, o que significa que o endereço IP real atribuído para o serviço em nuvem pode alterar ao longo do tempo. Para impedir que a acontecer, pode reservar um VIP para o seu serviço. Para saber mais sobre VIPs reservadas, consulte o artigo [reservado de IP público](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Consulte [preços do endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/) para obter informações sobre preços em VIPs e IPs reservados.

Pode utilizar o PowerShell para verificar os VIPs utilizados pelos seus serviços em nuvem, bem como adicionar e remover VIPs, associar um VIP para um ponto final e configurar um VIP específico de balanceamento de carga.

## <a name="limitations"></a>Limitações

Neste momento, a funcionalidade de várias VIP é limitada para os seguintes cenários:

* **Apenas IaaS**. Só pode ativar várias VIP para serviços em nuvem que contêm as VMs. Não é possível utilizar várias VIP em cenários de PaaS com instâncias de função.
* **PowerShell apenas**. Só pode gerir várias VIP utilizando o PowerShell.

Estas limitações são temporárias e podem ser alterados em qualquer altura. Certifique-se de que revê esta página para verificar as alterações futuras.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Como adicionar um VIP para um serviço em nuvem
Para adicionar um VIP ao seu serviço, execute o seguinte comando do PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Este comando apresenta um resultado semelhante ao seguinte exemplo:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Como remover um VIP de um serviço em nuvem
Para remover o VIP adicionado ao seu serviço no exemplo acima, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Apenas é possível remover um VIP, se este não tem pontos finais associados ao mesmo.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Como obter informações de VIP a partir de um serviço em nuvem
Para obter os VIPs associados um serviço em nuvem, execute o seguinte script do PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

O script apresenta um resultado semelhante ao seguinte exemplo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Neste exemplo, o serviço em nuvem tem 3 VIPs:

* **O Vip1** é VIP predefinido, sabe que porque o valor para IsDnsProgrammedName está definido como true.
* **Vip2** e **Vip3** não são utilizados como não têm quaisquer endereços IP. Só irá ser utilizadas se associar um ponto final para o VIP.

> [!NOTE]
> A subscrição só será cobrada por VIPs adicionais assim que estiverem associados um ponto final. Para obter mais informações sobre preços, consulte [preços do endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Como associar um VIP para um ponto final

Para associar um VIP num serviço em nuvem para um ponto final, execute o seguinte comando do PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

O comando cria um ponto final associado para o VIP chamado *Vip2* na porta *80*e associa-VM com o nome *myVM1* num serviço em nuvem com o nome *myService* utilizando *TCP* na porta *8080*.

Para verificar a configuração, execute o seguinte comando do PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

O resultado semelhante ao seguinte exemplo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Como ativar um VIP específico de balanceamento de carga

Pode associar um VIP único com várias máquinas virtuais para fins de balanceamento de carga. Por exemplo, tiver um serviço em nuvem com o nome *myService*e duas máquinas virtuais com o nome *myVM1* e *myVM2*. E o serviço de nuvem tem vários VIPs, um com o nome *Vip2*. Se pretender certificar-se de que todo o tráfego para a porta *81* no *Vip2* é equilibrados entre *myVM1* e *myVM2* na porta *8181* , execute o seguinte script do PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Também pode atualizar o Balanceador de carga a utilizar um VIP diferentes. Por exemplo, se executar o comando do PowerShell abaixo, irá alterar o conjunto para utilizar um VIP Vip1 com o nome de balanceamento de carga:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Passos Seguintes

[Análise de registos para balanceamento de carga do Azure](load-balancer-monitor-log.md)

[Internet destinado ao balanceador descrição geral de carga](load-balancer-internet-overview.md)

[Começar a utilizar na Internet com o Balanceador de carga](load-balancer-get-started-internet-arm-ps.md)

[Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md)

[APIs REST do IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx)

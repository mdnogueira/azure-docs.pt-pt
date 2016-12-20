---
title: Criar um balanceador de carga interno com o PowerShell no Resource Manager | Microsoft Docs
description: Saiba como criar um balanceador de carga interno com o PowerShell no Resource Manager
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 07ea7f3529d5d2e6fde07805663da7c0f3e65d86

---

# <a name="create-an-internal-load-balancer-using-powershell"></a>Criar um balanceador de carga interno com o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com os recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo explica como utilizar o modelo de implementação Resource Manager, o que é recomendado pela Microsoft para a maioria das novas implementações em vez do [modelo de implementação clássica](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

Os passos seguintes explicam como criar um balanceador de carga interno com o Azure Resource Manager com PowerShell. Com o Azure Resource Manager, os itens para criar um Balanceador de carga interno são configurados individualmente e, em seguida, combinados para criar um balanceador de carga.

Tem de criar e configurar os seguintes objetos para implementar um balanceador de carga:

* Configuração de IP de front-end - irá configurar o endereço IP privado para o tráfego de rede recebido
* Conjunto de endereços de back-end - irá configurar as interfaces de rede que irão receber o tráfego de balanceamento de carga proveniente do conjunto IP de front-end
* Regras de balanceamento de carga - configuração da porta local e de origem para o balanceador de carga.
* Sondas - configura a sonda de estado de funcionamento das instâncias da Máquina Virtual.
* Regras NAT de entrada - configura as regras da porta para aceder diretamente a uma das instâncias da Máquina Virtual.

Pode obter mais informações sobre os componentes do balanceador de carga com o Azure resource manager em [Suporte do Azure Resource Manager para o balanceador de carga](load-balancer-arm.md).

Os seguintes passos explicam como configurar um balanceador de carga entre duas máquinas virtuais.

## <a name="setup-powershell-to-use-resource-manager"></a>Configurar o PowerShell para utilizar o Resource Manager

Certifique-se de que tem a versão de produção mais recente do módulo do Azure para o PowerShell e configurou o PowerShell corretamente para aceder à sua subscrição do Azure.

### <a name="step-1"></a>Passo 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Passo 2

Verifique as subscrições da conta

```powershell
Get-AzureRmSubscription
```

Ser-lhe-á solicitado para Autenticar com as suas credenciais.

### <a name="step-3"></a>Passo 3

Escolha qual das suas subscrições do Azure utilizar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="create-resource-group-for-load-balancer"></a>Criar Grupo de Recursos do balanceador de carga

Crie um grupo de recursos (ignore este passo se estiver a utilizar um grupo de recursos existente)

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. Verifique se todos os comandos para criar um balanceador de carga irão utilizar o mesmo grupo de recursos.

No exemplo acima, criámos um grupo de recursos denominado “NRP-RG” e a localização “E.U.A. Oeste”.

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Criar Rede Virtual e um endereço IP privado para conjunto IP de front-end

Cria uma sub-rede para a rede virtual e atribui a variável $backendSubnet

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Criar uma rede virtual:

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Cria a rede virtual e adiciona a sub-rede lb-subnet-be à rede virtual NRPVNet e atribui à variável $vnet

## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Criar Conjunto IP de front-end e conjunto de endereços de back-end

Configurar um conjunto IP de front-end para o tráfego de rede do balanceador de carga recebido e conjunto de endereços de back-end para receber o tráfego com carga balanceada.

### <a name="step-1"></a>Passo 1

Crie um conjunto IP de front-end com o endereço IP privado 10.0.2.5 para a sub-rede 10.0.2.0/24, que será o ponto final de tráfego de rede recebido.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2"></a>Passo 2

Configure um conjunto de endereços de back-end utilizado para receber o tráfego de entrada do conjunto IP de front-end:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Criar regras LB, regras NAT, sonda e balanceador de carga

Depois de criar o conjunto IP de front-end e o conjunto de endereços back-end, terá de criar as regras que irão pertencer ao recurso do balanceador de carga:

### <a name="step-1"></a>Passo 1

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

O exemplo acima está a criar os seguintes itens:

* Regra NAT em que todo o tráfego de entrada para porta 3441 irá para a porta 3389.
* uma regra NAT secundária em que todo o tráfego de entrada para porta 3442 irá para a porta 3389.
* uma regra de balanceador de carga que irá equilibrar todo o tráfego de entrada na porta pública 80 para a porta local 80, no conjunto de endereços de back-end.
* uma regra de sonda que irá verificar o estado de funcionamento do caminho "HealthProbe.aspx"

### <a name="step-2"></a>Passo 2

Crie o balanceador de carga ao adicionar todos os objetos (regras NAT, regras do Balanceador de carga, configurações da sonda) em conjunto:

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-network-interfaces"></a>Criar interfaces de rede

Depois de criar o balanceador de carga interno, tem de definir as interfaces de rede que irão receber o tráfego de rede de entrada com balanceamento de carga, as regras NAT e a sonda. Neste caso, a interface de rede é configurada individualmente e pode ser atribuída a uma máquina virtual mais tarde.

### <a name="step-1"></a>Passo 1

Obtenha a rede virtual do recurso e a sub-rede para criar interfaces de rede:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Este passo cria uma interface de rede que irá pertencer ao conjunto de back-end do balanceador de carga e associar a primeira regra NAT para RDP a esta interface de rede:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2"></a>Passo 2

Crie uma segunda interface de rede denominada LB-Nic2-BE:

Este passo cria uma segunda interface de rede, atribuindo ao mesmo conjunto de back-end do balanceador de carga e associando a segunda regra NAT criada para RDP:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

O resultado final irá mostrar o seguinte:

    $backendnic1

Resultado esperado:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Passo 3

Utilize o comando Add-AzureRmVMNetworkInterface para atribuir o NIC a uma máquina virtual.

Pode encontrar as instruções passo a passo para criar uma máquina virtual e atribuir a um NIC depois da documentação: [Criar uma VM do Azure com o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Adicionar a interface de rede

Se já tiver uma máquina virtual criada, pode adicionar a interface de rede com os seguintes passos:

### <a name="step-1"></a>Passo 1

Carregue o recurso do balanceador de carga para uma variável (se ainda não o tiver feito). A variável utilizada é designada por $lb e utiliza os mesmos nomes do recurso do balanceador de carga criado acima.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2"></a>Passo 2

Carregue a configuração de back-end para uma variável.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb
```

### <a name="step-3"></a>Passo 3

Carregue a interface de rede já criada para uma variável. o nome da variável é $nic. O nome de interface de rede é o mesmo do exemplo abaixo.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4"></a>Passo 4

Altere a configuração de back-end na interface de rede.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5"></a>Passo 5

Guarde o objeto da interface de rede.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Depois adicionar uma interface de rede ao conjunto de back-end do balanceador de carga, este começa a receber tráfego de rede com base nas regras de balanceamento de carga para esse recurso do balanceador de carga.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

### <a name="step-1"></a>Passo 1
Ao utilizar o balanceador de carga do exemplo acima, atribua o objeto do balanceador de carga à variável $slb com Get-AzureRmLoadBalancer

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2"></a>Passo 2

No exemplo seguinte, irá adicionar uma nova regra NAT de Entrada com a porta 81 no front-end e a porta 8181 para o conjunto de back-end, para um balanceador de carga existente

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3"></a>Passo 3

Guardar a nova configuração com o Set-AzureLoadBalancer

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Utilize o comando Remove-AzureRmLoadBalancer para eliminar um balanceador de carga criado anteriormente denominado "NRP-LB", num grupo de recursos denominado "NRP-RG"

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Pode utilizar o parâmetro opcional -Force para evitar o pedido de eliminação.

## <a name="next-steps"></a>Passos seguintes

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO5-->



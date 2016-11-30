---
title: "Criar um balanceador de carga com acesso à Internet no Resource Manager com o PowerShell | Microsoft Docs"
description: "Saiba como criar um balanceador de carga com acesso à Internet no Resource Manager com o PowerShell"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 1a1c3c15c51b1e441f21158510e92cc8de057352
ms.openlocfilehash: d489573e9a3efceb5bf8a4d6a7e0284c61bc24e9

---

# <a name="a-nameget-startedacreating-an-internet-facing-load-balancer-in-resource-manager-by-using-powershell"></a><a name="get-started"></a>Criar um balanceador de carga com acesso à Internet no Resource Manager com o PowerShell

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação do Resource Manager. Também pode [saber como criar um balanceador de carga com acesso à Internet com o modelo de implementação clássica](load-balancer-get-started-internet-classic-cli.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Implementar a solução com o Azure PowerShell

Os procedimentos seguintes explicam como criar um balanceador de carga com acesso à Internet, através do Azure Resource Manager com o PowerShell. Com o Azure Resource Manager, cada recurso é criado e configurado individualmente e, em seguida, colocado em conjunto para criar um balanceador de carga.

Tem de criar e configurar os seguintes objetos para implementar um balanceador de carga:

* Configuração de IP front-end: contém os endereços IP públicos (PIP) para o tráfego de rede recebido.
* Conjunto de endereços de back-end: contém interfaces de rede (NICs) para as máquinas virtuais receberem tráfego de rede do balanceador de carga.
* Regras de balanceamento de carga: contém as regras que mapeiam uma porta pública no balanceador de carga para uma porta no conjunto de endereços de back-end.
* Regras NAT de entrada: contém as regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no conjunto de endereços de back-end.
* Sondas: contém sondas utilizadas para verificar a disponibilidade de instâncias da máquina virtual no conjunto de endereços de back-end.

Para obter mais informações, veja [Suporte do Azure Resource Manager para o Load Balancer](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para utilizar o Resource Manager

Certifique-se de que tem a versão mais recente de produção do módulo Azure Resource Manager para o PowerShell:

1. Inicie sessão no Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Introduza as credenciais quando lhe for pedido.

2. Verifique as subscrições da conta.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Escolha qual das suas subscrições do Azure utilizar.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Crie um grupo de recursos. (Ignore este passo se estiver a utilizar um grupo de recursos existente.)

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP público para o conjunto IP de front-end

1. Crie uma sub-rede e uma rede virtual.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Crie um recurso do endereço IP público do Azure, com o nome **PublicIP**, para ser utilizado por um conjunto IP de front-end com o nome DNS **loadbalancernrp.westus.cloudapp.azure.com**. O comando seguinte utiliza o tipo de alocação estático.

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > O balanceador de carga utiliza a etiqueta de domínio do IP público como um prefixo para o respetivo FQDN. Isto é diferente do modelo de implementação clássico, que utiliza o serviço cloud como o FQDN do balanceador de carga.
   > Neste exemplo, o FQDN é **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Crie um conjunto IP de front-end e um conjunto de endereços de back-end

1. Criar um conjunto IP de front-end com o nome **LB-Frontend** que utiliza o recurso **PublicIp**.

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. Crie um conjunto de endereços de back-end com o nome **LB-backend**.

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Crie regras NAT, uma regra de balanceador de carga, uma sonda e um balanceador de carga

Este exemplo cria os seguintes itens:

* Uma regra NAT para traduzir todo o tráfego de entrada na porta 3441 à porta 3389
* Uma regra NAT para traduzir todo o tráfego de entrada na porta 3442 à porta 3389
* Uma regra de sonda para verificar o estado de funcionamento numa página com o nome **HealthProbe.aspx**
* Uma regra de balanceador de carga para balancear todo o tráfego de entrada na porta 80 à porta 80 nos endereços do conjunto de back-end
* Um balanceador de carga que utiliza todos estes objetos

Siga estes passos:

1. Crie as regras NAT.

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. Crie uma sonda de estado de funcionamento. Existem duas formas de configurar uma pesquisa:

    Sonda HTTP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Sonda TCP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. Crie uma regra de balanceador de carga.

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. Crie o balanceador de carga, com os objetos criados anteriormente.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>Criar NICs

Crie interfaces de rede (ou modificar as existentes) e, em seguida, associá-las a regras NAT, regras de balanceador de carga e sondas:

1. Obtenha a rede virtual e uma sub-rede de rede virtual, onde os NICs têm de ser criados.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Crie um NIC com o nome **lb-nic1-be** e associe-o à primeira regra NAT e ao primeiro (e único) conjunto de endereços de back-end.

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. Crie um NIC com o nome **lb-nic2-be** e associe-o à segunda regra NAT e ao primeiro (e único) conjunto de endereços de back-end.

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. Verifique os NICs.

        $backendnic1

    Resultado esperado:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
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
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Utilize o cmdlet `Add-AzureRmVMNetworkInterface` para atribuir os NICs a VMs diferentes.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para obter documentação de orientação sobre como criar uma máquina virtual e atribuir um NIC, consulte [Criar uma VM do Azure com o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Adicionar a interface de rede ao balanceador de carga

1. Obtenha o balanceador de carga a partir do Azure.

    Carregue o recurso do balanceador de carga para uma variável (se ainda não o tiver feito). A variável é designada por **$lb**. Utilize os mesmos nomes do recurso do balanceador de carga que criou anteriormente.

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. Carregue a configuração de back-end para uma variável.

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb
    ```

3. Carregue a interface de rede já criada para uma variável. O nome da variável é **$nic**. O nome de interface de rede é o mesmo do exemplo anterior.

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. Altere a configuração de back-end na interface de rede.

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. Guarde o objeto da interface de rede.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    Depois adicionar uma interface de rede ao conjunto de back-end do balanceador de carga, este começa a receber tráfego de rede com base nas regras de balanceamento de carga para esse recurso do balanceador de carga.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

1. Ao utilizar o balanceador de carga do exemplo anterior, atribua um objeto do balanceador de carga à variável **$slb** com `Get-AzureLoadBalancer`.

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
    ```

2. No exemplo seguinte, adicione uma regra NAT de entrada - com a porta 81 no conjunto de front-end e a porta 8181 para o conjunto de back-end - para um balanceador de carga existente.

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. Guarde a nova configuração com o `Set-AzureLoadBalancer`.

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Utilize o comando `Remove-AzureLoadBalancer` para eliminar um balanceador de carga criado anteriormente denominado **NRP-LB**, num grupo de recursos denominado **NRP-RG**.

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Pode utilizar o parâmetro opcional **-Force** para evitar o pedido de eliminação.

## <a name="next-steps"></a>Passos seguintes

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO3-->



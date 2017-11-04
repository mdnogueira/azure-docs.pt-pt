---
title: Comandos do PowerShell comuns para redes virtuais do Azure | Microsoft Docs
description: "Comandos do PowerShell comuns para ajudá-lo à criação de uma rede virtual e os respetivos recursos associados para as VMs."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 26378ff9f16af9724db08cc4013654557f1c838b
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Comandos do PowerShell comuns para redes virtuais do Azure

Se pretender criar uma máquina virtual, terá de criar um [rede virtual](../../virtual-network/virtual-networks-overview.md) ou saber sobre uma rede virtual existente no qual a VM pode ser adicionada. Normalmente, quando criar uma VM, terá também de considerar a criação dos recursos descritos neste artigo.

Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.

Algumas variáveis poderão ser útil se executar mais do que um dos comandos neste artigo:

- $location - a localização dos recursos de rede. Pode utilizar [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) para localizar um [região geográfica](https://azure.microsoft.com/regions/) que funciona para si.
- $myResourceGroup - o nome do grupo de recursos onde sejam encontram os recursos de rede.

## <a name="create-network-resources"></a>Criar recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Criar configurações de sub-rede |$subnet1 = [novo AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = novo AzureRmVirtualNetworkSubnetConfig-Name "mySubnet2" - AddressPrefix XX. X.X.X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para uma [internet com o Balanceador de carga](../../load-balancer/load-balancer-internet-overview.md) e uma sub-rede separada para uma [Balanceador de carga interno](../../load-balancer/load-balancer-internal-overview.md). |
| Criar uma rede virtual |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) -Name "myVNet" - ResourceGroupName $myResourceGroup-localização $location - AddressPrefix XX. X.X.X/XX-sub-rede $subnet1, $subnet2 |
| Teste para um nome de domínio único |[Teste AzureRmDnsAvailability](https://docs.microsoft.com/powershell/module/azurerm.network/test-azurermdnsavailability) - DomainNameLabel "myDNS"-localização $location<BR><BR>Pode especificar um nome de domínio DNS para um [recurso de IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), que cria um mapeamento para domainname.location.cloudapp.azure.com para o endereço IP público nos servidores DNS geridos pelo Azure. O nome pode conter apenas letras, números e hífenes. O primeiro e último carateres têm de ser uma letra ou número e o nome de domínio tem de ser exclusivos dentro da respetiva localização do Azure. Se **verdadeiro** é devolvido o nome proposto é exclusivo global. |
| Crie um endereço IP público |$pip = [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) -Name "myPublicIp" - ResourceGroupName $myResourceGroup - DomainNameLabel "myDNS"-localização $location - AllocationMethod dinâmico<BR><BR>O endereço IP público utiliza o nome de domínio que anteriormente testado e é utilizado na configuração de front-end de Balanceador de carga. |
| Criar uma configuração de IP de front-end |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) -Name "myFrontendIP" - PublicIpAddress $pip<BR><BR>A configuração de front-end inclui o endereço IP público que criou anteriormente para tráfego de rede recebido. |
| Criar um conjunto de endereços de back-end |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Fornece endereços internos para o back-end de Balanceador de carga que são acedidos através de uma interface de rede. |
| Criar uma sonda |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) -Name "myProbe" - RequestPath 'HealthProbe.aspx'-protocolo http-porta 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Contém as sondas de estado de funcionamento utilizadas para verificar a disponibilidade de instâncias de máquinas virtuais no conjunto de endereços de back-end. |
| Criar uma regra de balanceamento de carga |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) -nome HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-sonda $healthProbe-protocolo de Tcp - FrontendPort 80 - BackendPort 80<BR><BR>Contém regras que atribuir a uma porta pública no balanceador de carga para uma porta no conjunto de endereços de back-end. |
| Criar uma regra NAT de entrada |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) -Name "myInboundRule1" - FrontendIpConfiguration $frontendIP-protocolo de TCP - FrontendPort 3441 - BackendPort 3389<BR><BR>Contém as regras de mapeamento de uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no conjunto de endereços de back-end. |
| Criar um balanceador de carga |$loadBalancer = [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancer) - ResourceGroupName $myResourceGroup-Name "myLoadBalancer"-localização $location - FrontendIpConfiguration $frontendIP - regra InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-sonda $healthProbe |
| Criar uma interface de rede |$nic1 = [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) - ResourceGroupName $myResourceGroup-Name "myNIC" - localização $location - PrivateIpAddress XX. X.X.X-sub-rede $subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - um LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede utilizando o endereço IP público e a sub-rede de rede virtual que criou anteriormente. |

## <a name="get-information-about-network-resources"></a>Obter informações sobre recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Lista de redes virtuais |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) - ResourceGroupName $myResourceGroup<BR><BR>Apresenta uma lista de todas as redes virtuais no grupo de recursos. |
| Obter informações sobre uma rede virtual |Get-AzureRmVirtualNetwork-Name "myVNet" - ResourceGroupName $myResourceGroup |
| Lista de sub-redes na rede virtual |Get-AzureRmVirtualNetwork-Name "myVNet" - ResourceGroupName $myResourceGroup &#124; Selecionar sub-redes |
| Obter informações sobre uma sub-rede |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" - VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor de $vnet representa o objeto devolvido pelo Get-AzureRmVirtualNetwork. |
| Lista de endereços IP |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) - ResourceGroupName $myResourceGroup<BR><BR>Apresenta uma lista de endereços IP públicos no grupo de recursos. |
| Lista os balanceadores de carga |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermloadbalancer) - ResourceGroupName $myResourceGroup<BR><BR>Apresenta uma lista de todos os balanceadores de carga no grupo de recursos. |
| Interfaces de rede de lista |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface) - ResourceGroupName $myResourceGroup<BR><BR>Apresenta uma lista de todas as interfaces de rede no grupo de recursos. |
| Obter informações sobre uma interface de rede |Get-AzureRmNetworkInterface-Name "myNIC" - ResourceGroupName $myResourceGroup<BR><BR>Obtém informações sobre uma interface de rede específicas. |
| Obter a configuração de IP de uma interface de rede |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterfaceipconfig) -Name "myNICIP" - NetworkInterface $nic<BR><BR>Obtém informações sobre a configuração de IP da interface de rede especificado. O valor de $nic representa o objeto devolvido pelo Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Gerir recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Adicionar uma sub-rede para uma rede virtual |[AzureRmVirtualNetworkSubnetConfig adicionar](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) - AddressPrefix XX. X.X.X/XX-Name "mySubnet1" - VirtualNetwork $vnet<BR><BR>Adiciona uma sub-rede para uma rede virtual existente. O valor de $vnet representa o objeto devolvido pelo Get-AzureRmVirtualNetwork. |
| Eliminar uma rede virtual |[Remove-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetwork) -Name "myVNet" - ResourceGroupName $myResourceGroup<BR><BR>Remove o grupo de recursos de rede virtual especificado. |
| Eliminar uma interface de rede |[Remover AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermnetworkinterface) -Name "myNIC" - ResourceGroupName $myResourceGroup<BR><BR>Remove o grupo de recursos a interface de rede especificado. |
| Eliminar um balanceador de carga |[Remover AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermloadbalancer) -Name "myLoadBalancer" - ResourceGroupName $myResourceGroup<BR><BR>Remove o grupo de recursos do Balanceador de carga especificado. |
| Eliminar um endereço IP público |[Remover AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermpublicipaddress)-Name "myIPAddress" - ResourceGroupName $myResourceGroup<BR><BR>Remove o endereço IP público especificado o grupo de recursos. |

## <a name="next-steps"></a>Passos Seguintes
* Utilizar a interface de rede que acabou de criar quando a [criar uma VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba mais sobre como pode [criar uma VM com várias interfaces de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).


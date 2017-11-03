---
title: Resolver erros de Azure Application Gateway incorreto Gateway (502) | Microsoft Docs
description: "Saiba como resolver erros 502 de Gateway de aplicação"
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: 6a24e9598362b7c4ff9e2d3371d619fbbd41907f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Resolução de problemas de erros do gateway incorreto no Gateway de aplicação

Saiba como resolver erros de gateway incorreto (502) recebidos ao utilizar o gateway de aplicação.

## <a name="overview"></a>Descrição geral

Depois de configurar um gateway de aplicação, um dos erros de que os utilizadores poderão encontrar é "erro de servidor: 502 - o servidor Web recebeu uma resposta inválida enquanto atuava como servidor de gateway ou proxy". Este erro pode acontecer devido a principais seguintes razões:

* NSG, UDR ou DNS personalizado está a bloquear o acesso aos membros do conjunto de back-end.
* VMs ou instâncias do conjunto de dimensionamento da máquina virtual de back-end são [não responder a sonda de estado de funcionamento predefinida](#problems-with-default-health-probe.md).
* Inválido ou incorrecto [configuração de sondas de estado de funcionamento personalizado](#problems-with-custom-health-probe.md).
* Azure Gateway de aplicação [conjunto back-end não está configurado ou vazio](#empty-backendaddresspool).
* Nenhum dos VMs ou instâncias [conjunto de dimensionamento da máquina virtual estão em bom estado](#unhealthy-instances-in-backendaddresspool).
* [Problemas de conectividade de tempo limite ou de pedido](#request-time-out) com pedidos de utilizador.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema de grupo de segurança de rede, a rota definida pelo utilizador ou o DNS personalizado

### <a name="cause"></a>Causa

Se o acesso ao back-end é bloqueado devido à presença de NSG, UDR ou DNS personalizado, instâncias de Gateway de aplicação não será capazes de alcançar o conjunto de back-end e iriam resultar em falhas de sonda causar 502 erros. Tenha em atenção que o NSG/UDR foi estar presente no sub-rede do Gateway de aplicação ou a sub-rede onde as VMs de aplicação estão implementadas. Da mesma forma presença de DNS personalizado na VNET também pode causar problemas se o FQDN é utilizado para os membros do conjunto de back-end e não está resolvido corretamente pelo servidor DNS de utilizador configurado para a VNET.

### <a name="solution"></a>Solução

Valide a configuração de DNS, UDR e NSG acedendo os seguintes passos:
* Verifique os NSGs associados à sub-rede de Gateway de aplicação. Certifique-se de que a comunicação ao back-end não é bloqueada.
* Verifique UDR associado à sub-rede de Gateway de aplicação. Certifique-se de que UDR não é instruir o tráfego na direção oposta ao back-end sub-rede - por exemplo Verifique a existência de encaminhamento para aplicações virtuais ou as rotas predefinidas que está a ser anunciadas à sub-rede de Gateway de aplicação através do ExpressRoute/VPN de rede.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Certifique-se efetiva NSG e rota com o back-end da VM

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Verifique a presença do DNS personalizado na VNet. DNS pode ser verificado ao observar os detalhes das propriedades da VNet na saída.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se estiver presente, certifique-se de que o servidor DNS é capaz de resolver corretamente o FQDN do membro do conjunto de back-end.

## <a name="problems-with-default-health-probe"></a>Problemas com a pesquisa de estado de funcionamento predefinida

### <a name="cause"></a>Causa

502 erros também podem ser frequentes indicadores que a sonda de estado de funcionamento predefinida não é capaz de alcançar VMs do back-end. Quando uma instância de Gateway de aplicação é aprovisionada, configura automaticamente uma sonda do Estado de funcionamento predefinida para cada BackendAddressPool utilizando propriedades do BackendHttpSetting. Não é necessária nenhuma intervenção do utilizador para definir esta pesquisa. Especificamente, quando uma regra de balanceamento de carga está configurada, é efetuada uma associação entre um BackendHttpSetting e BackendAddressPool. Uma pesquisa predefinida está configurada para cada um destes associações e Gateway de aplicação inicia uma ligação de verificação periódicas de integridade para cada instância na BackendAddressPool, a porta especificada no elemento BackendHttpSetting. A tabela seguinte lista os valores associados à sonda de estado de funcionamento predefinida.

| Propriedade de pesquisa | Valor | Descrição |
| --- | --- | --- |
| URL de sonda |http://127.0.0.1/ |Caminho de URL |
| intervalo |30 |Intervalo de pesquisa em segundos |
| Tempo limite |30 |Sonda de tempo limite em segundos |
| Limiar de mau estado de funcionamento |3 |Sonda de contagem de repetições. O servidor de back-end está marcado como após o número de falhas de sonda consecutivas atinge o limiar de mau estado de funcionamento. |

### <a name="solution"></a>Solução

* Certifique-se de que um site predefinido está configurado e está à escuta em 127.0.0.1.
* Se BackendHttpSetting Especifica uma porta diferente 80, site predefinido deve ser configurado para escutar nessa porta.
* A chamada para http://127.0.0.1:port deverá devolver um código de resultado HTTP de 200. Isto deve ser devolvido dentro do período de tempo limite de 30 segundos.
* Certifique-se de que a porta configurada está aberta e que não são regras de firewall ou grupos de segurança do Azure rede, que bloqueia o tráfego de entrada ou de saída na porta configurada.
* Se as VMs clássicas do Azure ou serviço em nuvem é utilizado com o FQDN ou o IP público, certifique-se de que o correspondente [endpoint](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) é aberto.
* Se a VM está configurada através do Azure Resource Manager e está fora da VNet onde o Gateway de aplicação é implementada, [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) tem de ser configurado para permitir o acesso na porta pretendido.

## <a name="problems-with-custom-health-probe"></a>Problemas com a pesquisa de estado de funcionamento personalizado

### <a name="cause"></a>Causa

Sondas de estado de funcionamento personalizado permitem flexibilidade adicional ao pesquisar o comportamento predefinido. Quando utilizar das sondas personalizadas, os utilizadores podem configurar o intervalo de pesquisa, o URL e caminho para testar e quantos falhadas as respostas de aceitar antes de marcar a instância de conjunto back-end como estando danificado. São adicionadas as seguintes propriedades adicionais.

| Propriedade de pesquisa | Descrição |
| --- | --- |
| Nome |Nome da sonda. Este nome é utilizado para fazer referência para a sonda nas definições de HTTP de back-end. |
| Protocolo |Protocolo utilizado para enviar a pesquisa. A sonda utiliza o protocolo definido nas definições de HTTP de back-end |
| Anfitrião |Nome de anfitrião para enviar a pesquisa. Aplicável apenas quando vários sites está configurada no Gateway de aplicação. Isto é diferente do nome de anfitrião VM. |
| Caminho |Caminho relativo da sonda. O caminho válido começa a partir do '/'. A pesquisa é enviada para \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\> |
| intervalo |Intervalo de pesquisa em segundos. Este é o intervalo de tempo entre dois sondas consecutivos. |
| Tempo limite |Sonda de tempo limite em segundos. Se uma resposta válida não foram recebida durante este período de tempo limite, a pesquisa está marcada como falhado. |
| Limiar de mau estado de funcionamento |Sonda de contagem de repetições. O servidor de back-end está marcado como após o número de falhas de sonda consecutivas atinge o limiar de mau estado de funcionamento. |

### <a name="solution"></a>Solução

Confirme que a sonda de estado de funcionamento personalizado está corretamente configurada como tabela anterior. Para além dos passos de resolução de problemas anteriores, certifique-se também o seguinte:

* Certifique-se de que a sonda foi especificada corretamente como pelo [guia](application-gateway-create-probe-ps.md).
* Se o Gateway de aplicação está configurada para um único site, por predefinição, o anfitrião nome deve ser especificado como '127.0.0.1', a menos que caso contrário configurado na sonda personalizada.
* Certifique-se de que uma chamada para http://\<anfitrião\>:\<porta\>\<caminho\> devolve um código de resultado HTTP de 200.
* Certifique-se de que o intervalo, o tempo limite e UnhealtyThreshold estão nos intervalos de aceitáveis.
* Se utilizar uma pesquisa HTTPS, certifique-se de que o servidor de back-end não necessita de SNI ao configurar um certificado fallback no próprio servidor de back-end. 
* Certifique-se de que o intervalo, o tempo limite e que UnhealtyThreshold estão nos intervalos de aceitáveis.

## <a name="request-time-out"></a>Tempo limite de pedido

### <a name="cause"></a>Causa

Quando é recebido um pedido de utilizador, o Gateway de aplicação aplica as regras configuradas para o pedido e encaminha o mesmo para uma instância de conjunto back-end. Aguarda para um intervalo de tempo para uma resposta da instância de back-end configurável. Por predefinição, este intervalo é **30 segundos**. Se o Gateway de aplicação não recebeu uma resposta de aplicação de back-end este intervalo, a pedido do utilizador é apresentado um erro de 502.

### <a name="solution"></a>Solução

Gateway de aplicação permite aos utilizadores configurar esta definição através de BackendHttpSetting, que pode ser aplicada, em seguida, a conjuntos diferentes. Conjuntos de back-end diferentes podem ter BackendHttpSetting diferentes e tempo limite de pedido, por conseguinte, diferentes configurado.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o Gateway de aplicação não tem as VMs ou conjunto de dimensionamento de máquina virtual configurado no conjunto de endereços de back-end, este não é possível encaminhar qualquer pedido de cliente e emite um erro de gateway inválido.

### <a name="solution"></a>Solução

Certifique-se de que o conjunto de endereços de back-end não está vazio. Isto pode ser feito um através do PowerShell, o CLI ou o portal.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

O resultado do cmdlet anterior deve conter o conjunto de endereços de back-end não vazia. Segue-se um exemplo em que dois conjuntos, são devolvidos que estão configurados com endereços IP ou FQDN, para as VMs de back-end. O estado de aprovisionamento do BackendAddressPool tem de ser 'foi concluída com êxito'.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instâncias de mau estado de funcionamento da BackendAddressPool

### <a name="cause"></a>Causa

Se todas as instâncias de BackendAddressPool mau estado de funcionamento, o Gateway de aplicação teria não qualquer back-end para o pedido de utilizador de rota. Isto também pode ser o caso quando estão em bom estadas de instâncias de back-end mas não dispõe de aplicações necessárias implementadas.

### <a name="solution"></a>Solução

Certifique-se de que as instâncias estão em bom estadas e a aplicação está configurada corretamente. Verifique se as instâncias de back-end são capazes de responder a um ping de outra VM na mesma VNet. Se configurado com um ponto final público, certifique-se de que um pedido de browser para a aplicação web é um.

## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra uma [suporta permissão](https://azure.microsoft.com/support/options/).


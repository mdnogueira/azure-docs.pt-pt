---
title: "Repor um gateway de VPN do Azure para restabelecer túneis IPsec | Microsoft Docs"
description: "Este artigo explica como repor o Gateway de VPN do Azure para restabelecer túneis IPsec. O artigo aplica-se a gateways VPN no clássica tanto os modelos de implementação Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: cherylmc
ms.openlocfilehash: 604af54d034b6b51a96ff098827e2352a163be18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reset-a-vpn-gateway"></a>Repor um Gateway de VPN

Repor o gateway de VPN do Azure é útil se perder a conectividade VPN em vários locais num ou mais túneis de rede de VPNs. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Este artigo ajuda-o a repor o gateway VPN.

### <a name="what-happens-during-a-reset"></a>O que acontece durante uma reposição?

Um gateway de VPN é composto por duas instâncias VM com uma configuração de modo de espera ativo. Ao repor o gateway, reinicia o gateway e, em seguida, volta as configurações em vários locais ao mesmo. O gateway mantém o endereço IP público que já tem, ou seja, não terá de atualizar a configuração do router da VPN com um novo endereço IP público para o VPN Gateway do Azure.

Quando emitir o comando de repor o gateway, a instância ativa atual do gateway de VPN do Azure é reiniciada imediatamente. Existirá um intervalo breve durante a ativação pós-falha da instância ativa (que está a ser reiniciada), para a instância de modo de espera. O intervalo deve ser inferior a um minuto.

Se a ligação não for restaurada após o primeiro reinício, execute o mesmo comando novamente para reiniciar a segunda instância da VM (o novo gateway ativo). Se forem pedidos dois reinícios seguidos, haverá um período ligeiramente superior em que ambas as instâncias da VM (ativa e no modo de espera) estão a ser reiniciadas. Isto resultará num intervalo maior na conetividade da VPN, de até 2 a 4 minutos para as VMs concluírem os reinícios.

Depois de dois reinícios, se ainda ocorrerem problemas de conectividade em vários locais, abra um pedido de suporte do portal do Azure.

## <a name="before"></a>Antes de começar

Antes de repor o gateway, verifique os itens principais listados abaixo para cada túnel de rede de VPNs (S2S) do IPsec. Qualquer erro de correspondência nos itens desligará os túneis VPN S2S. A verificar e corrigir as configurações para no local e os gateways de VPN do Azure poupa reinícios desnecessários e interrupções para as outras ligações de trabalho nos gateways.

Verifique os itens seguintes antes de repor o gateway:

* Os endereços IP de Internet (VIPs) do VPN Gateway do Azure e do gateway de VPN no local estão corretamente configurados nas políticas de VPN do Azure e no local.
* A chave pré-partilhada tem de ser igual no VPN Gateway do Azure e no gateway no local.
* Se aplicar uma configuração IPsec/IKE específica, tal como encriptação, algoritmos hash e PFS (Perfect Forward Secrecy), confirme se o VPN Gateway do Azure e o gateway no local têm as mesmas configurações.

## <a name="portal"></a>Portal do Azure

Só pode repor um gateway de VPN do Resource Manager através do portal do Azure. Se pretender repor um gateway clássico, consulte o [PowerShell](#resetclassic) passos.

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

1. Abra o [portal do Azure](https://portal.azure.com) e navegue para o gateway de rede virtual do Gestor de recursos que pretende repor.
2. No painel de gateway de rede virtual, clique em 'Repor'.

  ![Repor o painel de Gateway de VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. No painel da reposição, clique em de **repor** botão.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Modelo de implementação Resource Manager

O cmdlet para repor um gateway é **reposição AzureRmVirtualNetworkGateway**. Antes de efetuar uma reposição, certifique-se de que a versão mais recente do [cmdlets PowerShell do Resource Manager](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0). O exemplo seguinte repõe um gateway de rede virtual com o nome VNet1GW no grupo de recursos de TestRG1:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultado:

Quando receber um resultado de retorno, pode assumir a reposição do gateway foi concluída com êxito. No entanto, não há nada no resultado de retorno que indique explicitamente que a reposição foi concluída com êxito. Se pretender examinar rigorosamente o histórico para ver exatamente quando a reposição de gateway ocorreu, pode ver essa informação no [portal do Azure](https://portal.azure.com). No portal, navegue para **'GatewayName'-Estado de funcionamento do recurso >**.

### <a name="resetclassic"></a>Modelo de implementação clássica

O cmdlet para repor um gateway é **Reset-AzureVNetGateway**. Antes de efetuar uma reposição, certifique-se de que a versão mais recente do [cmdlets do PowerShell de gestão de serviço (SM)](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0). O exemplo seguinte repõe o gateway para uma rede virtual denominada "ContosoVNet":

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

Resultado:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>CLI do Azure

Para repor o gateway, utilize o [repor o gateway de vnet do rede az](https://docs.microsoft.com/cli/azure/network/vnet-gateway#az_network_vnet_gateway_reset) comando. O exemplo seguinte repõe um gateway de rede virtual com o nome VNet5GW no grupo de recursos de TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultado:

Quando receber um resultado de retorno, pode assumir a reposição do gateway foi concluída com êxito. No entanto, não há nada no resultado de retorno que indique explicitamente que a reposição foi concluída com êxito. Se pretender examinar rigorosamente o histórico para ver exatamente quando a reposição de gateway ocorreu, pode ver essa informação no [portal do Azure](https://portal.azure.com). No portal, navegue para **'GatewayName'-Estado de funcionamento do recurso >**.
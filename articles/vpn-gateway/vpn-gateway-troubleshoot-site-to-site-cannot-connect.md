---
title: "Resolver problemas de uma ligação de VPN de site para site do Azure que não é possível ligar | Microsoft Docs"
description: "Saiba como resolver problemas de uma ligação de VPN de site a site que subitamente deixa de funcionar e não pode ser novamente ligada."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: genli
ms.openlocfilehash: 55cfba5e9730b123bba20dfdc5d10c1157352a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Resolução de problemas: Uma ligação de VPN de site para site do Azure não é possível ligar e deixa de funcionar

Depois de configurar uma ligação de VPN de site a site entre uma rede no local e uma rede virtual do Azure, a ligação VPN deixa de funcionar e não pode ser novamente ligada. Este artigo fornece os passos de resolução de problemas para o ajudar a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver o problema, tente primeiro [repor o gateway de VPN do Azure](vpn-gateway-resetgw-classic.md) e repor o túnel do dispositivo VPN no local. Se o problema persistir, siga estes passos para identificar a causa do problema.

### <a name="prerequisite-step"></a>Passo pré-requisitos

Verifique o tipo de gateway de VPN do Azure.

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Verifique o **descrição geral** página do gateway VPN para obter as informações de tipo.
    
    ![Descrição geral do gateway](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1. Verifique se o dispositivo VPN no local é validado

1. Verifique se estão a utilizar um [validar o dispositivo VPN e versão do sistema operativo](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo não for um dispositivo VPN validado, terá de contactar o fabricante do dispositivo para verificar se existe um problema de compatibilidade.

2. Certifique-se de que o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [editar exemplos de configuração do dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Passo 2. Certifique-se a chave partilhada

Compare a chave partilhada para o dispositivo VPN no local para a VPN de rede Virtual do Azure para se certificar de que as chaves correspondem. 

Para ver a chave partilhada para a ligação de VPN do Azure, utilize um dos seguintes métodos:

**Portal do Azure**

1. Vá para a ligação de site para site de gateway VPN que criou.

2. No **definições** secção, clique em **chave partilhada**.
    
    ![Chave partilhada](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Para o modelo de implementação Azure Resource Manager:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Para o modelo de implementação clássica:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Passo 3. Verificar o elemento de rede VPN IPs

-   A definição de IP no **Gateway de rede Local** objeto no Azure deve corresponder ao IP de dispositivos no local.
-   A definição de IP do gateway do Azure que está definida no dispositivo local deve corresponder de IP do gateway do Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Passo 4. Verificar UDR e NSGs a sub-rede do gateway

Procurar e remover o encaminhamento definido pelo utilizador (UDR) ou grupos de segurança de rede (NSGs) na sub-rede de gateway e, em seguida, testar o resultado. Se o problema ser resolvido, Valide as definições que UDR ou NSG aplicado.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Passo 5. Verifique o endereço de interface externa do dispositivo VPN no local

- Se o endereço IP para a Internet do dispositivo VPN está incluído no **rede Local** definição no Azure, podem ocorrer esporádicas interrupções de ligação.
- Interface externa do dispositivo tem de ser diretamente na Internet. Não deverá haver nenhum tradução de endereços de rede ou firewall entre o dispositivo e da Internet.
- Para configurar a firewall do clustering de ter um IP virtual, tem de interromper o cluster e expor o dispositivo de VPN diretamente a uma interface público que o gateway pode interface com.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Passo 6. Certifique-se de que as sub-redes de corresponder exatamente (gateways de baseado na política do Azure)

-   Certifique-se de que as sub-redes correspondem exatamente entre a rede virtual do Azure e definições no local para a rede virtual do Azure.
-   Certifique-se de que as sub-redes correspondem exatamente entre as **Gateway de rede Local** e definições para a rede no local no local.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Passo 7. Certifique-se a pesquisa de estado de funcionamento do gateway do Azure

1. Vá para o [sonda do Estado de funcionamento](https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe).

2. Clicar no aviso de certificado.
3. Se receber uma resposta, o gateway VPN é considerado em bom estado. Se não receber uma resposta, o gateway poderá não estar em bom estado de funcionamento ou um NSG a sub-rede de gateway está a causar o problema. O texto seguinte é uma resposta de exemplo:

    &lt;? a versão de xml = "1.0"? > <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">instância primário: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6 < / cadeia&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Passo 8. Verifique se o dispositivo VPN no local com a funcionalidade de perfeita secrecy reencaminhar ativada

A funcionalidade de perfeita secrecy reencaminhar pode causar problemas de interrupção de ligação. Se o dispositivo VPN tem perfeita secrecy reencaminhar ativada, desative a funcionalidade. Em seguida, atualize o política IPsec do gateway de VPN.

## <a name="next-steps"></a>Passos seguintes

-   [Configurar uma ligação site a site a uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configurar uma política de IPsec/IKE para ligações de VPN de site a site](vpn-gateway-ipsecikepolicy-rm-powershell.md)

---
title: Resolver problemas do Azure--VPNs desliga ligados intermitentemente | Microsoft Docs
description: "Saiba como resolver o problema no qual a ligação VPN de Site para Site desligado regularmente."
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
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 96202a4d7e91a221aa655f58f29f2c8e24b86e4e
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Resolução de problemas: VPN de Site para Site do Azure desliga ligados intermitentemente

Podem ocorrer problemas se uma ligação de VPN do Microsoft Azure ponto a Site nova ou existente não está estável ou desliga regularmente. Este artigo fornece os passos para o ajudar a identificar e resolver a causa do problema de resolução de problemas. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

### <a name="prerequisite-step"></a>Passo pré-requisitos

Verifique o tipo de gateway de rede virtual do Azure:

1. Aceda a [portal do Azure](https://portal.azure.com).
2. Verifique o **descrição geral** página do gateway de rede virtual para obter as informações de tipo.
    
    ![Descrição geral do gateway](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Passo 1 Verifique se o dispositivo VPN no local é validado

1. Verifique se estão a utilizar um [validar o dispositivo VPN e versão do sistema operativo](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo VPN não é validado, poderá ter de contacte o fabricante do dispositivo para verificar se existe qualquer problema de compatibilidade.
2. Certifique-se de que o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [editar exemplos de configuração do dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Passo 2 Verifique as definições de associação de segurança (para gateways de rede virtual do Azure baseada em políticas)

1. Certifique-se de que a rede virtual, sub-redes e intervalos de no **gateway de rede Local** definição no Microsoft Azure são as mesmas como a configuração no dispositivo VPN no local.
2. Certifique-se de que as definições de associação de segurança correspondem.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Passo 3 procurar rotas definidas pelo utilizador ou grupos de segurança de rede na sub-rede de Gateway

Uma rota definida pelo utilizador na sub-rede de gateway pode ser restringir algum tráfego e permitindo que o tráfego restante. Isto torna aparecer que a ligação VPN é pouco fiáveis para algum tráfego e ideais para outras pessoas. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Passo 4 verificar o "um túnel VPN por par sub-rede" definição (para gateways de rede virtual baseada em políticas)

Certifique-se de que o dispositivo VPN no local é definido para ter **um túnel VPN por par sub-rede** para gateways de rede virtual baseada em políticas.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Passo 5 procurar limitação de associação de segurança (para gateways de rede virtual baseada em políticas)

O gateway de rede virtual baseado em política tem o limite de 200 pares de associação de segurança de sub-rede. Se o número de sub-redes de rede virtual do Azure multiplicado vezes pelo número de sub-redes locais é superior a 200, consulte sub-redes esporádicas desligar.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Passo 6 verificação no local o endereço de interface externa do dispositivo VPN

- Se a Internet com o endereço IP do dispositivo VPN está incluída no **gateway de rede Local** definição no Azure, pode deparar-se esporádicas interrupções de ligação.
- Interface externa do dispositivo tem de ser diretamente na Internet. Não deverá haver nenhum tradução de endereços de rede (NAT) ou firewall entre o dispositivo e da Internet.
-  Se configurar a Firewall do Clustering de ter um IP virtual, tem de interromper o cluster e expor o dispositivo de VPN diretamente a uma interface público que o gateway pode interface com.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Passo 7 Verifique se o dispositivo VPN no local tem Perfect Forward Secrecy ativada

O **Perfect Forward Secrecy** funcionalidade pode causar problemas a interrupção de ligação. Se o dispositivo VPN tem **perfeita Secrecy reencaminhar** ativada, desative a funcionalidade. Em seguida, [atualizar o gateway de rede virtual política IPsec](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Passos seguintes

- [Configurar uma ligação Site a Site a uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Configurar a política de IPsec/IKE para ligações VPN de Site a Site](vpn-gateway-ipsecikepolicy-rm-powershell.md)


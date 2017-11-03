---
title: Sobre os requisitos de criptografia e gateways de VPN do Azure | Microsoft Docs
description: Este artigo descreve os requisitos de criptografia e gateways de VPN do Azure
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: d2f3da47f1d4eebe1b81964790ff6612dd78155d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Sobre os requisitos de criptografia e gateways de VPN do Azure

Este artigo descreve como pode configurar gateways de VPN do Azure para satisfazer os requisitos de criptografia para túneis S2S VPN de vários locais e ligações VNet a VNet no Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Acerca dos parâmetros de política IPsec e IKE para gateways de VPN do Azure
Protocolo IPsec e IKE padrão suporta uma vasta gama de algoritmos criptográficos várias combinações. Se os clientes pedem uma combinação específica de algoritmos criptográficos e parâmetros, os gateways de VPN do Azure utilizam um conjunto de propostas de predefinição. Os conjuntos de política predefinida foram escolhidos para maximizar a interoperabilidade com uma vasta gama de dispositivos VPN de terceiros em configurações predefinidas. Como resultado, as políticas e o número de propostas não podem abranger todas as possíveis combinações de algoritmos criptográficos disponíveis e força da codificação chave.

A política predefinida definida para o gateway de VPN do Azure está listado no documento: [acerca dos dispositivos VPN e parâmetros IPsec/IKE para ligações de Gateway de VPN de Site para Site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos de criptografia
Para comunicações que necessitam de algoritmos criptográficos específicos ou parâmetros, normalmente devido a conformidade ou requisitos de segurança, os clientes já podem configurar os seus gateways de VPN do Azure para utilizar uma política personalizada do IPsec/IKE com específicos de criptografia algoritmos e chave força da codificação, em vez dos conjuntos de política predefinido do Azure.

Por exemplo, as políticas de modo principal IKEv2 para gateways de VPN do Azure utilizam apenas Diffie-Hellman Grupo 2 (1024 bits), enquanto que os clientes poderão ter de especificar grupos quanto mais forte fortes a serem utilizados no IKE, tais como 14 de grupo (2048 bits), grupo 24 (grupo MODP de 2048 bits) ou ECP (elíptica curva grupos) 384 ou 256 bits (e de grupo 19 20 de grupo, respetivamente). Requisitos semelhantes se aplicam às políticas de modo rápido IPsec bem.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Política personalizada do IPsec/IKE com gateways de VPN do Azure
Gateways de VPN do Azure suportam agora por ligação, a política personalizada do IPsec/IKE. Para um Site para Site ou a ligação VNet a VNet, pode escolher uma combinação específica de algoritmos criptográficos para IPsec e IKE à força chave pretendida, conforme mostrado no exemplo seguinte:

![política de ike IPSec](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Pode criar uma política de IPsec/IKE e aplicar a uma ligação nova ou existente. 

### <a name="workflow"></a>Fluxo de trabalho

1. Criar os gateways de rede local para a topologia de conectividade, conforme descrito em outros documentos de procedimentos, redes virtuais ou gateways de VPN
2. Criar uma política de IPsec/IKE
3. Pode aplicar a política quando cria uma ligação S2S ou VNet a VNet
4. Se a ligação já está a ser criada, pode aplicar ou atualizar a política para uma ligação existente


## <a name="ipsecike-policy-faq"></a>FAQ da política de IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Passos seguintes
Consulte [política IPsec/IKE configurar](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter instruções passo a passo sobre como configurar a política personalizada do IPsec/IKE numa ligação.

Consulte também [ligar vários dispositivos VPN baseado na política](vpn-gateway-connect-multiple-policybased-rm-ps.md) para saber mais sobre a opção de UsePolicyBasedTrafficSelectors.
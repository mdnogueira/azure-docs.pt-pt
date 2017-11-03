---
title: "Redes de pilha do Azure: Diferenças e as considerações"
description: "Saiba mais sobre as diferenças e as considerações ao trabalhar com redes na pilha do Azure."
services: azure-stack
keywords: 
author: ScottNapolitan
ms.author: victorh
ms.date: 9/25/2017
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 7b7bac508a759a1367ac7328840848efe17ea3c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="considerations-for-azure-stack-networking"></a>Considerações para redes de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Funcionamento em rede na pilha do Azure fornece muitas das funcionalidades que encontrar no Azure, com algumas diferenças que deve saber antes de iniciar a implementação.


Este artigo fornece uma descrição geral das considerações de exclusivas para o funcionamento em rede e as respetivas funcionalidades na pilha do Azure. Para saber mais sobre das principais diferenças entre a pilha do Azure e do Azure, consulte o [chave considerações](azure-stack-considerations.md) tópico.


## <a name="cheat-sheet-networking-differences"></a>Cheat folha: redes de diferenças

|Serviço | Funcionalidade | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | DNS de multi-inquilino | Suportado| Ainda não suportado|
| |Registos DNS AAAA|Suportado|Não suportado|
| |Zonas DNS por subscrição|100 (predefinição)<br>Pode ser aumentado no pedido.|100|
| |Conjuntos de registos DNS por zona|5000 (predefinição)<br>Pode ser aumentado no pedido.|5000|
||Servidores de nomes para a delegação de zona|Azure fornecem quatro servidores de nomes para cada zona de utilizador (inquilino) que é criada.|Pilha do Azure fornece dois servidores de nomes para cada zona de utilizador (inquilino) que é criada.|
| Rede virtual|Peering de rede virtual|Ligar duas redes virtuais na mesma região através da rede principal do Azure.|Ainda não suportado|
| |Endereços IPv6|Pode atribuir um endereço IPv6 como parte do [configuração de Interface de rede](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|É apenas suportado o IPv4.|
|Gateways de VPN|Gateway de VPN de ponto a Site|Suportado|Ainda não suportado|
| |Gateway de Vnet a Vnet|Suportado|Ainda não suportado|
| |SKUs do VPN Gateway|Suporte para básico, GW1, GW2, GW3, padrão de elevado desempenho, Ultra-elevado desempenho. |Suporte para básico, Standard e SKUs de elevado desempenho.|
|Load balancer|Endereços IP públicos do IPv6|Suporte para atribuir um endereço IP público IPv6 a um balanceador de carga.|É apenas suportado o IPv4.|
|Observador de Rede|Capacidades de monitorização de rede de inquilino de observador de rede|Suportado|Ainda não suportado|
|CDN|Perfis de rede de entrega de conteúdo|Suportado|Ainda não suportado|
|Gateway de aplicação|Balanceamento de carga de 7 camadas|Suportado|Ainda não suportado|
|Gestor de Tráfego|Encaminhar o tráfego de entrada para o desempenho da aplicação ideal e fiabilidade.|Suportado|Ainda não suportado|
|ExpressRoute|Configure uma ligação privada rápida ao cloud services da Microsoft da sua infraestrutura ou de colocalização de instalações no local.|Suportado|Suporte para ligar a pilha do Azure para um circuito Expressroute.|

## <a name="next-steps"></a>Passos seguintes

[DNS no Azure Stack](azure-stack-dns.md)

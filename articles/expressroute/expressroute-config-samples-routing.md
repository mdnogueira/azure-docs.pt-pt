---
title: "Exemplos de configuração de router de cliente do ExpressRoute | Microsoft Docs"
description: "Esta página fornece exemplos de configuração de router para os routers Cisco e Juniper."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exemplos de configuração de router para configurar e gerir encaminhamento
Esta página fornece uma interface e encaminhamento amostras de configuração para Cisco IOS-XE e Juniper MX routers da série. Estes são se destina a ser amostras para obter orientações sobre apenas e não pode ser utilizados tal como está. Pode trabalhar com o fornecedor de pensar em configurações adequadas para a sua rede. 

> [!IMPORTANT]
> Os exemplos nesta página são se destina a ser puramente para obter orientações. Tem de trabalhar com a equipa de vendas / técnica do fornecedor e a sua equipa de rede para detetar um com configurações adequadas para satisfazer as necessidades. Microsoft não irá suportar problemas relacionados com configurações listadas nesta página. Tem de contactar o fornecedor do dispositivo para problemas de suporte.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Definições de MTU e TCP MSS em interfaces de router
* O MTU para a interface de ExpressRoute é 1500, que é a MTU de predefinida típica para uma interface de Ethernet num router. A menos que o router tenha uma MTU diferente, por predefinição, não é necessário especificar um valor na interface de router.
* Ao contrário de um Gateway de VPN do Azure, o TCP MSS para um circuito ExpressRoute não precisa de ser especificado.

Exemplos de configuração de router abaixo aplicam-se a todos os peerings. Reveja [ExpressRoute peerings](expressroute-circuit-peerings.md) e [requisitos de encaminhamento do ExpressRoute](expressroute-routing.md) para obter mais detalhes sobre o encaminhamento.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE com base em routers
Os exemplos nesta secção aplicam-se para qualquer router com a família de sos IOS XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurar as interfaces e interfaces secundárias
Precisa de uma interface sub por peering cada router que à Microsoft. Uma interface sub pode ser identificada com um ID de VLAN ou um par empilhado de IDs de VLAN e um endereço IP.

**Definição de interface Dot1Q**

Este exemplo fornece a definição de interface secundárias para uma interface de rede secundárias com um único ID de VLAN. O ID de VLAN é exclusivo por peering. O octeto última do seu endereço de IPv4 será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definição de interface QinQ**

Este exemplo fornece a definição de interface secundárias para uma interface com dois IDs de VLAN secundária. O ID de VLAN (s-etiqueta), se utilizados externa permanece igual em todos os peerings. O ID de VLAN (c-tag) interna é exclusivo por peering. O octeto última do seu endereço de IPv4 será sempre um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Como configurar sessões de eBGP
Tem de configurar uma sessão BGP com a Microsoft para cada peering. O exemplo abaixo permite-lhe configurar uma sessão BGP com a Microsoft. Se o endereço IPv4 utilizado para a interface sub foi sendo, o endereço IP de vizinho o BGP (Microsoft) será + 1. O octeto última do endereço de IPv4 o vizinho BGP será sempre um número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configuração de prefixos para ser anunciados na sessão do BGP
Pode configurar o router para anunciar prefixos selecionados à Microsoft. Pode fazê-utilizando o exemplo abaixo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Mapas de rota
Pode utilizar os mapas de rota e apresenta uma lista de prefixo para prefixos de filtro propagados na sua rede. Pode utilizar o exemplo abaixo para realizar a tarefa. Certifique-se de que tem o programa de configuração do prefixo adequado listas.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Routers de série Juniper MX
Os exemplos nesta secção aplicam-se para todos os routers de série de Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurar as interfaces e interfaces secundárias

**Definição de interface Dot1Q**

Este exemplo fornece a definição de interface secundárias para uma interface de rede secundárias com um único ID de VLAN. O ID de VLAN é exclusivo por peering. O octeto última do seu endereço de IPv4 será sempre um número ímpar.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Definição de interface QinQ**

Este exemplo fornece a definição de interface secundárias para uma interface com dois IDs de VLAN secundária. O ID de VLAN (s-etiqueta), se utilizados externa permanece igual em todos os peerings. O ID de VLAN (c-tag) interna é exclusivo por peering. O octeto última do seu endereço de IPv4 será sempre um número ímpar.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Como configurar sessões de eBGP
Tem de configurar uma sessão BGP com a Microsoft para cada peering. O exemplo abaixo permite-lhe configurar uma sessão BGP com a Microsoft. Se o endereço IPv4 utilizado para a interface sub foi sendo, o endereço IP de vizinho o BGP (Microsoft) será + 1. O octeto última do endereço de IPv4 o vizinho BGP será sempre um número par.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configuração de prefixos para ser anunciados na sessão do BGP
Pode configurar o router para anunciar prefixos selecionados à Microsoft. Pode fazê-utilizando o exemplo abaixo.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Mapas de rota
Pode utilizar os mapas de rota e apresenta uma lista de prefixo para prefixos de filtro propagados na sua rede. Pode utilizar o exemplo abaixo para realizar a tarefa. Certifique-se de que tem o programa de configuração do prefixo adequado listas.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Passos Seguintes
Veja [FAQ do ExpressRoute](expressroute-faqs.md) para obter mais detalhes.


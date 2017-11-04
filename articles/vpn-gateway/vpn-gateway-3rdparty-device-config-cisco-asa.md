---
title: "Configuração de exemplo para ligar dispositivos de Cisco ASA para gateways de VPN do Azure | Microsoft Docs"
description: "Este artigo fornece uma configuração de exemplo para ligar dispositivos de Cisco ASA para gateways de VPN do Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: 080f83a67674ab059404870f6ec0e7470cfcceff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Configuração de exemplo: Cisco ASA dispositivo (IKEv2/não BGP)
Este artigo fornece configurações de exemplo para dispositivos de ligação da aplicação segurança adaptável Cisco (ASA) para gateways de VPN do Azure. O exemplo que se aplica a dispositivos Cisco ASA que estejam a executar o IKEv2 sem Border Gateway Protocol (BGP). 

## <a name="device-at-a-glance"></a>Dispositivo de forma rápida

|                        |                                   |
| ---                    | ---                               |
| Fabricante do dispositivo          | Cisco                             |
| Modelo do dispositivo           | ASA                               |
| Versão de destino         | 8.4 e posterior                     |
| Modelo testado           | ASA 5505                          |
| Versão testada         | 9.2                               |
| Versão do IKE            | IKEv2                             |
| BGP                    | Não                                |
| Tipo de gateway VPN do Azure | Gateway VPN baseado na rota           |
|                        |                                   |

> [!NOTE]
> A configuração de exemplo liga um dispositivo Cisco ASA a um Azure **baseado na rota** gateway de VPN. A ligação utiliza uma política de IPsec/IKE personalizada com o **UsePolicyBasedTrafficSelectors** opção, conforme descrito em [neste artigo](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> O exemplo requer que as utilizam dispositivos ASA o **IKEv2** política com configurações de lista-baseada no acesso, não VTI com base em. Consulte as especificações de fornecedor do dispositivo VPN para verificar se o IKEv2 política é suportada nos seus dispositivos VPN no local.


## <a name="vpn-device-requirements"></a>Requisitos de dispositivos VPN
Gateways de VPN do Azure utilizam os conjuntos de protocolo de IPsec/IKE padrão para estabelecer túneis VPN de Site a Site (S2S). Para os parâmetros de protocolo de IPsec/IKE detalhados e algoritmos de criptografia predefinido para gateways de VPN do Azure, consulte [acerca dos dispositivos VPN](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Opcionalmente, pode especificar uma combinação exata de algoritmos criptográficos e força da codificação chave para uma ligação específica, conforme descrito em [sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md). Se especificar uma combinação exata de algoritmos e chave força da codificação, lembre-se de que utiliza as especificações correspondentes nos seus dispositivos VPN.

## <a name="single-vpn-tunnel"></a>Túnel VPN único
Esta configuração é composta por um único túnel de S2S VPN entre um gateway de VPN do Azure e um dispositivo VPN no local. Opcionalmente, pode configurar o [BGP através do túnel VPN](#bgp).

![Túnel S2S VPN único](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Para obter instruções passo a passo Criar as configurações do Azure, consulte [a configuração de túnel VPN único](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Rede virtual e informações do gateway de VPN
Esta secção lista os parâmetros para o exemplo.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereços de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP do gateway VPN do Azure         | Azure_Gateway_Public_IP      |
| Prefixos de endereço no local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP do dispositivo VPN no local    | OnPrem_Device_Public_IP     |
| * Rede virtual ASN de BGP                | 65010                        |
| * IP do elemento BGP do azure           | 10.12.255.30                 |
| * No local ASN de BGP         | 65050                        |
| * IP do elemento BGP no local     | 10.52.255.254                |
|                              |                              |

\*Parâmetro opcional para BGP apenas.

### <a name="ipsecike-policy-and-parameters"></a>Política de IPsec/IKE e parâmetros
A tabela seguinte lista os parâmetros que são utilizados no exemplo e algoritmos de IPsec/IKE. Consulte as especificações de dispositivo VPN para verificar os algoritmos que são suportados para os modelos de dispositivos VPN e as versões de firmware.

| **IPsec/IKEv2**  | **Valor**                            |
| ---              | ---                                  |
| Encriptação IKEv2 | AES256                               |
| Integridade do IKEv2  | SHA384                               |
| Grupo DH         | DHGroup24                            |
| * A encriptação de IPsec | AES256                               |
| * Integridade IPsec  | SHA1                                 |
| Grupo PFS        | PFS24                                |
| Duração de SA QM   | segundos de 7.200                         |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors $True |
| Chave Pré-partilhada   | PreSharedKey                         |
|                  |                                      |

\*Em alguns dispositivos, IPsec integridade tem de ser um valor nulo quando o algoritmo de encriptação de IPsec AES GCM.

### <a name="asa-device-support"></a>Suporte de dispositivos do ASA

* Suporte para IKEv2 requer ASA versão 8.4 e posterior.

* Suporte para o grupo DH e grupo PFS além do grupo de 5 requer a versão do ASA 9.x.

* Suporte para encriptação de IPsec com AES-GCM e IPsec integridade com o SHA-256, SHA-384 ou SHA-512, requer a versão do ASA 9.x. Este requisito de suporte aplica-se aos dispositivos ASA mais recentes.

    > [!NOTE]
    > Modelos de dispositivos do ASA 5505, 5510, 5520, 5540, 5550 e 5580 não são suportados. Consulte as especificações de dispositivo VPN para verificar os algoritmos que são suportados para os modelos de dispositivos VPN e as versões de firmware.


### <a name="sample-device-configuration"></a>Exemplo de configuração de dispositivo
O script fornece um exemplo que se baseia na configuração e os parâmetros que são descritos nas secções anteriores. A configuração de túnel S2S VPN consiste dos seguintes partes:

1. Interfaces e as rotas
2. Apresenta uma lista de acesso
3. Política de IKE e parâmetros (fase 1 ou modo principal)
4. Política de IPsec e parâmetros (fase 2 ou modo rápido)
5. Outros parâmetros, tal como TCP MSS clamping

> [!IMPORTANT]
> Conclua os passos seguintes antes de utilizar o script de exemplo. Substitua os valores de marcador de posição no script com as definições do dispositivo para a sua configuração.

* Especifique a configuração de interface para dentro e fora de interfaces.
* Identifica as rotas para as redes privadas/dentro e fora/público.
* Certifique-se de que todos os nomes e números de política são exclusivos no seu dispositivo.
* Certifique-se de que os algoritmos criptográficos são suportados no seu dispositivo.
* Substitua o seguinte **valores de marcador de posição** valores reais para a sua configuração:
  - Fora do nome de interface: **fora**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Rede virtual e os nomes de gateway de rede local: **VNetName** e **LNGName**
  - Endereço de rede no local e de rede virtual **prefixos**
  - Adequada **netmasks**

#### <a name="sample-script"></a>Script de exemplo

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Comandos de depuração simples

Utilize os seguintes comandos do ASA para fins de depuração:

* Mostra a associação de segurança IPsec ou IKE (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Introduza o modo de depuração:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    O `debug` comandos podem gerar a saída significativa na consola.

* Mostra as configurações atuais do dispositivo:
    ```
    show run
    ```
    Utilize `show` subcomandos para partes específicas de lista de configuração do dispositivo, por exemplo:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Passos seguintes
Para configurar ativo-ativo em vários locais e ligações VNet a VNet, consulte [configurar gateways de VPN de ativo-ativo](vpn-gateway-activeactive-rm-powershell.md).

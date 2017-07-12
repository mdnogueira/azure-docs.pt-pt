<a id="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>

### A política de IPsec/IKE personalizado é suportada em todos os SKU de Gateway de VPN do Azure?
A política de IPsec/IKE personalizada é suportada no Azure **VpnGw1, VpnGw2, VpnGw3, Standard** e nos gateways de VPN **HighPerformance**. O SKU **Básico** NÃO é suportado.

<a id="how-many-policies-can-i-specify-on-a-connection" class="xliff"></a>

### Quantas políticas posso especificar numa ligação?
Só pode especificar ***uma*** combinação de políticas para uma determinada ligação.

<a id="can-i-specify-a-partial-policy-on-a-connection-eg-only-ike-algorithms-but-not-ipsec" class="xliff"></a>

### Posso especificar uma política parcial numa ligação? (Por exemplo, somente os algoritmos de IKE mas não IPsec)
Não, tem de especificar todos os parâmetros e algoritmos de IKE (modo principal) e IPsec (modo rápido). Não é permitida a especificação da política parcial.

<a id="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy" class="xliff"></a>

### Quais são os algoritmos e os principais pontos fortes suportados na política personalizada?
A tabela a seguir lista os algoritmos de criptográficos suportados e os principais pontos fortes configuráveis pelos clientes. Tem de selecionar uma opção para cada campo.

| **IPsec/IKEv2**  | **Opções**                                                                 |
| ---              | ---                                                                         |
| Encriptação IKEv2 | AES256, AES192, AES128, DES3, DES                                           |
| Integridade do IKEv2  | SHA384, SHA256, SHA1, MD5                                                   |
| Grupo DH         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Nenhum |
| Encriptação do IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nenhum    |
| Integridade do IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                          |
| Grupo PFS        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, Nenhum                     |
| Duração de SA QM*  | Segundos (inteiro; **mín. 300**) e KBytes (inteiro; **mín. 1024**)                                      |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors** ($True/$False; default $False)                             |
|                  |                                                                             |

* (*) A duração do SA do modo principal do IKEv2 é fixa em 28 800 segundos em gateways de VPN do Azure
* (**) Consulte o item seguinte de perguntas frequentes para "UsePolicyBasedTrafficSelectors"

<a id="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations" class="xliff"></a>

### Tudo o que precisa para fazer a correspondência entre a política de gateway VPN do Azure e as minhas configurações de dispositivo VPN local?
A configuração de dispositivo VPN local deve corresponder ou deve conter os seguintes algoritmos e parâmetros que especificar na política de IPsec/IKE do Azure:

* Algoritmo de encriptação do IKE
* Algoritmo de integridade do IKE
* Grupo DH
* Algoritmo de encriptação do IPsec
* Algoritmo de integridade do IPsec
* Grupo PFS
* Seletor de tráfego (*)

A duração do SA é apenas a especificação local, não é necessário corresponder.

Se ativar **UsePolicyBasedTrafficSelectors**, tem de garantir que o dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações dos prefixos de rede (gateway de rede local) no local de/para os prefixos de rede virtual do Azure, em vez de qualquer um para qualquer um. Por exemplo, se os prefixos de rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos de rede virtual são 192.168.0.0/16 e 172.16.0.0/16, tem de especificar os seletores de tráfego seguintes:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Consulte [Ligar dispositivos VPN baseados em várias políticas locais](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter mais detalhes sobre como usar essa opção.

<a id="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways" class="xliff"></a>

### A política personalizada substitui os conjuntos de políticas predefinidos de IPsec/IKE para gateways de VPN do Azure?
Sim, quando uma política personalizada é especificada numa ligação, o gateway de VPN do Azure só irá usar a política na ligação, como iniciador IKE e dispositivo de resposta IKE.

<a id="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected" class="xliff"></a>

### Se remover uma política de IPsec/IKE personalizada, a ligação torna-se desprotegida?
Não, a ligação ainda estará protegida pelo IPsec/IKE. Quando remove a política personalizada de uma ligação, o gateway de VPN do Azure reverterá para a [lista predefinida de propostas de IPsec/IKE](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) e reinicia o handshake IKE novamente com o dispositivo VPN local.

<a id="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection" class="xliff"></a>

### Adicionar ou atualizar uma política de IPsec/IKE pode atrapalhar a minha ligação VPN?
Sim, isso pode causar uma pequena interrupção (alguns segundos) como o gateway de VPN do Azure irá dividir a ligação existente e reiniciar o handshake IKE para restabelecer o túnel IPsec com os novos parâmetros e algoritmos criptográficos. Verifique se o dispositivo VPN local também é configurado com os algoritmos correspondentes e os principais pontos fortes para minimizar a interrupção.

<a id="can-i-use-different-policies-on-different-connections" class="xliff"></a>

### Posso usar políticas diferentes em ligações diferentes?
Sim. A política personalizada é aplicada consoante a ligação. Pode criar e aplicar diferentes políticas de IPsec/IKE em diferentes ligações. Também pode optar por aplicar políticas personalizadas num subconjunto de ligações. As restantes irão usar os conjuntos de políticas predefinidas de IPsec/IKE do Azure.

<a id="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well" class="xliff"></a>

### Também posso usar a política personalizada na ligação de VNet para VNet?
Sim, pode aplicar a política personalizada em ligações entre locais de IPsec ou em ligações VNet para VNet.

<a id="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources" class="xliff"></a>

### É necessário especificar a mesma política em ambos os recursos de ligação de VNet para VNet?
Sim. Um túnel de VNet para VNet consiste em dois recursos de ligação no Azure, uma para cada direção. Precisa de garantir que os recursos de ligação têm a mesma política, senão a ligação VNet para VNet não será estabelecida.

<a id="does-custom-ipsecike-policy-work-on-expressroute-connection" class="xliff"></a>

### A política de IPsec/IKE personalizada funciona na ligação do ExpressRoute?
Não. A política de IPsec/IKE só funciona em ligações VPN S2S e VNet para VNet por meio de gateways de VPN do Azure.

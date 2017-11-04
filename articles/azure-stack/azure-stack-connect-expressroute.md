---
title: Ligar a pilha do Azure para o Azure com o ExpressRoute
description: Como ligar redes virtuais na pilha do Azure para redes virtuais no Azure com o ExpressRoute.
services: azure-stack
documentationcenter: 
author: victorar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: aa6973939c6cfe0688f5781fdcea5d39670249df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Ligar a pilha do Azure para o Azure com o ExpressRoute

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Existem dois métodos suportados para ligar redes virtuais na pilha do Azure para redes virtuais no Azure:
   * **Site a Site**

     Uma ligação VPN através de IPsec (IKE v1 e IKE v2). Este tipo de ligação requer um dispositivo VPN ou RRAS. Para obter mais informações, consulte [ligar a pilha do Azure para o Azure através da VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     Uma ligação direta para o Azure da sua implementação de pilha do Azure. O ExpressRoute é **não** uma ligação VPN através da Internet pública. Para obter mais informações sobre o ExpressRoute do Azure, consulte [descrição geral do ExpressRoute](../expressroute/expressroute-introduction.md).

Este artigo mostra um exemplo com o ExpressRoute para ligar a pilha do Azure para o Azure.
## <a name="requirements"></a>Requisitos
Seguem-se requisitos específicos para ligar a pilha do Azure e o Azure com o ExpressRoute:
* Uma subscrição do Azure para criar um circuito do ExpressRoute e as VNets no Azure.
* A aprovisionado circuito de ExpressRoute através de um [fornecedor de conectividade](../expressroute/expressroute-locations.md).
* Um router que tenha o circuito de ExpressRoute ligado a portas sua WAN.
* O lado de LAN do router está ligado ao Gateway de multi-inquilino de pilha do Azure.
* O router têm de suportar ligações de VPN de Site a Site entre a interface de rede local e o Gateway de multi-inquilino de pilha do Azure.
* Se mais do que um inquilino for adicionado na sua implementação de pilha do Azure, o router tem de ser capaz de criar várias VRFs (de encaminhamento Virtual e de reencaminhamento).

O diagrama seguinte mostra uma vista conceptual de rede depois de concluir a configuração:

![Diagrama conceptual](media/azure-stack-connect-expressroute/Conceptual.png)

**Diagrama 1**

O diagrama de arquitetura seguinte mostra como vários inquilinos ligar a partir da infraestrutura de pilha do Azure através de ExpressRoute router para o Azure no limite Microsoft:

![Diagrama da arquitetura](media/azure-stack-connect-expressroute/Architecture.png)

**Diagrama de 2**

O exemplo apresentado neste artigo utiliza a mesma arquitetura para ligar ao Azure através do peering privado do ExpressRoute. É feito utilizando uma ligação de VPN de Site para Site do gateway de rede virtual na pilha do Azure para um router do ExpressRoute. Os seguintes passos neste artigo mostram como criar uma ligação ponto a ponto entre duas VNets de inquilinos diferentes dois na pilha do Azure para as respetivas VNets no Azure. Pode optar por adicionar como muitas VNets de inquilino e replicar os passos para cada inquilino ou utilizam este exemplo para implementar apenas um único inquilino VNet.

## <a name="configure-azure-stack"></a>Configurar a pilha do Azure
Agora, criar os recursos que tem de definir configurar o ambiente de pilha do Azure como um inquilino. Os passos seguintes mostram o que precisa de fazer. Estas instruções mostram como criar recursos através do portal do Azure pilha, mas também pode utilizar o PowerShell.

![Passos de recursos de rede](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Antes de começar
Antes de iniciar a configuração, tem de:
* Uma implementação de pilha do Azure.

   Para obter informações sobre a implementação do Kit de desenvolvimento de pilha do Azure, consulte [início rápido de implementação do Kit de desenvolvimento de pilha do Azure](azure-stack-deploy-overview.md).
* Uma oferta no Azure pilha que o utilizador pode subscrever.

  Para obter instruções, consulte [disponibilizar máquinas virtuais para os utilizadores do Azure pilha](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Criar recursos de rede na pilha do Azure

Utilize os procedimentos seguintes para criar recursos de rede necessários na pilha do Azure para cada inquilino:

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM
1. Inicie sessão no portal de utilizador com uma conta de utilizador (inquilino).

2. No portal, clique em **novo**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Selecione **Rede** no menu do Marketplace.

4. Clique em **Rede virtual** no menu.

5. Escreva os valores nos campos adequados utilizando a seguinte tabela:

   |Campo  |Valor  |
   |---------|---------|
   |Nome     |Tenant1VNet1         |
   |Espaço de endereços     |10.1.0.0/16|
   |Nome da sub-rede     |Tenant1 Sub1|
   |Intervalo de endereços da sub-rede     |10.1.1.0/24|

6. Deverá encontrar a Subscrição que criou anteriormente preenchida no campo **Subscrição**.

    a. Grupo de recursos, pode criar um grupo de recursos ou se já tiver uma, selecione **utilizar existente**.

    b. Verifique a localização predefinida.

    c. Clique em **afixar ao dashboard**.

    d. Clique em **Criar**.



#### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway
1. Abra o recurso de rede Virtual que criou (Tenant1VNet1) a partir do dashboard.
2. Na secção de definições, selecione **sub-redes**.
3. Clique em **Sub-rede de Gateway** para adicionar uma sub-rede de gateway à rede virtual.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. O nome da sub-rede está predefinido como **GatewaySubnet**.
   As sub-redes de gateway são especiais e têm de ter este nome específico para funcionarem corretamente.
5. No **intervalo de endereços** campo, certifique-se de que o endereço é **10.1.0.0/24**.
6. Clique em **OK** para criar a sub-rede do gateway.

#### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal de utilizador de pilha do Azure, clique em **novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Gateway de rede virtual** na lista de recursos de rede.
4. No campo **Nome**, escreva **GW1**.
5. Clique no item **Rede virtual** para escolher uma rede virtual.
   Selecione **Tenant1VNet1** da lista.
6. Clique no item de menu **Endereço IP público**. Quando o **escolher endereço IP público** secção abre clique **criar nova**.
7. No campo **Nome**, escreva **GW1-PiP** e clique em **OK**.
8. O **Tipo de VPN** deve ter a opção **Baseado na rota** selecionada por predefinição.
    Mantenha esta definição.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso para o Dashboard, se pretender. Clique em **Criar**.

#### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local

O objetivo do recurso de gateway de rede Local é indicar o gateway remoto no final da ligação VPN. Neste exemplo, lado remoto é o subinterface LAN do ExpressRoute router. Para o inquilino 1 neste exemplo, o endereço remoto é 10.60.3.255 conforme mostrado no diagrama 2.

1. Inicie sessão no computador físico do Azure Stack.
2. Inicie sessão no portal de utilizador com a sua conta de utilizador e clique em **novo**.
3. Selecione **Rede** no menu do Marketplace.
4. Selecione **Gateway de rede local** na lista de recursos.
5. No **nome** campo tipo **ER-Router-GW**.
6. Para o **endereço IP** campo, consulte a 2 de diagrama. O endereço IP do subinterface de LAN do router de ExpressRoute para o inquilino 1 é 10.60.3.255. Para o seu ambiente, escreva o endereço IP da interface do router de correspondente.
7. No **espaço de endereços** campo, escreva o espaço de endereços das VNets que pretende ligar no Azure. Neste exemplo, consulte o diagrama 2. Para o inquilino 1, tenha em atenção que as sub-redes necessárias são **192.168.2.0/24** (esta é a Vnet Hub no Azure) e **10.100.0.0/16** (esta é a VNet Spoke no Azure). Escreva as sub-redes correspondentes para o seu próprio ambiente.
   > [!IMPORTANT]
   > Este exemplo assume que está a utilizar rotas estáticas na ligação de VPN de Site a Site entre o gateway de pilha do Azure e o router do ExpressRoute.

8. Certifique-se de que o **subscrição**, **grupo de recursos**, e **localização** estão corretos e clique em **criar**.

#### <a name="create-the-connection"></a>Criar a ligação
1. No portal de utilizador de pilha do Azure, clique em **novo**.
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Ligação** na lista de recursos.
4. No **Noções básicas** secção de definições, escolha **Site a site (IPSec)** como o **tipo de ligação**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização** e clique em **OK**.
6. No **definições** secção, clique em **gateway de rede Virtual** clique **GW1**.
7. Clique em **gateway de rede Local**e clique em **ER Router GW**.
8. No **nome da ligação** , digite **ConnectToAzure**.
9. No **chave partilhada (PSK)** , digite **abc123** e clique em **OK**.
10. No **resumo** secção, clique em **OK**.

    Depois de criar a ligação, pode ver o endereço IP público utilizado pelo gateway de rede virtual. Para localizar o endereço no portal do Azure pilha, navegue para o gateway de rede Virtual. No **descrição geral**, localizar o **endereço IP público**. Tenha em atenção este endereço; irá utilizar como o *endereços IP internos* na secção seguinte (se aplicável para a sua implementação).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Para validar os dados estiverem em deslocação através da ligação VPN, terá de máquinas virtuais para enviar e receber dados na Vnet de pilha do Azure. Criar uma máquina virtual agora e coloque-o a sub-rede VM na sua rede virtual.

1. No portal de utilizador de pilha do Azure, clique em **novo**.
2. Selecione **Máquinas Virtuais** no menu do Marketplace.
3. Na lista de imagens da máquina virtual, selecione o **Windows Server 2016 Datacenter Eval** imagem e clique em **criar**.
4. No **Noções básicas** na secção de **nome** campo tipo **VM01**.
5. Escreva um nome de utilizador e uma palavra-passe válidos. Irá utilizar esta conta para iniciar sessão na VM depois de esta ser criada.
6. Forneça um **subscrição**, **grupo de recursos**, e **localização** e, em seguida, clique em **OK**.
7. No **tamanho** secção, clique num tamanho de máquina virtual para esta instância e, em seguida, clique em **selecione**.
8. No **definições** secção, pode aceitar as predefinições. Mas certifique-se de que a rede virtual selecionada está **Tenant1VNet1** e a sub-rede está definida como **10.1.1.0/24**. Clique em **OK**.
9. Reveja as definições no **resumo** secção e clique em **OK**.

Para cada inquilino VNet que pretende ligar, repita os passos anteriores do **criar a rede virtual e sub-rede VM** através de **criar uma máquina virtual** secções.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Configure a máquina virtual NAT para transversal de gateway
> [!IMPORTANT]
> Esta secção destina-se apenas a implementações do Kit de desenvolvimento de pilha do Azure. O NAT não é necessário para implementações de vários nós.

O Kit de desenvolvimento de pilha do Azure é autónomo e isolados da rede no qual está implementado anfitrião físico. Por isso, a rede de VIP "External" que os gateways estão ligados ao não seja externa, mas em vez disso, fique oculta atrás de um router de tradução de endereços de rede (NAT) a fazer.
 
O router é uma máquina virtual do Windows (**AzS BGPNAT01**) com a função de encaminhamento e dos serviços de acesso remoto (RRAS) na infraestrutura do Kit de desenvolvimento de pilha do Azure. Tem de configurar o NAT na máquina virtual AzS BGPNAT01 para permitir a ligação de VPN de Site a Site ligar em ambas as extremidades.

#### <a name="configure-the-nat"></a>Configure o NAT

1. Início de sessão para a máquina física de pilha do Azure com a sua conta de administrador.
2. Copie e edite o seguinte script do PowerShell e execute numa elevada ISE do Windows PowerShell. Substitua a palavra-passe de administrador. O endereço devolvido é sua *endereço externo BGPNAT*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. Para configurar o NAT, copiar e editar o seguinte script do PowerShell e execute numa elevada ISE do Windows PowerShell. Edite o script para substituir o *endereço externo BGPNAT* e *endereços IP internos* (que apontou anteriormente no **criar a ligação** secção).

   Nos diagramas de exemplo, o *endereço externo BGPNAT* é 10.10.0.62 e *endereços IP internos* é 192.168.102.1.

   ```
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Configurar o Azure
Agora que concluiu a configuração de pilha do Azure, pode implementar alguns recursos do Azure. O diagrama seguinte mostra um inquilino de exemplo rede virtual no Azure. Pode utilizar qualquer nome e o esquema de endereçamento para a sua VNet no Azure. No entanto, o intervalo de endereços das VNets no Azure e Azure pilha tem de ser exclusivos e não se podem sobrepor.

![Vnets do Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Diagrama 3**

Os recursos a que implementar no Azure são semelhantes aos recursos implementados na pilha do Azure. Da mesma forma, implementar:
* Redes virtuais e sub-redes
* Uma sub-rede de gateway
* Um gateway de rede virtual
* Uma ligação
* Um circuito do ExpressRoute

A infraestrutura de rede do Azure de exemplo está configurada da seguinte forma:
* Um hub standard (192.168.2.0/24) and spoke (10.100.0.0./16) VNet modelo é utilizado.
* As cargas de trabalho são implementadas no spoke Vnet e o circuito ExpressRoute está ligado à hub VNet.
* As duas VNets estão ligadas utilizando a funcionalidade da VNet peering.

### <a name="configure-vnets"></a>Configurar as Vnets
1. Inicie sessão no portal do Azure com as suas credenciais do Azure.
2. Crie a VNet com o espaço de endereços 192.168.2.0/24 do hub. Criar uma sub-rede com o intervalo de endereços 192.168.2.0/25 e adicionar uma sub-rede de gateway com o intervalo de endereços 192.168.2.128/27.
3. Criar o spoke intervalo de endereços VNet e sub-rede utilizando o 10.100.0.0/16.


Para obter mais informações sobre como criar redes virtuais no Azure, consulte [criar uma rede virtual com várias sub-redes](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

### <a name="configure-an-expressroute-circuit"></a>Configurar um circuito do ExpressRoute

1. Reveja os pré-requisitos do ExpressRoute na [ExpressRoute pré-requisitos e lista de verificação](../expressroute/expressroute-prerequisites.md).
2. Siga os passos no [criar e modificar um circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) para criar um circuito ExpressRoute com a sua subscrição do Azure.
3. Partilhe a chave do serviço do passo anterior com o seu fornecedor de alojamento fornecedor para aprovisionar o seu circuito de ExpressRoute no respetivo fim.
4. Siga os passos no [criar e modificar o peering de um circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) para configurar o peering privado no circuito do ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

* Siga os passos no [configurar um gateway de rede virtual para o ExpressRoute com o PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) para criar um gateway de rede virtual para o ExpressRoute na hub VNet.

### <a name="create-the-connection"></a>Criar a ligação

* Para ligar o circuito de ExpressRoute para o hub de VNet, siga os passos no [ligar uma rede virtual para um circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Elemento as Vnets

* Ponto do Hub e as VNets Spoke utilizando os passos no [criar um peering de rede virtual com o portal do Azure](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Quando configurar o VNet peering, certifique-se de que seleciona as seguintes opções:
   * Do hub para spoke: **permitir trânsito do gateway**
   * De spoke ao hub: **gateway remoto de utilização**

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

* Implemente as máquinas de virtuais de carga de trabalho para o spoke VNet.

Repita estes passos para nenhum inquilino adicional VNets que pretende ligar no Azure através do respetivos circuitos do ExpressRoute correspondentes.

## <a name="configure-the-router"></a>Configurar o router

Pode utilizar o seguinte diagrama de infraestrutura de ponto a ponto para a configuração do seu ExpressRoute Router. Este diagrama apresenta dois inquilinos (inquilino 1 e 2 do inquilino), com os seus respetivos circuitos do Expressroute. Cada está ligada à sua própria VRF (de encaminhamento Virtual e de reencaminhamento) no lado de LAN e WAN do router ExpressRoute assegure o isolamento de ponto a ponto entre os dois inquilinos. Tenha em atenção os endereços IP utilizados nas interfaces de router, siga a configuração de exemplo.

![Fim para o final do diagrama](media/azure-stack-connect-expressroute/EndToEnd.png)

**Diagrama de 4**

Pode utilizar qualquer router que suporta IKEv2 VPN e BGP para terminar a ligação de VPN de Site para Site de pilha do Azure. O router do mesmo é utilizado para ligar ao Azure através de um circuito ExpressRoute. 

Eis uma configuração de exemplo de um 1000 de ASR Cisco que suporta a infraestrutura de rede mostrada no diagrama 4:

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Testar a ligação

Teste a ligação depois de estabelecer a ligação de Site a Site e o circuito ExpressRoute. Esta tarefa é simple.  Inicie sessão para uma das máquinas virtuais que criou no seu Azure VNet e enviar um ping a máquina virtual criada no ambiente de pilha do Azure, ou vice versa. 

Para garantir que está a enviar o tráfego através do Site a Site e as ligações do ExpressRoute, deve executar um ping do endereço IP (DIP) dedicado da máquina virtual em ambos os ends e não o endereço VIP da máquina virtual. Por isso, tem de localizar e tenha em atenção o endereço na outra extremidade da ligação.

### <a name="allow-icmp-in-through-the-firewall"></a>Permitir o protocolo ICMP em através da firewall
Por predefinição, o Windows Server 2016 não permite a pacotes ICMP em através da firewall. Por isso, para cada máquina virtual que utilizar no teste, execute o seguinte cmdlet numa janela elevada do PowerShell:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Enviar um ping a máquina virtual de pilha do Azure

1. Inicie sessão no portal de utilizador de pilha do Azure com uma conta de inquilino.
2. Clique em **Máquinas Virtuais** na barra de navegação esquerda.
3. Localizar a máquina virtual que criou anteriormente e clique no mesmo.
4. Na secção para a máquina virtual, clique em **Connect**.
5. Abra um PowerShell elevado janela e escreva **ipconfig/all**.
6. Localizar o endereço IPv4 no resultado e anote-lo. Ping este endereço da máquina virtual na VNet do Azure. No ambiente de exemplo, o endereço é da sub-rede 10.1.1.x/24. No seu ambiente, o endereço poderão ser diferente. No entanto, deve estar na sub-rede que criou para a sub-rede de inquilino VNet.


### <a name="view-data-transfer-statistics"></a>Estatísticas de transferência de dados de vista

Se pretender saber quanto tráfego é a transmissão através da ligação, pode encontrar estas informações na secção de ligação no portal de utilizador de pilha do Azure. Estas informações são também outra boa forma de verificar que o ping que acabámos de enviar, na verdade, ocorreu através de ligações VPN e ExpressRoute.

1. Inicie sessão no portal de utilizador de pilha do Microsoft Azure com a sua conta de inquilino.
2. Navegue para o grupo de recursos onde foi criado o Gateway de VPN e selecione o **ligações** tipo de objeto.
3. Clique em de **ConnectToAzure** ligação na lista.
4. No **ligação** secção, pode ver as estatísticas de **dados** e **a dados extra**. Deverá ver alguns valores diferentes de zero aí.

   ![Dados em dados de saída](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Passos seguintes
[Implementar aplicações do Azure e Azure pilha](azure-stack-solution-pipeline.md)
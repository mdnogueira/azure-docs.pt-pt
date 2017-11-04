---
title: "Criar uma ligação de VPN de site a site entre duas redes virtuais em diferentes ambientes do Kit de desenvolvimento de pilha do Azure | Microsoft Docs"
description: "Procedimento passo a passo que um administrador da nuvem utiliza para criar uma ligação de VPN de site a site entre dois ambientes do Kit de desenvolvimento de pilha do Azure de nó único."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: scottnap
ms.openlocfilehash: fa2a940620e06521fa110fa13dcbc3050635a502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Criar uma ligação de VPN de site a site entre duas redes virtuais em diferentes ambientes do Kit de desenvolvimento de pilha do Azure
## <a name="overview"></a>Descrição geral
Este artigo mostra como criar uma ligação de VPN de site a site entre duas redes virtuais em dois ambientes separados do Kit de desenvolvimento de pilha do Azure. Ao configurar as ligações, saiba como funcionam os gateways de VPN na pilha do Azure.

### <a name="connection-diagram"></a>Diagrama de ligação
O diagrama seguinte mostra que a configuração da ligação deve ter o seguinte aspeto quando tiver terminado.

![Configuração da ligação VPN site a site](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Antes de começar
Para concluir a configuração da ligação, certifique-se de que tem os seguintes itens antes de começar:

* Dois servidores que cumpram os requisitos de hardware do Kit de desenvolvimento de pilha do Azure, que são definidos pelo [os pré-requisitos de implementação do Azure pilha](azure-stack-deploy.md). Certifique-se de que os pré-requisitos que são apresentadas as [artigo](azure-stack-deploy.md) são demasiado cumprido.
* O [Kit de desenvolvimento de pilha do Azure](https://azure.microsoft.com/en-us/overview/azure-stack/try/) pacote de implementação.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Implementar os ambientes do Kit de desenvolvimento de pilha do Azure
Para concluir a configuração da ligação, tem de implementar dois ambientes do Kit de desenvolvimento de pilha do Azure.
> [!NOTE] 
> Para cada Kit de desenvolvimento de pilha do Azure implementa, siga o [instruções de implementação](azure-stack-run-powershell-script.md). Neste artigo, os ambientes do Kit de desenvolvimento de pilha do Azure são denominados *POC1* e *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Preparar uma oferta no POC1 e POC2
No POC1 e POC2, prepare uma oferta para que um utilizador pode subscrever a oferta e implementar as máquinas virtuais. Para obter informações sobre como criar uma oferta, consulte [disponibilizar máquinas virtuais para os utilizadores do Azure pilha](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Reveja e conclua a tabela de configuração de rede
A tabela seguinte resume a configuração de rede para ambos os ambientes do Kit de desenvolvimento de pilha do Azure. Utilize o procedimento que aparece depois da tabela para adicionar o endereço de BGPNAT externa que é específico para a sua rede.

**Tabela de configuração de rede**
|   |POC1|POC2|
|---------|---------|---------|
|Nome da rede virtual     |VNET-01|VNET-02 |
|Espaço de endereços de rede virtual |10.0.10.0/23|10.0.20.0/23|
|Nome da sub-rede     |Sub-rede-01|Sub-rede-02|
|Intervalo de endereços da sub-rede|10.0.10.0/24 |10.0.20.0/24 |
|Sub-rede de gateway     |10.0.11.0/24|10.0.21.0/24|
|Endereço BGPNAT externo     |         |         |

> [!NOTE]
> Os endereços BGPNAT IP externos no ambiente de exemplo são 10.16.167.195 para POC1 e 10.16.169.131 para POC2. Utilize o procedimento seguinte para determinar os endereços BGPNAT IP externos para os anfitriões do Kit de desenvolvimento de pilha do Azure e, em seguida, adicioná-los para a tabela de configuração de rede anterior.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obter o endereço IP do adaptador externo da VM NAT
1. Iniciar sessão para a máquina física de pilha do Azure para POC1.
2. Editar o código do Powershell seguinte para substituir a palavra-passe de administrador e, em seguida, execute o código no anfitrião de POC:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. Adicione o endereço IP para a tabela de configuração de rede que é apresentada na secção anterior.

4. Repita este procedimento em POC2.

## <a name="create-the-network-resources-in-poc1"></a>Criar os recursos de rede no POC1
Agora, criar os recursos de rede de POC1 que tem de definir dos seus gateways. As instruções seguintes mostram como criar os recursos através do portal de utilizador. Também pode utilizar o código do PowerShell para criar os recursos.

![Fluxo de trabalho que é utilizado para criar recursos](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Inicie sessão como um inquilino
Um administrador de serviço pode iniciar sessão como um inquilino para testar a planos, ofertas e subscrições que poderão utilizar os seus inquilinos. Se ainda não tiver um, [criar uma conta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sessão.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM
1. Utilize uma conta de inquilino para iniciar sessão no portal de utilizador.
2. No portal de utilizador, selecione **novo**.

    ![Criar nova rede virtual](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Aceda a **Marketplace**e, em seguida, selecione **redes**.
4. Selecione **rede Virtual**.
5. Para **nome**, **espaço de endereços**, **nome de sub-rede**, e **intervalo de endereços da sub-rede**, utilize os valores que são apresentados anteriormente na rede tabela de configuração.
6. No **subscrição**, é apresentada a subscrição que criou anteriormente.
7. Para **grupo de recursos**, pode criar um grupo de recursos ou se já tiver uma, selecione **utilizar existente**.
8. Verifique a localização predefinida.
9. Selecione **Afixar ao dashboard**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway
1. No dashboard, abra o recurso de rede virtual de VNET-01 que criou anteriormente.
2. No painel **Definições**, selecione **Sub-redes**.
3. Para adicionar uma sub-rede de gateway para a rede virtual, selecione **sub-rede do Gateway**.
   
    ![Adicionar sub-rede do gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Por predefinição, o nome de sub-rede está definido como **GatewaySubnet**.
   As sub-redes de gateway são especiais. Para funcionar corretamente, terão de utilizar o *GatewaySubnet* nome.
5. No **intervalo de endereços**, certifique-se de que o endereço está **10.0.11.0/24**.
6. Selecione **OK** para criar a sub-rede do gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **novo**. 
2. Aceda a **Marketplace**e, em seguida, selecione **redes**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. No **nome**, introduza **GW1**.
5. Selecione o **rede Virtual** item para escolher uma rede virtual.
   Selecione **VNET-01** da lista.
6. Selecione o **endereço IP público** item de menu. Quando o **escolher endereço IP público** painel se abre, selecione **criar nova**.
7. No **nome**, introduza **GW1 PiP**e, em seguida, selecione **OK**.
8.  Por predefinição, para **tipo de VPN**, **baseado na rota** está selecionada.
    Manter o **baseado na rota** tipo de VPN.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao dashboard. Selecione **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local
A implementação de um *gateway de rede local* nesta implementação de avaliação do Azure Stack é um pouco diferente quando comparada com uma implementação do Azure real.

Numa implementação do Azure, um gateway de rede local representa um dispositivo físico no local (inquilino), que utiliza para ligar a um gateway de rede virtual no Azure. Nesta implementação de avaliação de pilha do Azure, ambas as extremidades da ligação são gateways de rede virtual!

Uma forma de pensar nesta mais genericamente é que o recurso de gateway de rede local indica sempre o gateway remoto no final da ligação. Devido à forma como o Kit de desenvolvimento de pilha do Azure foi concebido, tem de fornecer o endereço IP do adaptador de rede externo na tradução de endereços de rede (NAT) VM do outros Azure pilha Development Kit como o endereço IP público do gateway de rede local. Em seguida, criar mapeamentos NAT na VM do NAT para se certificar de que ambas as extremidades estão ligadas corretamente.


### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local
1. Iniciar sessão para a máquina física de pilha do Azure para POC1.
2. No portal de utilizador, selecione **novo**.
3. Aceda a **Marketplace**e, em seguida, selecione **redes**.
4. Na lista de recursos, selecione **gateway de rede local**.
5. No **nome**, introduza **POC2-GW**.
6. No **endereço IP**, introduza o endereço externo BGPNAT para POC2. Este endereço é apresentado anteriormente na tabela de configuração de rede.
7. No **espaço de endereços**, para o espaço de endereços da VNET POC2 que criar mais tarde, introduza **10.0.20.0/23**.
8. Certifique-se de que o **subscrição**, **grupo de recursos**, e **localização** estão corretos e, em seguida, selecione **criar**.

### <a name="create-the-connection"></a>Criar a ligação
1. No portal de utilizador, selecione **novo**.
2. Aceda a **Marketplace**e, em seguida, selecione **redes**.
3. Na lista de recursos, selecione **ligação**.
4. No **Noções básicas** painel Definições, para o **tipo de ligação**, selecione **Site a site (IPSec)**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
6. No **definições** painel, selecione **gateway de rede Virtual**e, em seguida, selecione **GW1**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **POC2-GW**.
8. No **nome da ligação**, introduza **POC1 POC2**.
9. No **chave partilhada (PSK)**, introduza **12345**e, em seguida, selecione **OK**.
10. No **resumo** painel, selecione **OK**.

### <a name="create-a-vm"></a>Criar uma VM
Para validar os dados que circulam através da ligação VPN, terá das máquinas virtuais para enviar e receber dados em cada Kit de desenvolvimento de pilha do Azure. Criar uma máquina virtual no POC1 agora e, em seguida, na sua rede virtual, colocá-la na sua sub-rede VM.

1. No portal do Azure, selecione **novo**.
2. Aceda a **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens da máquina virtual, selecione o **Windows Server 2016 Datacenter Eval** imagem.
4. No **Noções básicas** painel, na **nome**, introduza **VM01**.
5. Introduza um nome de utilizador válido e palavra-passe. Pode utilizar esta conta para iniciar sessão para a VM, depois de criado.
6. Forneça um **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
7. No **tamanho** painel, para esta instância, selecione um tamanho de máquina virtual e, em seguida, selecione **selecione**.
8. No **definições** painel, aceite as predefinições. Certifique-se de que o **VNET-01** rede virtual está selecionada. Certifique-se de que a sub-rede está definida como **10.0.10.0/24**. Em seguida, selecione **OK**.
9. No **resumo** painel, reveja as definições e, em seguida, selecione **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Criar os recursos de rede no POC2

O passo seguinte consiste em criar os recursos de rede para POC2. As instruções seguintes mostram como criar os recursos através do portal de utilizador.

### <a name="sign-in-as-a-tenant"></a>Inicie sessão como um inquilino
Um administrador de serviço pode iniciar sessão como um inquilino para testar a planos, ofertas e subscrições que poderão utilizar os seus inquilinos. Se ainda não tiver um, [criar uma conta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sessão.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM

1. Inicie sessão com uma conta de inquilino.
2. No portal de utilizador, selecione **novo**.
3. Aceda a **Marketplace**e, em seguida, selecione **redes**.
4. Selecione **rede Virtual**.
5. Utilize as informações anteriormente na tabela de configuração de rede para identificar os valores para o POC2 **nome**, **espaço de endereços**, **nome de sub-rede**e **Intervalo de endereços da sub-rede**.
6. No **subscrição**, é apresentada a subscrição que criou anteriormente.
7. Para **grupo de recursos**, crie um novo grupo de recursos ou, se já tiver uma, selecione **utilizar existente**.
8. Certifique-se a predefinição **localização**.
9. Selecione **Afixar ao dashboard**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abra o recurso de rede Virtual que criou (**VNET-02**) partir do dashboard.
2. No painel **Definições**, selecione **Sub-redes**.
3. Selecione **sub-rede do Gateway** para adicionar uma sub-rede de gateway para a rede virtual.
4. O nome da sub-rede está predefinido como **GatewaySubnet**.
   As sub-redes de gateway são especiais e têm de ter este nome específico para funcionarem corretamente.
5. No **intervalo de endereços** campo, certifique-se de que o endereço é **10.0.21.0/24**.
6. Selecione **OK** para criar a sub-rede do gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **novo**.  
2. Aceda a **Marketplace**e, em seguida, selecione **redes**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. No **nome**, introduza **GW2**.
5. Para escolher uma rede virtual, selecione **rede Virtual**. Em seguida, selecione **VNET-02** da lista.
6. Selecione **endereço IP público**. Quando o **escolher endereço IP público** painel se abre, selecione **criar nova**.
7. No **nome**, introduza **GW2 PiP**e, em seguida, selecione **OK**.
8. Por predefinição, para **tipo de VPN**, **baseado na rota** está selecionada.
    Manter o **baseado na rota** tipo de VPN.
9. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao dashboard. Selecione **Criar**.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. No portal de utilizador POC2, selecione **novo**. 
4. Aceda a **Marketplace**e, em seguida, selecione **redes**.
5. Na lista de recursos, selecione **gateway de rede Local**.
6. No **nome**, introduza **POC1-GW**.
7. No **endereço IP**, introduza o endereço externo BGPNAT para POC1 listados anteriormente a tabela de configuração de rede.
8. No **espaço de endereços**, das POC1, introduza o **10.0.10.0/23** espaço de endereços da **VNET-01**.
9. Certifique-se de que o **subscrição**, **grupo de recursos**, e **localização** estão corretos e, em seguida, selecione **criar**.

## <a name="create-the-connection"></a>Criar a ligação
1. No portal de utilizador, selecione **novo**. 
2. Aceda a **Marketplace**e, em seguida, selecione **redes**.
3. Na lista de recursos, selecione **ligação**.
4. No **básico** painel Definições, para o **tipo de ligação**, escolha **Site a site (IPSec)**.
5. Selecione o **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
6. No **definições** painel, selecione **gateway de rede Virtual**e, em seguida, selecione **GW2**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **POC1-GW**.
8. No **nome da ligação**, introduza **POC2 POC1**.
9. No **chave partilhada (PSK)**, introduza **12345**. Se escolher um valor diferente, lembre-se de que as TI *tem* corresponde ao valor para a chave partilhada que criou no POC1. Selecione **OK**.
10. Reveja o **resumo** painel e, em seguida, selecione **OK**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Criar uma máquina virtual no POC2 agora e colocá-la na sua sub-rede VM na sua rede virtual.

1. No portal do Azure, selecione **novo**.
2. Aceda a **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens da máquina virtual, selecione o **Windows Server 2016 Datacenter Eval** imagem.
4. No **Noções básicas** painel, para **nome**, introduza **VM02**.
5. Introduza um nome de utilizador válido e palavra-passe. Pode utilizar esta conta para iniciar sessão para a máquina virtual depois de criado.
6. Forneça um **subscrição**, **grupo de recursos**, e **localização**e, em seguida, selecione **OK**.
7. No **tamanho** painel, selecione uma máquina virtual de tamanho para esta instância e, em seguida, selecione **selecione**.
8. No **definições** painel, pode aceitar as predefinições. Certifique-se de que o **VNET-02** rede virtual está selecionada e certifique-se de que a sub-rede está definida como **10.0.20.0/24**. Selecione **OK**.
9. Reveja as definições no **resumo** painel e, em seguida, selecione **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Configurar a máquina virtual NAT no cada Kit de desenvolvimento de pilha do Azure para transversal de gateway
Porque o Kit de desenvolvimento de pilha do Azure é autónomo e isolados da rede no qual está implementado anfitrião físico, o *externo* rede VIP que os gateways estão ligados ao não é, na verdade, externo. Em vez disso, a rede de VIP fique oculta atrás de um router que efetua a tradução de endereços de rede. 

O router é uma máquina virtual do Windows Server, denominada *AzS bgpnat01*, que executa a função de encaminhamento e dos serviços de acesso remoto (RRAS) na infraestrutura do Kit de desenvolvimento de pilha do Azure. Tem de configurar o NAT na máquina virtual AzS bgpnat01 para permitir a ligação de VPN de site a site ligar em ambas as extremidades. 

Para configurar a ligação VPN, terá de criar uma rota estática de mapa NAT que mapeia da interface externa na máquina virtual BGPNAT para o VIP do conjunto de Gateway do limite. Uma rota de mapa NAT estática é necessária para cada porta numa ligação VPN.

> [!NOTE]
> Esta configuração é necessária para ambientes do Kit de desenvolvimento de pilha do Azure apenas.
> 
> 

### <a name="configure-the-nat"></a>Configure o NAT
> [!IMPORTANT]
> Tem de concluir este procedimento para *ambos* ambientes do Kit de desenvolvimento de pilha do Azure.

1. Determinar o **endereços IP internos** para utilizar o seguinte script do PowerShell. Abra o gateway de rede virtual (GW1 e GW2) e, em seguida, no **descrição geral** painel, guarde o valor para o **endereço IP público** para utilização posterior.
![Endereço IP](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Iniciar sessão para a máquina física de pilha do Azure para POC1.
3. Copiar e editar o seguinte script do PowerShell. Para configurar o NAT em cada Kit de desenvolvimento de pilha do Azure, execute o script numa elevada ISE do Windows PowerShell. O script, adicione valores ao *endereço externo BGPNAT* e *endereços IP internos* marcadores de posição:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
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
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Repita este procedimento em POC2.

## <a name="test-the-connection"></a>Testar a ligação
Agora que é estabelecida a ligação de site a site, deve validar que pode obter o tráfego que flui através do mesmo. Para validar, iniciar sessão para uma das máquinas virtuais que criou no ambiente ou Kit de desenvolvimento de pilha do Azure. Em seguida, executar um ping a máquina virtual que criou no outro ambiente. 

Para garantir que enviar o tráfego através da ligação site a site, certifique-se de que se enviar pings para o endereço IP direto (DIP) da máquina virtual na sub-rede remota, não o VIP. Para tal, localize o endereço na outra extremidade da ligação. Guarde o endereço para utilização posterior.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Inicie sessão no inquilino do VM no POC1
1. Inicie sessão para a máquina física de pilha do Azure para POC1 e, em seguida, utilizar uma conta de inquilino para iniciar sessão no portal de utilizador.
2. Na barra de navegação esquerdo, selecione **computação**.
3. Na lista de VMs, localizar **VM01** que criou anteriormente e, em seguida, selecioná-lo.
4. No painel da máquina virtual, clique em **Connect**e, em seguida, abra o ficheiro de VM01.rdp.
   
     ![Botão de ligar](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Inicie sessão com a conta que configurou quando criou a máquina virtual.
6. Abra uma **do Windows PowerShell** janela.
7. Introduza **ipconfig/all**.
8. O resultado, localize o **endereço IPv4**e, em seguida, guarde o endereço para utilização posterior. Este é o endereço que vai enviar um ping de POC2. No ambiente de exemplo, o endereço é **10.0.10.4**, mas no seu ambiente poderá ser diferente. Este deve coincidir com o **10.0.10.0/24** sub-rede que criou anteriormente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Inicie sessão no inquilino do VM no POC2
1. Inicie sessão para a máquina física de pilha do Azure para POC2 e, em seguida, utilizar uma conta de inquilino para iniciar sessão no portal de utilizador.
2. Na barra de navegação esquerdo, clique em **computação**.
3. Na lista de máquinas virtuais, localizar **VM02** que criou anteriormente e, em seguida, selecioná-lo.
4. No painel da máquina virtual, clique em **Ligar**.
5. Inicie sessão com a conta que configurou quando criou a máquina virtual.
6. Abra uma **do Windows PowerShell** janela.
7. Introduza **ipconfig/all**.
8. Deverá ver um endereço de IPv4 que se encontrem dentro **10.0.20.0/24**. No ambiente de exemplo, o endereço é **10.0.20.4**, mas o seu endereço de poderão ser diferente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Da máquina virtual no POC2, executar um ping a máquina virtual no POC1, através do túnel. Para tal, ping que registou de VM01 DIP.
   No ambiente de exemplo, isto é **10.0.10.4**, mas certifique-se enviar um ping o endereço que anotou no laboratório. Deverá ver um resultado de que o seguinte aspeto:
   
    ![Ping enviado com êxito](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Uma resposta da máquina virtual remota indica um teste com êxito! Pode fechar a janela de máquina virtual. Para testar a ligação, pode tentar outros tipos de transferências de dados, como uma cópia de ficheiros.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Ver estatísticas de transferência de dados através da ligação de gateway
Se pretender saber a quantidade de dados atravessa a ligação de site a site, esta informação está disponível na **ligação** painel. Este teste também é outra forma de verificar que o ping que acabámos de enviar, na verdade, Ocorreu um erro através da ligação VPN.

1. Enquanto tem sessão iniciada para a máquina virtual de inquilino no POC2, utilize a sua conta de inquilino para iniciar sessão no portal de utilizador.
2. Aceda a **todos os recursos**e, em seguida, selecione o **POC2 POC1** ligação. **Ligações** aparece.
4. No **ligação** painel, as estatísticas de **dados** e **a dados extra** aparecer. Na captura de ecrã seguinte, os números de grandes dimensões são atribuída ao utilizador a transferência de ficheiro adicionais. Deverá ver alguns valores diferentes de zero não existe.
   
    ![Dados de entrada e saída](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)

---
title: "Criar uma ligação de Rede de VPNs entre duas Redes Virtuais em Ambientes de PoC do Azure Stack diferentes | Documentos da Microsoft"
description: "Procedimento passo a passo que permite a um administrador da cloud criar uma ligação de Rede de VPNs entre dois ambientes de POC de um nó no TP2."
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
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>Criar uma ligação de Rede de VPNs entre duas Redes Virtuais em Ambientes de PoC do Azure Stack diferentes
## <a name="overview"></a>Descrição geral
Este artigo mostra como criar uma Ligação de Rede de VPNs entre duas redes virtuais em dois ambientes de Prova de Conceito (POC) do Azure Stack separados. Ao mesmo tempo que configura as ligações, ficará a saber como funcionam os gateways de VPN no Azure Stack.

> [!NOTE]
> Este documento aplica-se especificamente à POC do Azure Stack TP2.
> 
> 

### <a name="connection-diagram"></a>Diagrama de ligação
O diagrama seguinte mostra o aspeto que a configuração deve apresentar quando tiver terminado:

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>Antes de começar
Para concluir esta configuração, certifique-se de que tem os seguintes itens antes de começar:

* Dois Servidores que cumprem os requisitos de hardware da POC do Azure Stack definidos pelo documento [Pré-requisitos de Implementação do Azure Stack](azure-stack-deploy.md), bem como os restantes pré-requisitos definidos nesse documento.
* O Pacote de Implementação do Azure Stack Technical Preview 2.

## <a name="deploy-the-poc-environments"></a>Implementar os ambientes de POC
Irá implementar dois ambientes de POC do Azure Stack para concluir esta configuração.

* Para cada POC que implementar, pode seguir as instruções de implementação detalhadas no artigo [Implementar a POC do Azure Stack](azure-stack-run-powershell-script.md).
  Cada ambiente de POC apresentado neste documento é designado genericamente por *POC1* e *POC2*.

## <a name="configure-quotas-for-compute-network-and-storage"></a>Configurar Quotas para Computação, Rede e Armazenamento
Em primeiro lugar, precisa de configurar *Quotas* para computação, rede e armazenamento. Estes serviços podem ser associados a um *Plano* e, em seguida, a uma *Oferta* que os inquilinos podem subscrever.

> [!NOTE]
> Precisa de realizar estes passos para cada ambiente de POC do Azure Stack.
> 
> 

A criação de Quotas para Serviços sofreu alterações desde o TP1. Os passos para criar quotas no TP2 podem ser consultados em <http://aka.ms/mas-create-quotas>. Pode aceitar as predefinições para todas as definições de quota para este exercício.

## <a name="create-a-plan-and-offer"></a>Criar um Plano e uma Oferta
Os [Planos](azure-stack-key-features.md) consistem em agrupamentos de um ou mais serviços. Enquanto fornecedor, pode criar planos para oferecer aos seus inquilinos. Por sua vez, os seus inquilinos subscrevem as suas ofertas para utilizar os planos e os serviços incluídos.

> [!NOTE]
> Precisa de realizar estes passos para cada ambiente de POC do Azure Stack.
> 
> 

1. Deve começar por criar um Plano. Para tal, pode seguir os passos especificados no artigo online [Criar um Plano](azure-stack-create-plan.md).
2. Crie uma Oferta seguindo os passos descritos em [Criar uma Oferta no Azure Stack](azure-stack-create-offer.md).
3. Inicie sessão no portal como um administrador de inquilinos e [subscreva a Oferta que criou](azure-stack-subscribe-plan-provision-vm.md).

## <a name="create-the-network-resources-in-poc-1"></a>Criar os Recursos de Rede na POC 1
Agora é chegado o momento de criar os recursos de que precisa para definir a sua configuração. Os passos seguintes ilustram o que irá fazer. Estas instruções mostram como criar os recursos através do portal, mas pode utilizar o PowerShell para alcançar o mesmo efeito.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>Iniciar sessão como um inquilino
Um administrador de serviços pode iniciar sessão como um inquilino para testar os planos, as ofertas e as subscrições que os seus inquilinos poderão utilizar. Se ainda não tiver uma conta, deverá [Criar uma conta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sessão.

### <a name="create-the-virtual-network--vm-subnet"></a>Criar a rede virtual e a sub-rede VM
1. Inicie sessão com uma conta de inquilino.
2. No portal do Azure, clique em **Novo**.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. Selecione **Rede** no menu do Marketplace.
4. Clique no item **Rede virtual** no menu.
5. Clique em **Criar** perto da parte inferior do painel de descrição do recurso. Introduza os seguintes valores nos campos adequados, de acordo com esta tabela.
   
   | **Campo** | **Valor** |
   | --- | --- |
   | Nome |vnet-01 |
   | Espaço de endereços |10.0.10.0/23 |
   | Nome da sub-rede |subnet-01 |
   | Intervalo de endereços da sub-rede |10.0.10.0/24 |
6. Deverá encontrar a Subscrição que criou anteriormente preenchida no campo **Subscrição**.
7. Em relação ao Grupo de Recursos, pode criar um novo Grupo de Recursos ou, se já tiver um, selecione **Utilizar existente**.
8. Verifique a localização predefinida.
9. Clique em **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abra o recurso de Rede Virtual que acabou de criar (Vnet-01) a partir do dashboard.
2. No painel Definições, selecione **Sub-redes**.
3. Clique em **Sub-rede de Gateway** para adicionar uma sub-rede de gateway à rede virtual.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. O nome da sub-rede está predefinido como **GatewaySubnet**.
   As sub-redes de gateway são especiais e têm de ter este nome específico para funcionarem corretamente.
5. No campo **Intervalo de endereços**, escreva **10.0.11.0/24**.
6. Clique em **Criar** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o Gateway de Rede Virtual
1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Gateway de rede virtual** na lista de recursos de rede.
4. Reveja a descrição e clique em **Criar**.
5. No campo **Nome**, escreva **GW1**.
6. Clique no item **Rede virtual** para escolher uma rede virtual.
   Selecione **Vnet-01** na lista.
7. Clique no item de menu **Endereço IP público**. Quando o painel **Escolher endereço IP público** for aberto, clique em **Criar novo**.
8. No campo **Nome**, escreva **GW1-PiP** e clique em **OK**.
9. O **Tipo de gateway** deve ter a opção **VPN** selecionada por predefinição. Mantenha esta definição.
10. O **Tipo de VPN** deve ter a opção **Baseado na rota** selecionada por predefinição.
    Mantenha esta definição.
11. Certifique-se de que a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao Dashboard se assim o desejar. Clique em **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o Gateway de Rede Local
A implementação de um *gateway de rede local* nesta implementação de avaliação do Azure Stack é um pouco diferente quando comparada com uma implementação do Azure real.

Tal como no Azure, existe o conceito de um gateway de rede local. No entanto, numa implementação do Azure, um gateway de rede local representa um dispositivo físico no local (no inquilino) que é utilizado para ligar a um gateway de rede virtual no Azure. No entanto, nesta implementação de avaliação do Azure Stack, ambas as extremidades da ligação são gateways de rede virtual!

Para ter uma perspetiva mais generalizada desta situação, pense no recurso de Gateway de Rede Local como uma maneira de indicar sempre o gateway remoto na outra extremidade da ligação. Devido à forma como a POC foi concebida, tem de fornecer o endereço IP do adaptador de rede externo na VM NAT da outra POC como o Endereço IP Público do Gateway de Rede Local. Em seguida, irá criar mapeamentos NAT na VM NAT para se certificar de que ambas as extremidades estão ligadas corretamente.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obter o endereço IP do adaptador externo da VM NAT
1. Inicie sessão no computador físico do Azure Stack para a POC2.
2. Prima a [Tecla Windows] + R para abrir o menu **Executar**, escreva **mstsc** e prima Enter.
3. No campo **Computador**, escreva o nome **MAS-BGPNAT01** e clique em  **Ligar**.
4. Clique no Menu Iniciar, clique com o botão direito do rato em **Windows PowerShell** e selecione **Executar como Administrador**.
5. Escreva **ipconfig /all**.
6. Localize o Adaptador Ethernet que está ligado à sua rede local e tome nota do endereço IPv4 vinculado a esse adaptador. No ambiente de exemplo, o endereço é **10.16.167.195**, mas o seu será algo diferente.
7. Registe esse endereço. É este que irá utilizar como Endereço IP Público do recurso de Gateway de Rede Local criado na POC1.

### <a name="create-the-local-network-gateway-resource"></a>Criar o Recurso de Gateway de Rede Local
1. Inicie sessão no computador físico do Azure Stack para a POC1.
2. No campo **Computador**, escreva o nome **MAS-CON01** e clique em **Ligar**.
3. No portal do Azure, clique em **Novo**.
   
4. Selecione **Rede** no menu do Marketplace.
5. Selecione **Gateway de rede local** na lista de recursos.
6. No campo **Nome**, escreva **POC2-GW**.
7. Ainda não sabe qual é o endereço IP do outro gateway, mas não há problema porque pode voltar atrás e alterá-lo mais tarde. Por agora, escreva **10.16.167.195** no campo **Endereço IP**.
8. No campo **Espaço de Endereços**, escreva o espaço de endereços da Vnet que irá criar na POC2. Este será **10.0.20.0/23**, por isso, escreva esse valor.
9. Certifique-se de que a **Subscrição**, o **Grupo de Recursos** e a **Localização** estão corretos e clique em **Criar**.

### <a name="create-the-connection"></a>Criar a Ligação
1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Ligação** na lista de recursos.
4. No painel de definições **Básico**, escolha **Site a site (IPSec)** como o **Tipo de ligação**.
5. Selecione a **Subscrição**, o **Grupo de Recursos** e a **Localização**, e clique em **OK**.
6. No painel **Definições**, escolha o **Gateway de Rede Virtual** (**GW1**) que criou anteriormente.
7. Escolha o **Gateway de Rede Local** (**POC2-GW**) que criou anteriormente.
8. No campo **Nome da Ligação**, escreva **POC1-POC2**.
9. No campo **Chave Partilhada (PSK)**, escreva **12345** e clique em **OK**.

### <a name="create-a-vm"></a>Criar uma VM
Para validar os dados em trânsito na Ligação VPN, precisa que as VMs enviem e recebam dados em cada POC. Crie uma VM na POC1 agora e coloque-a na sub-rede VM da sua rede virtual.

1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Máquinas Virtuais** no menu do Marketplace.
3. Na lista de imagens de máquinas virtuais, selecione a imagem **Windows Server 2012 R2 Datacenter**.
4. No painel **Básico**, no campo **Nome**, escreva **VM01**.
5. Escreva um nome de utilizador e uma palavra-passe válidos. Irá utilizar esta conta para iniciar sessão na VM depois de esta ser criada.
6. Forneça uma **Subscrição**, um **Grupo de Recursos** e uma **Localização** e, em seguida, clique em **OK**.
7. No painel **Tamanho**, escolha um tamanho de VM para esta instância e, em seguida, clique em **Selecionar**.
8. No painel **Definições**, pode aceitar as predefinições; certifique-se apenas de que a Rede virtual selecionada é a **VNET-01** e que a Sub-rede está definida como **10.0.10.0/24**. Clique em **OK**.
9. Reveja as definições no painel **Resumo** e clique em **OK**.

## <a name="create-the-network-resources-in-poc-2"></a>Criar os Recursos de Rede na POC 2
### <a name="log-in-as-a-tenant"></a>Iniciar sessão como um inquilino
Um administrador de serviços pode iniciar sessão como um inquilino para testar os planos, as ofertas e as subscrições que os seus inquilinos poderão utilizar. Se ainda não tiver uma conta, deverá [Criar uma conta de inquilino](azure-stack-add-new-user-aad.md) antes de iniciar sessão.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e a sub-rede VM
1. Inicie sessão com uma conta de inquilino.
2. No portal do Azure, clique em **Novo**.
   
3. Selecione **Rede** no menu do Marketplace.
4. Clique em **Rede virtual** no menu.
5. Clique em **Criar** perto da parte inferior do painel de descrição do recurso. Escreva os seguintes valores para os campos adequados listados na tabela abaixo.
   
   | **Campo** | **Valor** |
   | --- | --- |
   | Nome |vnet-02 |
   | Espaço de endereços |10.0.20.0/23 |
   | Nome da sub-rede |subnet-02 |
   | Intervalo de endereços da sub-rede |10.0.20.0/24 |
6. Deverá encontrar a Subscrição que criou anteriormente preenchida no campo **Subscrição**.
7. Em relação ao Grupo de Recursos, pode criar um novo Grupo de Recursos ou, se já tiver um, selecione **Utilizar existente**.
8. Verifique a **localização** predefinida. Se quiser, pode afixar a rede virtual ao Dashboard para facilitar o acesso.
9. Clique em **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abra o recurso de Rede virtual que criou (**Vnet-02**) a partir do Dashboard.
2. No painel **Definições**, selecione **Sub-redes**.
3. Clique em **Sub-rede de gateway** para adicionar uma sub-rede de gateway à rede virtual.
   
4. O nome da sub-rede está predefinido como **GatewaySubnet**.
   As sub-redes de gateway são especiais e têm de ter este nome específico para funcionarem corretamente.
5. No campo **Intervalo de endereços**, escreva **10.0.20.0/24**.
6. Clique em **Criar** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o Gateway de Rede Virtual
1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Gateway de rede virtual** na lista de recursos de rede.
4. Reveja a descrição e clique em **Criar**.
5. No campo **Nome**, escreva **GW2**.
6. Clique em **Rede virtual** para escolher uma rede virtual.
   Selecione **Vnet-02** na lista.
7. Clique em **Endereço IP público**. Quando o painel **Escolher endereço IP público** for aberto, clique em **Criar novo**.
8. No campo **Nome**, escreva **GW2-PiP** e clique em **OK**.
9. O **Tipo de gateway** deve ter a opção **VPN** selecionada por predefinição. Mantenha esta definição.
10. O **Tipo de VPN** deve ter a opção **Baseado na rota** selecionada por predefinição.
    Mantenha esta definição.
11. Verifique se a **Subscrição** e a **Localização** estão corretas. Pode afixar o recurso ao Dashboard se assim o desejar. Clique em **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o Gateway de Rede Local
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Obter o endereço IP do Adaptador externo da VM NAT
1. Inicie sessão no computador físico do Azure Stack para a POC1.
2. Prima sem soltar a [Tecla Windows] + R para abrir o menu **Executar**, escreva **mstsc** e prima enter.
3. No campo **Computador**, escreva o nome **MAS-BGPNAT01** e clique em  **Ligar**.
4. Clique no menu Iniciar, clique com o botão direito do rato em **Windows PowerShell** e selecione **Executar como Administrador**.
5. Escreva **ipconfig /all**.
6. Localize o adaptador Ethernet que está ligado à sua rede local e tome nota do endereço IPv4 vinculado a esse adaptador. No ambiente de exemplo, o endereço é **10.16.169.131**, mas o seu será diferente.
7. Registe esse endereço. É este que irá utilizar mais tarde como Endereço IP Público do recurso de Gateway de Rede Local criado na POC1.

#### <a name="create-the-local-network-gateway-resource"></a>Criar o Recurso de Gateway de Rede Local
1. Inicie sessão no computador físico do Azure Stack para a POC2.
2. No campo **Computador**, escreva o nome **MAS-CON01** e clique em **Ligar**.
3. No portal do Azure, clique em **Novo**.
   
4. Selecione **Rede** no menu do Marketplace.
5. Selecione **Gateway de rede local** na lista de recursos.
6. No campo **Nome**, escreva **POC1-GW**.
7. Agora, vai precisar do Endereço IP Público que registou para o Gateway de rede virtual na POC1. Escreva **10.16.169.131** no campo **Endereço IP**.
8. No campo **Espaço de Endereços**, escreva o espaço de endereços de **Vnet-01** da POC1 - **10.0.0.0/16**.
9. Certifique-se de que a **Subscrição**, o **Grupo de Recursos** e a **Localização** estão corretos e clique em **Criar**.

## <a name="create-the-connection"></a>Criar a Ligação
1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Rede** no menu do Marketplace.
3. Selecione **Ligação** na lista de recursos.
4. No painel de definições **Básico**, escolha **Site a site (IPSec)** como o **Tipo de ligação**.
5. Selecione a **Subscrição**, o **Grupo de Recursos** e a **Localização**, e clique em **OK**.
6. No painel **Definições**, escolha o **Gateway de Rede Virtual** (**GW1**) que criou anteriormente.
7. Escolha o **Gateway de Rede Local** (**POC1-GW**) que criou anteriormente.
8. No campo **Nome da Ligação**, escreva **POC2-POC1**.
9. No campo **Chave Partilhada (PSK)**, escreva **12345**. Se escolher um valor diferente, não se esqueça que este TEM de corresponder ao valor da Chave Partilhada criada na POC1. Clique em **OK**.

## <a name="create-a-vm"></a>Criar uma VM
Crie uma VM na POC1 agora e coloque-a na sub-rede VM da sua rede virtual.

1. No portal do Azure, clique em **Novo**.
   
2. Selecione **Máquinas Virtuais** no menu do Marketplace.
3. Na lista de imagens de máquinas virtuais, selecione a imagem **Windows Server 2012 R2 Datacenter**.
4. No painel **Básico**, no campo **Nome**, escreva **VM02**.
5. Escreva um nome de utilizador e uma palavra-passe válidos. Irá utilizar esta conta para iniciar sessão na VM depois de esta ser criada.
6. Forneça uma **Subscrição**, um **Grupo de Recursos** e uma **Localização** e, em seguida, clique em **OK**.
7. No painel **Tamanho**, escolha um tamanho de VM para esta instância e, em seguida, clique em **Selecionar**.
8. No painel Definições, pode aceitar as predefinições; certifique-se apenas de que a rede virtual selecionada é a **VNET-02** e que a sub-rede está definida como **20.0.0.0/24**. Clique em **OK**.
9. Reveja as definições no painel **Resumo** e clique em **OK**.

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>Configurar a VM NAT em cada POC para o gateway transversal
A POC foi concebida para ser autónoma e estar isolada da rede em que o anfitrião físico está implementado e é por isso que a rede VIP "Externa" à qual os gateways estão ligados não é efetivamente externa; em vez disso, encontra-se oculta atrás de um router ocupada a fazer a Tradução de Endereços de Rede (NAT). O router é, na verdade, uma VM do Windows Server (**MAS-BGPNAT01**) com a função Serviços de Encaminhamento e Acesso Remoto (RRAS) em execução na infraestrutura da POC. Tem de configurar a NAT na VM MAS-BGPNAT01 para permitir que a Ligação de Rede de VPNs se ligue em ambas as extremidades. Para tal, tem de criar um Mapeamento NAT estático que mapeie a interface externa na VM BGPNAT para o VIP do Conjunto de Gateways de Periferia para as portas necessárias a uma ligação VPN.

> [!NOTE]
> Esta configuração só é necessária para ambientes de POC.
> 
> 

### <a name="configure-nat"></a>Configurar a NAT
Precisa de seguir estes passos em AMBOS os ambientes de POC.

1. Inicie sessão no computador físico do Azure Stack para a POC1.
2. Prima sem soltar a [Tecla Windows] + R para abrir o menu **Executar**, escreva **mstsc** e prima **Enter**.
3. No campo **Computador**, escreva o nome **MAS-BGPNAT01** e clique em **Ligar**.
4. Clique no menu Iniciar, clique com o botão direito do rato em **Windows PowerShell** e selecione **Executar como Administrador**.
5. Escreva **ipconfig /all**.
6. Localize o Adaptador Ethernet que está ligado à sua rede local e tome nota do endereço IPv4 vinculado a esse adaptador. No ambiente de exemplo, o endereço é **10.16.169.131** (com um círculo vermelho à volta), mas o seu será diferente.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. Escreva o seguinte comando do PowerShell para designar o endereço NAT externo para as portas de autenticação IKE. Não se esqueça de alterar o endereço IP para o que corresponde ao seu ambiente.
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. Em seguida, crie um mapeamento NAT estático para mapear o endereço externo para o Endereço IP Público de Gateway a fim de mapear a porta 500 de ISAKMP para a PHASE 1 do túnel IPSEC.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> Neste caso, o parâmetro `-InternalAddress` é o Endereço IP Público do Gateway de Rede Virtual que criou anteriormente.  Para localizar este endereço IP, observe as propriedades do painel Gateway de Rede Virtual e localize o valor do Endereço IP Público.       

9. Por último, tem de configurar a NAT transversal que utiliza a porta 4500 para estabelecer com êxito o túnel IPEC completo através dos dispositivos NAT.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> Neste caso, o parâmetro `-InternalAddress` é o Endereço IP Público do Gateway de Rede Virtual que criou anteriormente.  Para localizar este endereço IP, observe as propriedades do painel Gateway de Rede Virtual e localize o valor do Endereço IP Público.       

10. Repita os passos 1 a 9 na POC2.

## <a name="test-the-connection"></a>Testar a ligação
Agora que a ligação Site a Site foi estabelecida, deve validar a sua capacidade de fazer circular o tráfego através da mesma. Esta é uma tarefa simples: só tem de iniciar sessão numa das VMs que criou em qualquer um dos ambientes de POC e enviar o ping para a VM que criou no outro ambiente. Para garantir que o tráfego está a circular através da ligação Site a Site, deve certificar-se de que envia o ping para o Endereço IP direto (DIP) da VM na sub-rede remota, e não para o VIP. Para isso, precisa de localizar e tomar nota do endereço na outra extremidade da ligação.

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>Iniciar sessão na VM de inquilinos na POC1
1. Inicie sessão no computador físico do Azure Stack para a POC1 e inicie sessão no Portal com uma conta de inquilino.
2. Clique em **Máquinas Virtuais** na barra de navegação esquerda.
3. Localize a **VM01** que criou anteriormente na lista de VMs e clique nela.
4. No painel da máquina virtual, clique em **Ligar**.
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Abra uma Linha de comandos a partir da VM e escreva **ipconfig /all**.
6. Localize o **Endereço IPv4** nos resultados e tome nota do mesmo. Este é o endereço cujo ping irá enviar a partir da POC2. No ambiente de exemplo, o endereço é **10.0.10.4**, mas no seu ambiente poderá ser diferente. Em todo o caso, deverá estar contido na sub-rede **10.0.10.0/24** criada anteriormente.

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>Iniciar sessão na VM de inquilinos na POC2
1. Inicie sessão no computador físico do Azure Stack para a POC2 e inicie sessão no portal com uma conta de inquilino.
2. Clique em **Máquinas Virtuais** na barra de navegação esquerda.
3. Localize a **VM02** que criou anteriormente na lista de VMs e clique nela.
4. No painel da máquina virtual, clique em **Ligar**.
   
5. Abra uma Linha de comandos a partir da VM e escreva **ipconfig /all**.
6. Deverá encontrar um endereço IPv4 contido em 10.0.20.0/24. No ambiente de exemplo, o endereço é 10.0.20.4, mas o seu poderá ser diferente.
7. Agora, a partir da VM na POC2, envie o ping para a VM na POC1, através do túnel. Para isso, deve enviar o ping para o DIP que registou da VM01.
   No ambiente de exemplo, o endereço é 10.0.10.4, mas certifique-se de que envia o ping para o endereço que anotou no seu laboratório. Deverá ver um resultado semelhante a este:
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. Uma resposta da VM remota a indicar que o teste foi bem-sucedido! Pode fechar a janela de Ligação da VM. Em alternativa, pode tentar realizar outras transferências de dados, como uma cópia de ficheiro, para testar a ligação.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Ver estatísticas de transferência de dados através da ligação de gateway
Se quiser determinar a quantidade de dados que está a ser transmitida através da sua ligação Site a Site, encontra estas informações disponíveis no painel Ligação. Este teste é também uma excelente forma de verificar se o ping que acaba de enviar foi efetivamente transmitido através da ligação VPN.

1. Com a sessão ainda iniciada na VM de inquilinos da POC2, inicie sessão no **Portal de POC do Microsoft Azure Stack** com a sua conta de inquilino.
2. Clique no item de menu **Procurar** e selecione **Ligações**.
3. Clique na ligação **POC2-POC1** na lista.
4. No painel Ligação, são mostradas as estatísticas de **Entrada de Dados** e **Saída de Dados**. Na captura de ecrã seguinte, encontra alguns números mais elevados do que os criados por um simples ping. Isso deve-se a algumas transferências de ficheiros adicionais. Deverá ver alguns valores diferentes de zero aí.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)




<!--HONumber=Feb17_HO2-->



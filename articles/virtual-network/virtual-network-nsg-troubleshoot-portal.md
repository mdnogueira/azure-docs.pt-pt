---
title: "Resolver problemas relacionados com grupos de segurança de rede - Portal | Microsoft Docs"
description: "Saiba como resolver problemas relacionados com grupos de segurança de rede no modelo de implementação Azure Resource Manager através do Portal do Azure."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: f01d3b43a7953697a6b03e176dace33448d95cd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Resolver problemas relacionados com grupos de segurança de rede através do Portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Se configurar grupos de segurança de rede (NSGs) na sua máquina virtual (VM) e está a ter problemas de conectividade VM, este artigo fornece uma descrição geral das funcionalidades de diagnóstico para os NSGs ajudar a resolver adicional.

Os NSGs permitem-lhe controlar os tipos de tráfego que fluxo e para as máquinas virtuais (VMs). Os NSGs podem ser aplicados a sub-redes na uma rede Virtual do Azure (VNet), interfaces de rede (NIC) ou ambos. As regras efetivas aplicadas a um NIC são uma agregação de regras que existem no NSGs aplicados a um NIC e a sub-rede que está ligado a. As regras entre estes NSGs, por vezes, podem entrar em conflito entre si e afetar a conectividade de rede da VM.  

Pode ver todas as regras de segurança eficaz dos seus NSGs, como aplicada em NICs da VM. Este artigo mostra como resolver problemas de conectividade VM a utilizar estas regras no modelo de implementação Azure Resource Manager. Se não estiver familiarizado com conceitos de VNet e o NSG, leia o [rede Virtual](virtual-networks-overview.md) e [grupos de segurança de rede](virtual-networks-nsg.md) artigos de descrição geral.

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Utilizar regras de segurança de eficaz para resolver fluxo de tráfego da VM
O cenário em que se segue é um exemplo de um problema de ligação comuns:

Uma VM chamada *VM1* faz parte de uma sub-rede designada *Subnet1* dentro de uma VNet com o nome *WestUS VNet1*. Uma tentativa de ligar à VM através de RDP através da porta TCP 3389 falhar. Os NSGs são aplicados em ambos os o NIC *VM1 NIC1* e a sub-rede *Subnet1*. O tráfego para a porta TCP 3389 é permitido no NSG associado à interface de rede *VM1 NIC1*; no entanto, o ping TCP para VM1 da porta 3389 falhar.

Embora este exemplo utiliza a porta TCP 3389, os seguintes passos podem ser utilizados para determinar as falhas de ligação de entrada e saída através de qualquer porta.

### <a name="vm"></a>Ver as regras de segurança eficaz para uma máquina virtual
Conclua os seguintes passos para resolver os NSGs para uma VM:

Pode ver uma lista completa das regras de segurança eficaz de um NIC, da própria VM. Também pode adicionar, modificar e eliminar regras do NSG NIC e sub-rede a partir do painel regras Efetivo, se tiver permissões para realizar estas operações.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, em seguida, clique em **máquinas virtuais** na lista que é apresentada.
3. Selecione uma VM para resolver problemas da lista que é apresentado e é apresentado o painel uma VM com as opções.
4. Clique em **diagnosticar & resolver problemas** e, em seguida, selecione um problema comum. Neste exemplo, **não consigo ligar à minha VM do Windows** está selecionada. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. Os passos são apresentados sob o problema, conforme mostrado na imagem seguinte: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Clique em *regras do grupo de segurança eficaz* na lista de passos recomendados.
6. O **obter regras de segurança eficaz** é apresentado o painel, conforme mostrado na imagem seguinte:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Tenha em atenção as seguintes secções de imagem:
   
   * **Âmbito:** definido como *VM1*, a VM que selecionou no passo 3.
   * **Interface de rede:** *VM1 NIC1* está selecionada. Uma VM pode ter várias interfaces de rede (NIC). Cada NIC pode ter regras de segurança eficaz exclusivo. Quando a resolução de problemas, poderá ter de ver as regras de segurança em vigor para cada NIC.
   * **Os NSGs associados:** os NSGs podem ser aplicados para o NIC e a NIC está ligada à sub-rede. Na imagem, um NSG foi aplicado a NIC e a sub-rede que está ligado a. Pode clicar nos nomes NSG diretamente modificar regras no NSGs.
   * **Separador VM1 nsg:** a lista de regras apresentado na imagem é para o NSG aplicado para o NIC. Várias regras de predefinidas são criadas pelo Azure sempre que um NSG é criado. Não é possível remover as regras predefinidas, mas pode substitui-los com regras de maior prioridade. Para obter mais informações sobre regras predefinidas, leia o [descrição geral do NSG](virtual-networks-nsg.md#default-rules) artigo.
   * **A coluna de destino:** algumas das regras tem texto na coluna, enquanto outros utilizadores têm de prefixos de endereço. O texto é o nome de etiquetas predefinidas aplicada a regra de segurança quando foi criado. As etiquetas são identificadores fornecidos pelo sistema que representam vários prefixos. Selecionar uma regra com uma etiqueta, tais como *AllowInternetOutBound*, apresenta uma lista de prefixos no **prefixos de endereços** painel.
   * **Transferir:** a lista de regras pode ser longa. Pode transferir um ficheiro. csv das regras para a análise offline clicando **transferir** e guardar o ficheiro.
   * **AllowRDP** regra de entrada: esta regra permite ligações RDP para a VM.
7. Clique em de **Subnet1 NSG** separador para ver as efetivas regras do NSG aplicado à sub-rede, conforme mostrado na imagem seguinte: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Tenha em atenção o *denyRDP* **entrada** regra. As regras de entrada aplicadas na sub-rede são avaliadas antes das regras aplicadas na interface de rede. Uma vez que é aplicada a regra negar na sub-rede, falha o pedido para estabelecer a ligação TCP 3389, porque a regra permitir na NIC nunca é avaliada. 
   
    O *denyRDP* regra é o motivo por que motivo está a falhar a ligação de RDP. Remover deve resolver o problema.
   
   > [!NOTE]
   > Se a VM associada com a NIC não está num Estado em execução ou não foram aplicados NSGs para o NIC ou uma sub-rede, não existem regras são apresentadas.
   > 
   > 
8. Para editar as regras do NSG, clique em *Subnet1 NSG* no **associados NSGs** secção.
   Esta ação abre o **Subnet1 NSG** painel. Pode editar diretamente as regras ao clicar no **regras de segurança de entrada**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. Depois de remover o *denyRDP* regra de entrada no **Subnet1 NSG** e adicionar um *allowRDP* regra, o eficaz regras lista parece que a imagem seguinte:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Confirme que a porta TCP 3389 está aberta abrindo uma ligação de RDP para a VM ou utilizar a ferramenta de PsPing. Pode saber mais sobre PsPing ao ler o [página de transferência PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Ver as regras de segurança eficaz para uma interface de rede
Se o fluxo de tráfego da VM é afetado para uma NIC específica, pode ver uma lista completa das regras eficazes para a NIC de contexto de interfaces de rede, efetuando os seguintes passos:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, em seguida, clique em **interfaces de rede** na lista que é apresentada.
3. Seleccione uma interface de rede. Na imagem seguinte, um NIC com o nome *VM1 NIC1* está selecionada.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Tenha em atenção que o **âmbito** está definido para a interface de rede selecionada. Para saber mais sobre as informações adicionais, apresentadas, leia o passo 6 do **resolver NSGs para uma VM** secção deste artigo.
   
   > [!NOTE]
   > Se um NSG é removido de uma interface de rede, a sub-rede do NSG é ainda Efetivo no NIC indicado. Neste caso, o resultado seria apenas mostrar as regras da sub-rede NSG. Regras surgem apenas se a NIC está ligada a uma VM.
   > 
   > 
4. Pode editar diretamente regras para NSGs associados a um NIC e uma sub-rede. Para saber como, leia o passo 8 a **ver regras de segurança eficaz para uma máquina virtual** secção deste artigo.

## <a name="nsg"></a>Ver as regras de segurança eficaz para um grupo de segurança de rede (NSG)
Quando modificar as regras do NSG, poderá pretender rever o impacto das regras a serem adicionados numa VM específica. Pode ver uma lista completa das regras de segurança eficaz para todos os NICs de um determinado NSG aplicado, sem ter de mudar o contexto no painel NSG indicado. Para resolver efetivas regras dentro de um NSG, execute os seguintes passos:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, em seguida, clique em **grupos de segurança de rede** na lista que é apresentada.
3. Selecione um NSG. Na imagem seguinte, que foi selecionado um NSG denominado VM1 nsg.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Tenha em atenção as seguintes secções de imagem anterior:
   
   * **Âmbito:** definido como o NSG selecionado.
   * **Máquina virtual:** NSG um quando é aplicada a uma sub-rede, é aplicada a todas as interfaces de rede ligadas a todas as VMs ligadas à sub-rede. Esta lista mostra todas as VMs este NSG é aplicado a. Pode selecionar qualquer VM na lista.
     
     > [!NOTE]
     > Se um NSG é aplicado a apenas uma subrede vazia, VMs não serão listadas. Se um NSG é aplicado a um NIC que não esteja associado uma VM, os NICs também não serão listados. 
     > 
     > 
   * **Interface de rede:** uma VM pode ter várias interfaces de rede. Pode selecionar uma interface de rede ligada à VM selecionada.
   * **AssociatedNSGs:** em qualquer altura, um NIC pode ter até duas eficazes os NSGs, um aplicado a NIC e o outro à sub-rede. Embora o âmbito é selecionado como VM1-nsg, se a NIC tiver uma NSG de sub-rede efetiva, o resultado apresentará ambos os NSGs.
4. Pode editar diretamente regras para NSGs associados um NIC ou sub-rede. Para saber como, leia o passo 8 a **ver regras de segurança eficaz para uma máquina virtual** secção deste artigo.

Para saber mais sobre as informações adicionais, apresentadas, leia o passo 6 do **ver regras de segurança eficaz para uma máquina virtual** secção deste artigo.

> [!NOTE]
> Embora uma sub-rede e a NIC cada só podem ter um que NSG aplicado aos mesmos, um NSG pode ser associado a vários NICs e sub-redes múltiplas.
> 
> 

## <a name="considerations"></a>Considerações
Quando a resolução de problemas de conectividade, considere os seguintes pontos:

* Regras NSG predefinidas irão bloquear o acesso de entrada da internet e permitir apenas VNet tráfego de entrada. As regras devem ser adicionadas explicitamente para permitir acesso de entrada a partir da Internet, conforme necessário.
* Se não existem quaisquer regras de segurança NSG fazendo com que a conectividade de rede de uma VM falhar, o problema pode ser devido a:
  * Software de firewall em execução no sistema de operativo da VM
  * Rotas configuradas para aplicações virtuais ou tráfego no local. Tráfego de Internet pode ser redirecionado no local através do túnel forçada. Uma ligação de RDP/SSH através da Internet para a VM pode não funcionar com esta definição, dependendo da forma como o hardware de rede no local processa este tráfego. Leia o [rotas de resolução de problemas](virtual-network-routes-troubleshoot-powershell.md) artigo para aprender a diagnosticar problemas de rota que podem ser impeding o fluxo de tráfego e terminar da VM. 
* Se tiver executado o peering VNets, por predefinição, a etiqueta VIRTUAL_NETWORK automaticamente será expandido para incluir os prefixos das VNets. Pode ver estes prefixos no **ExpandedAddressPrefix** lista, para resolver problemas relacionados com a conectividade do VNet peering. 
* Regras de segurança eficaz só são mostradas se houver um NSG associado a VM NIC e ou sub-rede. 
* Se não existem nenhum NSGs associados com a NIC ou sub-rede e de que possui um endereço IP público atribuído à sua VM, todas as portas será abertas para acesso de entrada e saído. Se a VM tem um endereço IP público, aplicando os NSGs para o NIC ou uma sub-rede é vivamente recomendado.


---
title: "Resolver problemas relacionados com grupos de segurança de rede - PowerShell | Microsoft Docs"
description: "Saiba como resolver problemas relacionados com grupos de segurança de rede no modelo de implementação Azure Resource Manager com o Azure PowerShell."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 5edaf7197576ac1c0bd1fc6bed21fd65ed135106
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Resolver problemas relacionados com grupos de segurança de rede com o Azure PowerShell
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

## <a name="detailed-troubleshooting-steps"></a>Passos de resolução de problemas detalhada
Conclua os seguintes passos para resolver os NSGs para uma VM:

1. Inicie uma sessão do PowerShell do Azure e iniciar sessão no Azure. Se não estiver familiarizado com a utilização do Azure PowerShell, leia o [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) artigo.
2. Introduza o seguinte comando para devolver todas as regras NSG aplicadas a um NIC com o nome *VM1 NIC1* no grupo de recursos *RG1*:
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Se não souber o nome de uma NIC, introduza o seguinte comando para obter os nomes de todos os NICs num grupo de recursos: 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    O texto seguinte é um exemplo do resultado regras Efetivo devolvido para o *VM1 NIC1* NIC:
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Tenha em atenção as seguintes informações no resultado:
   
   * Existem duas **NetworkSecurityGroup** secções: uma é associada a uma sub-rede (*Subnet1*) e um está associado um NIC (*VM1 NIC1*). Neste exemplo, um NSG foi aplicado a cada um.
   * **Associação** mostra o recurso (sub-rede ou NIC) de um determinado NSG é associado. Se o recurso NSG é movido/desassociar imediatamente antes de executar este comando, poderá ter de aguardar alguns segundos para que a alteração para refletir no resultado do comando. 
   * Os nomes de regra são precedidos pela *defaultSecurityRules*: NSG um quando é criado, várias regras de segurança predefinidas são criadas dentro da mesma. Não não possível remover as regras predefinidas, mas pode ser substituídos com as regras de prioridade superiores.
     Leia o [descrição geral do NSG](virtual-networks-nsg.md#default-rules) artigo para saber mais sobre o NSG predefinido regras de segurança.
   * **ExpandedAddressPrefix** expande os prefixos de endereço de etiquetas predefinidas NSG. Etiquetas representam vários prefixos de endereço. Expansão das etiquetas pode ser útil quando resolver problemas de conectividade VM do prefixos de endereço específico. Por exemplo, com o VNET peering, etiqueta VIRTUAL_NETWORK expande para mostrar os prefixos de VNet em modo de peering no resultado do anterior.
     
     > [!NOTE]
     > As comando apenas mostra Efetivo regras, se um NSG é associado uma sub-rede, um NIC ou ambos. Uma VM pode ter vários NICs com NSGs diferentes aplicados. Quando a resolução de problemas, execute o comando para cada NIC.
     > 
     > 
3. Para facilitar a filtragem por grande número de regras do NSG, introduza os seguintes comandos para resolução de problemas mais: 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    É aplicado um filtro para o tráfego RDP (a porta TCP 3389), a vista de grelha, conforme mostrado na imagem seguinte:
   
    ![Lista de regras](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Como pode ver na vista de grelha, são ambos de permissão e negação regras para RDP. A saída do passo 2 mostra que o *DenyRDP* regra está no NSG aplicado à sub-rede. Para as regras de entrada, NSGs aplicados à sub-rede são processados pela primeira vez. Se for encontrada uma correspondência, o NSG aplicado à interface de rede não foi processado. Neste caso, o *DenyRDP* regra da sub-rede bloqueia RDP para a VM (**VM1**).
   
   > [!NOTE]
   > Uma VM pode ter vários NICs anexados ao mesmo. Cada pode ser ligada à sub-rede diferente. Uma vez que os comandos nos passos anteriores são executados em relação a um NIC, é importante certificar-se de que especifica a NIC está a ter a falha de conectividade para. Se não tiver a certeza, pode sempre executar os comandos em relação a cada NIC ligado à VM.
   > 
   > 
5. Para o RDP nas VM1, altere o *RDP negar (3389)* regra para *permitir RDP(3389)* no **Subnet1 NSG** NSG. Confirme que a porta TCP 3389 está aberta abrindo uma ligação de RDP para a VM ou utilizar a ferramenta de PsPing. Pode saber mais sobre PsPing ao ler o [PsPing página de transferência](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    Pode ou remover regras a um NSG, utilizando as informações na saída do comando seguinte:
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Considerações
Quando a resolução de problemas de conectividade, considere os seguintes pontos:

* Regras NSG predefinidas irão bloquear o acesso de entrada da internet e permitir apenas VNet tráfego de entrada. As regras devem ser adicionadas explicitamente para permitir acesso de entrada a partir da Internet, conforme necessário.
* Se não existem quaisquer regras de segurança NSG fazendo com que a conectividade de rede de uma VM falhar, o problema pode ser devido a:
  * Software de firewall em execução no sistema de operativo da VM
  * Rotas configuradas para aplicações virtuais ou tráfego no local. Tráfego de Internet pode ser redirecionado no local através do túnel forçada. Uma ligação de RDP/SSH através da Internet para a VM pode não funcionar com esta definição, dependendo da forma como o hardware de rede no local processa este tráfego. Leia o [rotas de resolução de problemas](virtual-network-routes-troubleshoot-powershell.md) artigo para aprender a diagnosticar problemas de rota que podem ser impeding o fluxo de tráfego e terminar da VM. 
* Se tiver executado o peering VNets, por predefinição, a etiqueta VIRTUAL_NETWORK automaticamente será expandido para incluir os prefixos das VNets. Pode ver estes prefixos no **ExpandedAddressPrefix** lista, para resolver problemas relacionados com a conectividade do VNet peering. 
* Regras de segurança eficaz só são mostradas se houver um NSG associado a VM NIC e ou sub-rede. 
* Se não existem nenhum NSGs associados com a NIC ou sub-rede e de que possui um endereço IP público atribuído à sua VM, todas as portas será abertas para acesso de entrada e saído. Se a VM tem um endereço IP público, aplicando os NSGs para o NIC ou uma sub-rede é vivamente recomendado.  


---
title: FAQ da rede Virtual do Azure | Microsoft Docs
description: "Respostas às perguntas mais frequentes sobre as redes virtuais do Microsoft Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/18/2017
ms.author: jdial
ms.openlocfilehash: c71e188b74ebfd9420f840957e83190cf476b584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Rede Virtual do Azure perguntas mais frequentes (FAQ)

## <a name="virtual-network-basics"></a>Noções básicas de rede virtuais

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma rede Virtual do Azure (VNet)?
Uma rede Virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da nuvem do Azure dedicado à sua subscrição. Pode utilizar as VNets para aprovisionar e gerir redes privadas virtuais (VPNs) no Azure e, opcionalmente, ligar VNets outras VNets no Azure ou com no local a infraestrutura de TI para criar soluções híbridas ou em vários locais. Cada VNet que criou tem bloco CIDR e pode ser associado a outras redes locais e as VNets, desde que os blocos CIDR não se podem sobrepor. Também tem controlo das definições do servidor DNS para VNets e a segmentação da VNet em sub-redes.

Utilize as VNets para:

* Crie um dedicado privada apenas na nuvem VNet, por vezes, não precisa de uma configuração de vários locais para a sua solução. Quando cria uma VNet, os seus serviços e VMs dentro da VNet podem comunicar diretamente e segura entre si na nuvem. Isto mantém o tráfego de forma segura dentro da VNet, mas ainda permite-lhe configurar as ligações de ponto final para as VMs e serviços que necessitam de uma comunicação de Internet como parte da sua solução.
* Expandir o seu centro de dados com as VNets em segurança, pode criar VPNs de (S2S) do site para site tradicionais de forma segura dimensionar a capacidade de centro de dados. S2S VPNs utilize IPSEC para fornecer uma ligação segura entre o gateway de VPN empresarial e o Azure.
* Cenários de nuvem híbrida ativar VNets dão-lhe a flexibilidade para suportar uma gama de cenários de nuvem híbrida. Pode ligar de forma segura aplicações baseadas na nuvem a qualquer tipo de sistema no local como mainframes e sistemas Unix.

### <a name="how-do-i-know-if-i-need-a-vnet"></a>Como saber se preciso de uma VNet?
O [descrição geral de rede Virtual](virtual-networks-overview.md) artigo fornece uma tabela de decisão que irão ajudar a decidir a melhor opção de conceção de rede para si.

### <a name="how-do-i-get-started"></a>Como posso começar?
Visite o [documentação das redes virtuais](https://docs.microsoft.com/azure/virtual-network/) para começar a utilizar. Este conteúdo fornece informações de descrição geral e implementação para todas as funcionalidades de VNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Pode utilizar as VNets sem conectividade em vários locais?
Sim. Pode utilizar uma VNet sem utilizar conectividade híbrida. Isto é particularmente útil se pretender executar controladores de domínio do Active Directory do Microsoft Windows Server e farms do SharePoint no Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Pode efetuar otimização de WAN entre VNets ou uma VNet e os meus Centro de dados no local?

Sim. Pode implementar um [aplicação virtual de rede de otimização de WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) de vários fornecedores através do Azure Marketplace.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>As ferramentas de utilizar para criar uma VNet?
Pode utilizar as ferramentas seguintes para criar ou configurar uma VNet:

* Portal do Azure (para clássico e as VNets do Resource Manager).
* Um ficheiro de configuração de rede (netcfg - para apenas as VNets clássicas). Consulte o [configurar uma VNet com um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md) artigo.
* PowerShell (para clássico e as VNets do Resource Manager).
* CLI do Azure (para clássico e as VNets do Resource Manager).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Os intervalos de endereços podem utilizar a minha vnets?
Qualquer intervalo de endereços IP definidos no [RFC 1918](http://tools.ietf.org/html/rfc1918). Por exemplo, 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Pode ter endereços IP públicos no meu VNets?
Sim. Para obter mais informações sobre os intervalos de endereços IP públicos, consulte o [espaço de endereços IP públicos numa rede virtual](virtual-networks-public-ip-within-vnet.md) artigo. Os endereços IP públicos não serão acessíveis diretamente a partir da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Existe um limite ao número de sub-redes na minha VNet?
Sim. Leia o [Azure limita](../azure-subscription-service-limits.md#networking-limits) artigo para obter detalhes. Espaços de endereços da sub-rede não se podem sobrepor um do outro.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições sobre como utilizar estas sub-redes de endereços IP?
Sim. Azure reserva-se alguns endereços IP dentro de cada sub-rede. O primeiro e o último endereço IP das sub-redes são reservados para conformidade com o protocolo, juntamente com três outros endereços utilizados para os servidores do Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Como pequenos e grandes como podem ser VNets e sub-redes?
A sub-rede mais pequena que suportamos é/29, não sendo o maior um /8 (utilizando as definições de sub-rede CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso usar o meu VLANs para o Azure utilizando as VNets?
Não. As VNets estão sobrepõe de camada 3. Azure não suporta qualquer semântica de camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Pode especificar políticas de encaminhamento personalizadas no meu VNets e sub-redes?
Sim. Pode utilizar o utilizador definido encaminhamento (UDR). Para obter mais informações sobre UDR, visite [rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>As VNets suportam multicast ou difusão?
Não. Não é proporcionado suporte multicast ou difusão.

### <a name="what-protocols-can-i-use-within-vnets"></a>Protocolos de que pode utilizar nas VNets?
Pode utilizar protocolos de TCP, UDP e TCP/IP de ICMP nas VNets. Multicast, difusão, pacotes de IP-em-IP encapsulado e pacotes de Generic Routing Encapsulation (GRE) estão bloqueadas nas VNets. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso ping meu routers predefinido numa VNet?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Pode utilizar tracert para diagnosticar conectividade?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes depois de é criada a VNet?
Sim. Sub-redes podem ser adicionadas a VNets em qualquer altura, desde que o endereço de sub-rede que não faz parte de outra sub-rede na VNet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha sub-rede depois de criar?
Sim. Pode adicionar, remover, expandir ou reduzir uma sub-rede, se existirem sem VMs ou serviços implementados no mesmo.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Posso modificar sub-redes depois de criá-las?
Sim. Pode adicionar, remover e modificar os blocos CIDR utilizados por uma VNet.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Posso ligar à internet se estiver a executar os meus serviços numa VNet?
Sim. Todos os serviços implementados numa VNet podem ligar à internet. Cada serviço em nuvem implementado no Azure tem um VIP publicamente endereçável atribuído. Terá de definir pontos finais de entrada para funções de PaaS e os pontos finais para máquinas virtuais para ativar estes serviços aceitar ligações a partir da internet.

### <a name="do-vnets-support-ipv6"></a>As VNets suportam o IPv6?
Não. Não é possível utilizar o IPv6 com as VNets neste momento.

### <a name="can-a-vnet-span-regions"></a>Uma VNet pode abranger a regiões?
Não. Uma VNet está limitada a uma única região.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Pode ligar uma VNet para outra VNet no Azure?
Sim. Pode ligar uma VNet para outra VNet a utilizar:
- Um Gateway VPN do Azure. Leia o [configurar uma ligação VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) artigo para obter detalhes. 
- O VNet peering. Leia o [descrição geral do VNet peering](virtual-network-peering-overview.md) artigo para obter detalhes.

## <a name="name-resolution-dns"></a>Resolução de nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são as minhas opções de DNS para VNets?
Utilize a tabela de decisão no [resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md) página para ajudá-lo através de todos os DNS opções disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Pode especificar servidores DNS para uma VNet?
Sim. Pode especificar endereços IP do servidor DNS nas definições da VNet. Serão aplicada como servidores DNS predefinido para todas as VMs na VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS que pode a especificar
Referência a [Azure limita](../azure-subscription-service-limits.md#networking-limits) artigo para obter detalhes.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar os meus servidores DNS depois criei rede?
Sim. Pode alterar a lista de servidores DNS para a sua VNet em qualquer altura. Se alterar a lista de servidores DNS, terá de reiniciar cada uma das VMs na sua VNet serem para recolher o novo servidor DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é DNS fornecidos pelo Azure e funciona com as VNets?
DNS fornecidos pelo Azure é um serviço DNS de multi-inquilino disponibilizado pela Microsoft. Azure regista todas as VMs e instâncias de função Serviço de nuvem neste serviço. Este serviço fornece resolução de nomes pelo nome do anfitrião para as VMs e instâncias de função contidas no mesmo serviço de nuvem e através do FQDN para VMs e instâncias de função na mesma VNet. Leia o [resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md) artigo para obter mais informações sobre o DNS.

> [!NOTE]
> Há uma limitação de momento, para os serviços de 100 nuvem primeiro numa VNet para resolução de nomes de inquilino em vários locais através do DNS fornecidos pelo Azure. Se estiver a utilizar o seu próprio servidor DNS, esta limitação não se aplica.
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Pode substituir as minhas definições DNS por-VM / base do serviço?
Sim. Pode definir os servidores DNS numa base de serviço de nuvem para substituir as definições de rede predefinido. No entanto, recomendamos que utilize o DNS de toda a rede quanto possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Pode colocar meu próprio sufixo DNS?
Não. Não é possível especificar um sufixo DNS personalizado para as suas VNets.

## <a name="connecting-virtual-machines"></a>Ligar máquinas virtuais

### <a name="can-i-deploy-vms-to-a-vnet"></a>Pode implementar VMs para uma VNet?
Sim. Todas as interfaces de rede (NIC) ligadas a uma VM implementada através do modelo de implementação Resource Manager devem estar ligadas a uma VNet. VMs implementadas através do modelo de implementação clássica, opcionalmente, podem ser ligadas a uma VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quais são os diferentes tipos de endereços IP que pode atribuição ao VMs?
* **Privados:** atribuídos a cada NIC dentro de cada VM. O endereço é atribuído a utilizar é o método estático ou dinâmico. Atribuição de endereços IP privados do intervalo que especificou nas definições de sub-rede da VNet. Recursos implementados através do modelo de implementação clássica são atribuídos a endereços IP privados, mesmo que não estão ligados a uma VNet. Um endereço IP privado atribuído com o método dinâmico permanecerá atribuído a um recurso, até que o recurso é eliminado (VMs ou serviço em nuvem ranhuras de implementação). Um endereço IP privado atribuído com o método dinâmico podem ser alteradas quando uma VM for reiniciada após ter sido no estado parado (desalocado). Um endereço IP privado atribuído com o método estático permanecerá atribuído a um recurso, até que o recurso é eliminado. Se precisar de Certifique-se de que o endereço IP privado para um recurso nunca é alterado até que o recurso é eliminado, atribua um endereço IP privado com o método estático.
* **Público:** opcionalmente atribuído a NICs anexados a VMs implementadas através do modelo de implementação Azure Resource Manager. O endereço pode ser atribuído com o método de alocação estático ou dinâmico. Instâncias de função de todas as VMs e serviços em nuvem implementadas através do modelo de implementação clássica existem dentro de um serviço em nuvem, que é atribuído um *dinâmica*, pública endereço IP virtual (VIP). Um público *estático* endereço IP, chamado um [endereço IP reservado](virtual-networks-reserved-public-ip.md), opcionalmente, pode ser atribuída como um VIP. Pode atribuir endereços IP públicos para VMs ou serviços em nuvem função instâncias individuais implementadas através do modelo de implementação clássica. Estes endereços são denominados [IP público de nível de instância (ILPIP](virtual-networks-instance-level-public-ip.md) endereços e podem ser atribuídos dinamicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Pode reservar um endereço IP privado para uma VM que irá criar numa altura posterior?
Não. Não é possível reservar um endereço IP privado. Se um endereço IP privado está disponível será atribuído a uma instância VM ou função pelo servidor DHCP. Essa VM pode ou não ser aquele que pretende que o endereço IP privado a atribuir à. No entanto, pode, alterar o endereço IP privado de uma VM já criado para qualquer endereço IP privado disponível.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Efetue a alteração de endereços IP privada para as VMs numa VNet?
Depende. Dinâmicos endereços IP privados continuam com uma VM até o respetivo parado (desalocado) ou eliminado. Endereços IP privados estáticos não são lançados de uma VM até serem eliminada.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Pode manualmente atribuição de endereços IP ao NICs no sistema de operativo VM?
Sim, mas não é recomendado. Alterar manualmente o endereço IP para uma NIC dentro do sistema de operativo de uma VM, potencialmente, levar a perda de conectividade para a VM se alterar o endereço IP atribuído a um NIC dentro da VM do Azure.

### <a name="what-happens-to-my-ip-addresses-if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system"></a>O que acontece a minha endereços IP se parar o um bloco de implementação do serviço em nuvem ou encerramento de uma VM a partir do sistema operativo?
Nada. Os endereços IP (VIP público, público e privado) permanecem atribuídos para a ranhura de implementação do serviço de nuvem ou VM. Endereços dinâmicos só são lançados se uma VM estiver parada (desalocada) ou eliminada, ou uma ranhura de implementação do serviço de nuvem é eliminada. Ao clicar no **parar** botão para uma VM no portal do Azure define o estado para parado (desalocado). Neste caso, a VM irá perder respetivos endereços IP.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Pode posso mover VMs de uma sub-rede para outra sub-rede numa VNet sem voltar a implementar?
Sim. Pode encontrar mais informações no [como mover uma VM ou instância de função para outra sub-rede](virtual-networks-move-vm-role-to-subnet.md) artigo.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Pode configurar um endereço MAC estático para a minha VM?
Não. Um endereço MAC não pode ser configurado estaticamente.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>O endereço MAC permanecer o mesmo para a minha VM depois de terem sido criadas?
Sim, o endereço de MAC foi alterada para uma VM implementada através do Gestor de recursos e os modelos de implementação clássica até serem eliminada. Anteriormente, o endereço de MAC foi libertado se a VM foi parada (desalocada), mas agora o endereço MAC é mantido, mesmo quando a VM se encontra no Estado desalocado.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso ligar à internet de uma VM numa VNet?
Sim. Todas as VMs e serviços em nuvem instâncias de função implementadas numa VNet podem ligar à Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Serviços do Azure que se ligam a VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Pode utilizar aplicações de Web do serviço de aplicações do Azure com uma VNet?
Sim. Pode implementar aplicações Web dentro de uma VNet com ASE (ambiente de serviço de aplicações). Todas as aplicações Web podem ligar e aceder aos recursos na sua VNet do Azure, se tiver uma ligação ponto a site configurada para a sua VNet em segurança. Para obter mais informações, veja os artigos seguintes:

* [Criar Web Apps num ambiente de serviço de aplicações](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Integrar a sua aplicação com uma Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md)
* [Utilizar a integração de VNet e as ligações híbridas com as Web Apps](../app-service/web-sites-integrate-with-vnet.md#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Pode implementar serviços em nuvem com funções web e de trabalho (PaaS) numa VNet?
Sim. (Opcionalmente), pode implementar instâncias de função de serviços em nuvem nas VNets. Para tal, especifique o nome da VNet e os mapeamentos de função/sub-rede na secção de configuração de rede da sua configuração de serviço. Não é necessário atualizar qualquer um dos seus binários.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Pode ligar uma Máquina Virtual escala definido (VMSS) para uma VNet?
Sim. Tem de ligar um VMSS para uma VNet.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Pode mover os meus serviços dentro ou fora do VNets?
Não. Não é possível mover serviços dentro ou fora do VNets. Terá de eliminar e voltar a implementar o serviço movê-lo para outra VNet.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>O que é o modelo de segurança para VNets?
As VNets estão completamente isoladas da outro e outros serviços alojados na infraestrutura do Azure. Uma VNet é um limite de fidedignidade.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Pode restringir o fluxo de tráfego de entrada ou saída aos recursos ligados à VNet?
Sim. Pode aplicar [grupos de segurança de rede](virtual-networks-nsg.md) a sub-redes individuais numa VNet, NICs ligado a uma VNet, ou ambos.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Pode implementar uma firewall entre os recursos ligados à VNet?
Sim. Pode implementar um [aplicação virtual de rede de firewall](https://azure.microsoft.com/en-us/marketplace/?term=firewall) de vários fornecedores através do Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Existe informações disponíveis sobre como proteger VNets?
Sim. Consulte o [descrição geral de segurança de rede de Azure](../security/security-network-overview.md) artigo para obter detalhes.

## <a name="apis-schemas-and-tools"></a>APIs, esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Pode gerir as VNets a partir do código?
Sim. Pode utilizar as APIs REST para VNets no [do Azure Resource Manager](https://msdn.microsoft.com/library/mt163658.aspx) e [clássico (gestão de serviço)](http://go.microsoft.com/fwlink/?LinkId=296833)) modelos de implementação.

### <a name="is-there-tooling-support-for-vnets"></a>Existe suporte de ferramentas para VNets?
Sim. Saiba mais sobre como utilizar:
- Portal do Azure para implementar as VNets através de [do Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) e [clássico](virtual-networks-create-vnet-classic-pportal.md) modelos de implementação.
- PowerShell para gerir as VNets implementadas através de [Resource Manager](/powershell/resourcemanager/azurerm.network/v3.1.0/azurerm.network.md) e [clássico](/powershell/module/azure/?view=azuresmps-3.7.0) modelos de implementação.
- O [interface de linha de comandos do Azure (CLI)](../virtual-machines/azure-cli-arm-commands.md#azure-network-commands-to-manage-network-resources) para gerir as VNets implementadas através de ambos os modelos de implementação.  

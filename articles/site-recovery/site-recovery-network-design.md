---
title: "Conceber a sua infraestrutura de rede para a recuperação de desastre com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo aborda considerações de design de rede para o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: jwhit
editor: 
ms.assetid: ce787731-d930-4f00-a309-e2fbc2e1f53b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: pratshar
ms.openlocfilehash: 5b6fb7bac852b29663866e99758241bd5a7ab59e
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="designing-your-network-for-disaster-recovery"></a>Conceber a sua rede para recuperação após desastre

Este artigo aborda considerações de rede ao utilizar [do Azure Site Recovery](site-recovery-overview.md) para desastre recuperação no local para o Azure ou para uma secundária-local no site. Concentra-se sobre a definição de intervalos de endereços IP e sub-redes após a ativação pós-falha para uma localização secundária.

## <a name="overview"></a>Descrição geral

Recuperação de sites é um serviço do Microsoft Azure que orquestra a proteção e recuperação de aplicações virtualizadas para fins de recuperação de desastre de continuidade de negócio (BCDR).

Um mundo de conectividade de 24/7, é importante manter a sua infraestrutura de empresas e aplicações de cópia de segurança e em execução. O objetivo do BCDR consiste em restaurar componentes falhados, para que a sua organização pode rapidamente retomar as operações normais. Desenvolver estratégias de recuperação de desastres para lidar com eventos pouco provável é muito difícil. Isto acontece porque a dificuldade em inerente de prever o futuro, especialmente para improbable eventos. E, devido ao custo de manutenção adequadas medidas de proteção contra far-reaching catastrophes elevado.

É fundamental para o planeamento de BCDR, um objetivo de tempo de recuperação (RTO) e o objetivo de ponto de recuperação (RPO) tem de ser definidas no seu plano BCDR. Quando um desastre strikes um centro de dados, pode rapidamente (baixa RTO) coloque online as respetivas máquinas virtuais replicadas localizadas no Centro de dados secundária ou Microsoft Azure com a perda de dados mínimo (RPO baixo).

Ativação pós-falha é possibilitada pela recuperação de sites, que inicialmente copia designadas máquinas virtuais a partir do Datacenter primário para o Centro de dados secundária ou para o Azure (dependendo do cenário) e, em seguida, atualiza periodicamente as réplicas. Durante o planeamento da infraestrutura, design de rede deve ser considerada como potencial constrangimento que possam impedir a partir da empresa de reunião RTO e objetivos do RPO.  

Quando os administradores estiver a planear implementar uma solução de recuperação após desastre, uma das perguntas principais no respetivos minds é como a máquina virtual estaria acessível depois de concluída a ativação pós-falha. Recuperação de site permite ao administrador escolha a rede à qual uma máquina virtual deverá ser ligada após a ativação pós-falha. Se o site primário é o Azure ou é um site no local gerido por um servidor VMM, isto é conseguido utilizando o mapeamento da rede. Saiba mais sobre [mapeamento da rede no Azure para o Azure DR](site-recovery-network-mapping-azure-to-azure.md) e [mapeamento da rede a partir do VMM](site-recovery-network-mapping.md)


Ao conceber a rede para o site de recuperação, o administrador tem duas opções:

* Utilize um intervalo de endereços IP diferentes para a rede no site de recuperação. Neste cenário, a máquina virtual após a ativação pós-falha irá obter um novo endereço IP e o administrador teria que efetuar uma atualização DNS. 
* Utilize o mesmo intervalo de endereços IP para a rede no site de recuperação. Em certos cenários, os administradores preferir manter os endereços IP que têm no site primário, mesmo após a ativação pós-falha. Num cenário normal, um administrador teria que atualizar as rotas para indicar a nova localização dos endereços IP. Mas, no cenário onde uma sub-rede de Stretch está implementada entre a primária e os sites de recuperação, a manter os endereços IP para as máquinas virtuais torna-se uma boa opção. Não é possível alongar uma sub-rede a partir de uma rede no local a uma rede do Azure ou entre duas redes do Azure.  

Quando os administradores estiver a planear implementar uma solução de recuperação após desastre, uma das perguntas principais no respetiva consideração é como as aplicações estarão acessível depois de concluída a ativação pós-falha. Aplicações modernas dependem quase sempre redes algumas grau, mover fisicamente, por isso, que um serviço de um site para outro representa um desafio de rede. Existem duas formas principais que este problema é resolvido em soluções de recuperação após desastre. A primeira abordagem é manter os endereços IP fixos. Apesar dos serviços de mover e os servidores de alojamento a ser em diferentes localizações físicas, aplicações demorar a configuração do endereço IP com os mesmos para a nova localização. A abordagem segundo envolve completamente alterar o endereço IP durante a transição para o site recuperado. Cada abordagem tem vários variações de implementação que se encontram-se resumidas abaixo.

Ao conceber a rede para o site de recuperação, o administrador tem duas opções:

## <a name="option-1-retain-ip-addresses"></a>Opção 1: Manter endereços IP
De uma perspetiva de processo de recuperação após desastre, utilizando o IP fixo endereços parece ser o método mais simples de implementar, mas tem um número de potenciais desafios, que, na prática, torna a abordagem de pelo menos, mais popular. O Azure Site Recovery fornece a capacidade de manter os endereços IP em todos os cenários. Antes de um decide manter IP, deve receber profundamente adequada para as restrições que impõe as capacidades de ativação pós-falha. Informe-nos analisar os fatores que podem ajudar a tomar uma decisão para manter os endereços IP, ou não. Isto pode ser conseguido de duas formas, utilizando uma sub-rede Stretch ou efetuar uma ativação pós-falha de sub-rede completa.

### <a name="stretched-subnet"></a>Sub-rede Stretch
Aqui a sub-rede é disponibilizada em simultâneo no site primário e localizações de DR. Termos simples significa pode mover um servidor e a respetiva configuração de IP (Layer 3) para o segundo site e a rede irá encaminhar o tráfego para a nova localização automaticamente. Este é trivial para lidar com uma perspetiva de servidor, mas tem vários desafios:

* De uma perspetiva de (camada de ligação de dados) de camada 2, necessita de equipamento de rede que pode gerir uma VLAN Stretch, mas isto tornou-se inferior de um problema, agora é amplamente disponível. O segundo e mais difícil problema é que alongar a VLAN o domínio de falhas potenciais é expandido para ambos os sites, essencialmente se tornar um ponto único de falha. Embora seja uma ocorrência improvável, tiver ocorrido que um storm difusão iniciada, mas não foi possível isolado. Estamos a ter encontrado opinions mistos sobre este problema último e ter visualizado várias implementações com êxito, bem como "não implementamos esta tecnologia aqui."
* Não é possível se estiver a utilizar o Microsoft Azure como o site de DR sub-rede Stretch.

### <a name="subnet-failover"></a>Ativação pós-falha de sub-rede
É possível implementar a ativação pós-falha de sub-rede desfrutar das vantagens da solução de sub-rede Stretch descrito acima sem alongar a sub-rede através de vários sites. Aqui determinada sub-rede estarão presente no Site 1 ou 2 do Site, mas nunca em ambos os sites em simultâneo. Para manter o espaço de endereços IP na eventualidade de ocorrer uma ativação pós-falha, é possível através de programação dispor para a infraestrutura de router mover as sub-redes de um site para outro. Num cenário de ativação pós-falha, que as sub-redes seriam movidos com associada protegido VMs. A principal desvantagem para esta abordagem é em caso de falha que tem de mover a sub-rede de toda. Esta situação pode ser OK, mas pode afetar a granularidade de ativação pós-falha.

Vamos examinar como uma empresa fictícias com o nome Contoso é capaz de replicar as respetivas VMs para uma localização de recuperação ao falhar ao longo de toda a sub-rede. Vamos primeiro vista de olhos como Contoso é capaz de gerir as suas sub-redes ao replicar as VMs entre duas localizações no local e, em seguida, vamos abordar como ativação pós-falha de sub-rede funciona quando [Azure é utilizado como o site de recuperação após desastre](#failover-to-azure).

#### <a name="fail-over-from-on-premises-to-azure"></a>Efetuar a ativação pós-falha no local do Azure 
O Azure Site Recovery permite ao Azure a ser utilizado como um site de recuperação após desastre para as máquinas virtuais.  

Vamos examinar um cenário onde uma empresa fictícias com o nome do Woodgrove Bank tem a infraestrutura no local que alojam as respetivas aplicações de linha de negócio e estão a alojar as aplicações móveis no Azure. Conectividade entre VMs do Banco Woodgrove nos servidores do Azure e no local é fornecida por um site para site (S2S) rede privada Virtual (VPN) ou o ExpressRoute. VPN de site a site permite a rede virtual do Woodgrove Bank no Azure para ser vistos como uma extensão de rede do Woodgrove Bank no local. Esta comunicação é ativada por VPN de site a site entre o limite do Banco Woodgrove e rede virtual do Azure. Agora Woodgrove pretende utilizar a recuperação de sites para replicar o respetivas cargas de trabalho em execução primário região do Azure para outra região do Azure. Esta opção satisfaça as necessidades de Woodgrove, o qual pretende uma opção de DR económica e tem capacidade para armazenar dados em ambientes de nuvem pública. Woodgrove tem de lidar com a configuração e aplicações que dependem hard-coded endereços IP, e, por isso, tem um requisito para manter os endereços IP para as aplicações após falha através do noutra região no Azure.

Woodgrove decidiu atribuir endereços IP do intervalo de endereços IP (172.16.1.0/24, 172.16.2.0/24) aos seus recursos em execução no Azure.

Para Woodgrove conseguir replicar as respetivas máquinas virtuais no Azure, mantendo os endereços IP, uma rede Virtual do Azure tem de ser criado. Deve ser uma extensão da rede no local para que as aplicações podem efetuar a ativação pós-falha do site no local para o Azure totalmente integrada. Azure permite-lhe adicionar conectividade VPN de site a site, bem como ponto a site para as redes virtuais criadas no Azure. Quando configurar a ligação de site a site, rede do Azure permite-lhe encaminhar o tráfego para a localização no local (Azure chama-lo da rede local) apenas se o intervalo de endereços IP é diferente do intervalo de endereços IP no local, porque não suporta o Azure alongar sub-redes.  Isto significa que, se tiver um 192.168.1.0/24 sub-rede no local, não é possível adicionar um 192.168.1.0/24 local da rede na rede do Azure. Isto é esperado porque o Azure não sabe que não existem sem VMs do Active Directory na sub-rede e de que a sub-rede está a ser criada apenas para fins de DR. Para conseguir corretamente encaminhar o tráfego de rede fora de uma rede Azure as sub-redes na rede e da rede local tem não estão em conflito.

![Antes da ativação pós-falha de sub-rede](./media/site-recovery-network-design/network-design7.png)

Antes da ativação pós-falha

Para ajudar a satisfazer os seus requisitos comerciais de Woodgrove, temos de implementar os fluxos de trabalho seguintes:

* Criar uma rede adicional, informe-nos chamá-lo a rede de recuperação, onde seria possível criar as máquinas virtuais de efetuada a ativação pós-falha.
* Para garantir que o IP para uma VM é mantido após uma ativação pós-falha, aceda ao separador Configurar em propriedades VM, especifique o IP mesmo que a VM possui no local e, em seguida, clique em Guardar. Quando a VM está a ativação pós-falha, o Azure Site Recovery atribuir o IP fornecido para a máquina virtual.

![Propriedades da rede](./media/site-recovery-network-design/network-design8.png)

Assim que a ativação pós-falha é acionada e as máquinas virtuais são criadas na rede de recuperação com o IP pretendido, a conectividade a esta rede pode ser estabelecida utilizando um [ligação Vnet a Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Se for necessário esta ação pode ser colocado em script.  Como é descrito na secção anterior sobre a ativação pós-falha de sub-rede, mesmo em caso de ativação pós-falha para o Azure, rotas teria de adequadamente ser modificados para refletir que 192.168.1.0/24 foi agora movido para o Azure.

![Após a ativação pós-falha de sub-rede](./media/site-recovery-network-design/network-design9.png)

Após a ativação pós-falha

Se não tiver uma rede' Azure' conforme mostrado na imagem acima. Pode criar uma ligação de VPN de site a site entre o 'Site primário' e a rede de recuperação após a ativação pós-falha.  


#### <a name="fail-over-to-a-secondary-on-premises-site"></a>Ativação pós-falha para um site secundário no local
Informe-nos analisar um cenário em pretenda manter o IP de cada uma das VMs e ativação pós-falha a sub-rede completa em conjunto. O site primário tem aplicações sejam executadas na sub-rede 192.168.1.0/24. Quando ocorre a ativação pós-falha, todas as máquinas virtuais que fazem parte desta sub-rede serão ativadas pós-falha para o site de recuperação e manter os seus endereços IP. Rotas terá adequadamente ser modificados para refletir o facto de que todas as máquinas virtuais que pertencem à sub-rede 192.168.1.0/24 agora movido para o site de recuperação.

Na ilustração seguinte as rotas entre o site primário e site de recuperação, terceiro site e site primário, terceiro site e site de recuperação terá de ser modificados adequadamente.

As imagens seguintes mostram as sub-redes antes da ativação pós-falha. Sub-rede 192.168.0.1/24 está ativo no Site primário antes da ativação pós-falha e fica ativa do Site de recuperação após a ativação pós-falha

![Antes da ativação pós-falha](./media/site-recovery-network-design/network-design2.png)

Antes da ativação pós-falha

A imagem abaixo mostra as redes e sub-redes após a ativação pós-falha.

![Após a ativação pós-falha](./media/site-recovery-network-design/network-design3.png)

Após a ativação pós-falha

Se o site secundário é no local e estiver a utilizar um servidor do VMM para geri-lo, em seguida, ao ativar a proteção para uma máquina virtual específica, a recuperação de sites irá alocar recursos de rede, de acordo com o seguinte fluxo de trabalho:

* Recuperação de site atribui um endereço IP para cada interface de rede na máquina virtual do conjunto de endereços IP estáticos definido na rede relevante para cada instância de VMM do System Center.
* Se o administrador define o mesmo conjunto de endereços IP para a rede no site de recuperação do conjunto de endereços IP da rede no site primário, ao alocar o endereço IP a máquina virtual de réplica, a recuperação de sites iria atribuir o mesmo endereço IP que que da máquina virtual primária.  O endereço IP é reservado no VMM, mas não definido como o endereço IP de ativação pós-falha do anfitrião Hyper-v. O endereço IP de ativação pós-falha num anfitrião Hyper-v estiver definido para apenas antes da ativação pós-falha.


Se o mesmo endereço IP não estiver disponível, a recuperação de sites aloca algum outro endereço IP disponível do conjunto de endereços IP definido.

Depois da VM está ativada para proteção, que pode utilizar o seguinte script de exemplo para verificar o IP que foi atribuído à máquina virtual. O mesmo IP deverá ser definido como IP de ativação pós-falha e atribuído à VM no momento da ativação pós-falha:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

> [!NOTE]
> O cenário em que as máquinas virtuais utilizar DHCP, a gestão de endereços IP é completamente fora do controlo do Site Recovery. Um administrador tem para se certificar de que o servidor DHCP que serve os endereços IP no site de recuperação pode servir do mesmo intervalo de que o site primário.
>
>



## <a name="option-2-changing-ip-addresses"></a>Opção 2: A alteração dos endereços IP
Esta abordagem parece ser atuais mais predominantes com base no tem visto. Assume a forma de alterar o endereço IP de cada VM que esteja envolvido na ativação pós-falha. Uma desvantagem desta abordagem requer que a rede de entrada para 'saber' que a aplicação que estava no IPx está agora em IPy. Mesmo se IPx e IPy nomes lógicos, as entradas de DNS, normalmente, tem de ser alteradas ou removida da cache em toda a rede e entradas em cache nas tabelas de rede tem de ser atualizado ou descarregados, por conseguinte, um período de inatividade foi ser visto consoante o modo como a infraestrutura DNS foi Configure. Estes problemas, podem ser atenuados utilizando valores TTL baixos no caso de aplicações de intranet e utilizando [Gestor de tráfego do Azure com a recuperação de Site](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/), para aplicações baseadas na internet

### <a name="changing-the-ip-addresses---illustration"></a>Alterar os endereços IP - ilustração
Informe-nos analisar o cenário em que estiver a planear utilizar IPs diferente entre o site primário e os sites de recuperação. No exemplo seguinte, também tem uma terceira do site de onde as aplicações alojadas no site primário ou de recuperação podem ser acedidos sites.

![IP diferentes - antes da ativação pós-falha](./media/site-recovery-network-design/network-design10.png)


Na imagem acima, existem algumas aplicações alojadas na sub-rede 192.168.1.0/24 de sub-rede no site primário e tenham sido configurados para detetar um no site de recuperação na sub-rede 172.16.1.0/24 após uma ativação pós-falha. Rotas de rede/ligações VPN foram configuradas corretamente para que todos os três sites podem aceder umas às outras.

Como a imagem abaixo mostra, após falha através de uma ou mais aplicações, serão restaurados na sub-rede de recuperação. Neste caso, iremos não são restrita para efetuar a ativação pós-falha de toda a sub-rede ao mesmo tempo. Não são necessárias alterações para reconfigurar as rotas de rede ou VPN. Algumas atualizações DNS e uma ativação pós-falha irão garantir que as aplicações permanecem acessíveis. Se o DNS está configurado para permitir atualizações dinâmicas, em seguida, as máquinas virtuais seria registar-se com as novas IP assim que for iniciado após uma ativação pós-falha.

![IP diferentes - após a ativação pós-falha](./media/site-recovery-network-design/network-design11.png)


Depois de falhar a ativação pós-falha da máquina virtual de réplica pode ter um endereço IP que não é o mesmo que o endereço IP da máquina virtual primária. Máquinas virtuais irá atualizar o servidor DNS que estão a utilizar após são iniciados. As entradas de DNS, normalmente, tem de ser alteradas ou removida da cache em toda a rede e entradas em cache nas tabelas de rede tem de ser atualizado ou descarregados, pelo que não é invulgar para ser confrontados com período de indisponibilidade enquanto estas alterações de estado de efetuar. Este problema pode ser mitigado ao:

* Utilizar baixos TTL valores para as aplicações de intranet.
* Utilizar o Azure tráfego do Configuration Manager com a recuperação de Site para aplicações baseadas na internet.
* Com o seguinte script no seu plano de recuperação para atualizar o servidor DNS para garantir uma atualização atempada (o script não é necessário se o registo de DNS dinâmicos estiver configurado)

        param(
        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

### <a name="changing-the-ip-addresses--disaster-recovery-to-azure"></a>Alterar o IP endereços – recuperação após desastre para o Azure
O [configuração do funcionamento em rede de infraestrutura do Microsoft Azure como um Site de recuperação após desastre](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) blogue explica como configurar a infraestrutura de rede do Azure necessária quando a retenção de endereços IP não é um requisito. Este começa com descrever a aplicação e, em seguida, observar como definir a segurança de rede no local e no Azure e, em seguida, e acaba com como efetuar uma ativação pós-falha de teste e uma ativação pós-falha planeada.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [mapeamento da rede](site-recovery-network-mapping.md).

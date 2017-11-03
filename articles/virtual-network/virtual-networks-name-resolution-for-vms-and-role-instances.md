---
title: "Resolução para VMs e instâncias de função"
description: "Cenários de resolução de nome para o IaaS do Azure, as soluções híbridas, entre os serviços de nuvem diferente, do Active Directory e utilizando o seu próprio servidor DNS "
services: virtual-network
documentationcenter: na
author: GarethBradshawMSFT
manager: carmonm
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2016
ms.author: telmos
ms.openlocfilehash: 479cf8cf358d0b242d8ce030d8639b493e4767d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="name-resolution-for-vms-and-role-instances"></a>Resolução de nomes para VMs e instâncias de função
Dependendo de como utilizar o Azure para alojar o IaaS, PaaS e soluções híbridas, se pretender permitir que as VMs e instâncias de função que criar para comunicar entre si. Embora esta comunicação pode ser feita através da utilização de endereços IP, é muito mais simples utilizar nomes que podem ser memorizados facilmente e não alteram. 

Quando precisam de resolver os nomes de domínio para endereços IP internos instâncias de função e as VMs alojadas no Azure, podem utilizar um dos dois métodos:

* [Resolução de nome fornecidos pelo Azure](#azure-provided-name-resolution)
* [Resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) (que pode reencaminhar consultas para os servidores DNS fornecidos pelo Azure) 

O tipo de resolução de nomes que utiliza depende de como as VMs e instâncias de função têm de comunicar entre si.

**A tabela seguinte ilustra os cenários e soluções de resolução de nome correspondente:**

| **Cenário** | **Solução** | **Sufixo** |
| --- | --- | --- |
| Resolução de nome entre instâncias de função ou VMs localizadas no mesmo serviço em nuvem ou de rede virtual |[Resolução de nome fornecidos pelo Azure](#azure-provided-name-resolution) |nome de anfitrião ou o FQDN |
| Resolução de nome entre instâncias de função ou VMs localizadas em redes virtuais diferentes |Gerida pelo cliente servidores DNS reencaminhamento consultas entre vnets para a resolução pelo Azure (proxy DNS).  Consulte [resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |Apenas FQDN |
| Resolução de nomes de computador e o serviço no local de instâncias de função ou VMs no Azure |Gerida pelo cliente servidores DNS (por exemplo, o controlador de domínio no local, controlador de domínio só de leitura local ou um DNS secundário sincronizada com êxito utilizando transferências de zona).  Consulte [resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |Apenas FQDN |
| Resolução de nomes de anfitrião do Azure a partir de computadores no local |Consultas de reencaminhar para um servidor de proxy DNS gerida pelo cliente na vnet correspondente, o servidor de proxy reencaminha consultas para o Azure para a resolução. Consulte [resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |Apenas FQDN |
| Inversa de DNS para IPs interno |[Resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |n/d |
| Resolução de nome entre VMs ou instâncias de função localizadas nos serviços de nuvem diferente, não numa rede virtual |Não aplicável. A conectividade entre VMs e instâncias de função nos serviços de nuvem diferente não é suportada fora de uma rede virtual. |n/d |

## <a name="azure-provided-name-resolution"></a>Resolução de nome fornecidos pelo Azure
Juntamente com a resolução de nomes DNS públicos, o Azure oferece resolução dos nomes internos para VMs e instâncias de função que residem dentro da mesma rede virtual ou serviço em nuvem.  VMs/instâncias num serviço em nuvem partilham o mesmo sufixo DNS (para que o nome de anfitrião autónomo é suficiente) mas numa nuvem diferente de redes virtuais clássicas serviços têm diferentes sufixos DNS para que o FQDN é necessária para resolver nomes entre os serviços de nuvem diferente.  Nas redes virtuais no modelo de implementação Resource Manager, o sufixo DNS é consistente entre a rede virtual (para que o FQDN não é necessário) e nomes DNS podem ser atribuídos a NICs e VMs. Apesar de resolução de nome fornecidos pelo Azure necessita de qualquer configuração, não é a opção adequada para todos os cenários de implementação, como mostrado na tabela acima.

> [!NOTE]
> No caso de funções web e de trabalho, também pode aceder os endereços IP internos de instâncias de função com base no número de nome e a instância de função utilizando a API de REST de gestão de serviço do Azure. Para obter mais informações, consulte [referência de API de REST de gestão de serviço](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features-and-considerations"></a>Funcionalidades e considerações
**Funcionalidades:**

* Facilidade de utilização: é necessária nenhuma configuração para poder utilizar a resolução de nome fornecidos pelo Azure.
* O serviço de resolução de nome fornecidos pelo Azure está altamente disponível, poupando a necessidade de criar e gerir clusters dos seus servidores DNS.
* Pode ser utilizado em conjunto com os seus próprios servidores DNS para resolver os nomes de anfitrião do Azure e no local.
* Resolução de nomes é fornecida entre instâncias de função/VMs no mesmo serviço em nuvem sem necessidade de um FQDN.
* Resolução de nomes é fornecida entre VMs em redes virtuais que utilizam o modelo de implementação Resource Manager, sem necessidade do FQDN. Redes virtuais no modelo de implementação clássica requerem o FQDN ao resolver os nomes de serviços de nuvem diferente. 
* Pode utilizar nomes de anfitriões que melhor descrevem as implementações, em vez de trabalhar com nomes gerado automaticamente.

**Considerações:**

* Não é possível modificar o sufixo DNS criados no Azure.
* Não é possível registar manualmente os seus próprios registos.
* WINS e NetBIOS não são suportados. (Não é possível ver as suas VMs no Explorador do Windows.)
* Os nomes de anfitrião tem de ser compatível com o DNS (tem de utilizar apenas 0-9, a-z e '-' e não pode começar nem terminar com um '-'. Consulte a secção de 3696 RFC 2.)
* O tráfego de consulta DNS está limitado para cada VM. Isto não deve afetar a maioria das aplicações.  Se a limitação do pedido é observado, certifique-se de que a colocação em cache do lado do cliente está ativada.  Para obter mais detalhes, consulte [ao obter o máximo partido do resolução de nome fornecidos pelo Azure](#Getting-the-most-from-Azure-provided-name-resolution).
* Apenas as VMs nos serviços de 180 nuvem primeiro estão registadas para cada rede virtual de um modelo de implementação clássica. Não é aplicável às redes virtuais em modelos de implementação do Resource Manager.

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obter o máximo partido do resolução de nome fornecidos pelo Azure
**A colocação em cache do lado do cliente:**

Nem todas as consultas DNS tem de ser enviadas através da rede.  Colocação em cache do lado do cliente ajuda a reduzir a latência e melhorar a maior resiliência às blips de rede ao resolver as consultas DNS periódicas a partir de uma cache local.  Registos DNS contêm uma Time-To-Live (TTL) que permite que a cache armazenar o registo para desde possíveis sem afetar a actualização de registo, pelo que a colocação em cache do lado do cliente é adequada para a maioria das situações.

A predefinição do cliente DNS do Windows tem uma cache DNS incorporada.  Alguns Linux distros não incluem a colocação em cache por predefinição, é recomendado que um adicionadas para cada VM com Linux (após verificar-se de que não é uma cache local já).

Há uma série de diferentes DNS colocação em cache pacotes disponíveis, por exemplo, dnsmasq, Eis os passos para instalar o dnsmasq a distros mais comuns:

* **Ubuntu (utiliza resolvconf)**:
  * Instale apenas o pacote de dnsmasq ("sudo apt get instalação dnsmasq").
* **SUSE (utiliza netconf)**:
  * instalar o pacote de dnsmasq ("sudo zypper instalação dnsmasq") 
  * Ativar o serviço de dnsmasq ("systemctl ativar dnsmasq.service") 
  * iniciar o serviço de dnsmasq ("systemctl início dnsmasq.service") 
  * Editar "/ etc/sysconfig/rede/configuração" e altere NETCONFIG_DNS_FORWARDER = "" para "dnsmasq"
  * Atualizar resolv.conf ("netconfig update") para definir a cache, como a resolução DNS local
* **OpenLogic (utiliza NetworkManager)**:
  * instalar o pacote de dnsmasq ("sudo yum instalação dnsmasq")
  * Ativar o serviço de dnsmasq ("systemctl ativar dnsmasq.service")
  * iniciar o serviço de dnsmasq ("systemctl início dnsmasq.service")
  * Adicionar "preceder nome-servidores domínio 127.0.0.1;" para "/etc/dhclient-eth0.conf"
  * Reinicie o serviço de rede ("serviço rede restart") para definir a cache, como a resolução DNS local

> [!NOTE]
> O pacote de 'dnsmasq' é apenas um dos muitos caches DNS disponíveis para o Linux.  Antes de o utilizar, verifique a respetiva adequação para as suas necessidades específicas e que não existem outro cache está instalado.
> 
> 

**Tentativas do lado do cliente:**

O DNS é principalmente um protocolo UDP.  Como o protocolo UDP não garante a entrega de mensagens, lógica de repetição é processada no próprio protocolo DNS.  Cada cliente DNS (sistema operativo) pode apresentem um lógica de repetição diferentes consoante a preferência criadores:

* Sistemas operativos Windows novamente depois de 1 segundo e, em seguida, novamente após outro 2, 4 e outro 4 segundos. 
* As repetições a configuração do Linux predefinida que se após 5 segundos.  Recomenda-se para alterar esta para repetir 5 vezes intervalos 1 segundo.  

Para verificar as definições atuais numa VM com Linux, 'cat /etc/resolv.conf' e observe a linha de 'Opções', por ex.:

    options timeout:1 attempts:5

O ficheiro resolv.conf, normalmente, é gerado para automática e não deve ser editado.  Os passos específicos para adicionar a linha de 'Opções' variam consoante distro:

* **Ubuntu** (utiliza resolvconf):
  * Adicione a linha de opções para ' / etc/resolveconf/resolv.conf.d/head' 
  * Execute 'resolvconf -u' ao atualizar
* **SUSE** (utiliza netconf):
  * Adicionar 'timeout:1 tentativas: 5' para o NETCONFIG_DNS_RESOLVER_OPTIONS = "" parâmetro de '/ etc/sysconfig/rede/configuração' 
  * Execute 'netconfig update' ao atualizar
* **OpenLogic** (utiliza NetworkManager):
  * Adicionar 'eco "opções timeout:1 tentativas: 5" ' para ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
  * Execute 'rede reinício do serviço' ao atualizar

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nomes utilizando o seu próprio servidor DNS
Existem várias situações em que as suas necessidades de resolução de nome poderão ir para além das funcionalidades fornecidas pelo Azure, por exemplo quando utilizar domínios do Active Directory ou quando necessitar de resolução de DNS entre redes virtuais (vnets).  Para estes cenários, o Azure oferece a capacidade para que possa utilizar os seus próprios servidores DNS.  

Servidores DNS dentro de uma rede virtual podem reencaminhar consultas DNS para resoluções de recursiva do Azure para resolver os nomes de anfitrião dentro da rede virtual.  Por exemplo, um domínio Controller (controlador de domínio) em execução no Azure pode responder às consultas DNS para os domínios e reencaminhar todas as outras consultas para o Azure.  Isto permite que as VMs ver os seus recursos no local (através de DC) e o hostnames fornecidos pelo Azure (através do reencaminhador).  Acesso para resoluções de recursiva do Azure é fornecido através de virtual IP 168.63.129.16.

Reencaminhamento de DNS também permite a resolução de DNS inter-vnet e permite que as máquinas no local resolver os nomes de anfitrião fornecidos pelo Azure.  Para resolver o nome do anfitrião da VM, o servidor DNS VM tem de residir na mesma rede virtual e de ser configurado para consultas de nome de anfitrião direta para o Azure.  Como o sufixo DNS for diferente em cada vnet, pode utilizar regras de reencaminhamento condicional para enviar consultas DNS para a vnet correta para a resolução.  A imagem seguinte mostra duas vnets e uma rede no local ao executar a resolução de DNS inter-vnet através deste método.  Um reencaminhador DNS de exemplo está disponível no [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Inter-vnet DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Ao utilizar a resolução de nome fornecidos pelo Azure, um sufixo DNS interno (*. internal.cloudapp.net) fornecido para cada VM utilizando o DHCP.  Isto permite que a resolução de nome de anfitrião, porque os registos de nome de anfitrião se encontram na zona internal.cloudapp.net.  Quando utilizar a sua própria solução de resolução de nome, o sufixo IDNS não é fornecido para VMs porque-interfere com outras arquiteturas DNS (por exemplo, cenários de associados a um domínio).  Em vez disso, fornecemos um marcador não está em funcionamento (reddog.microsoft.com).  

Se for necessário, é possível determinar o sufixo DNS interna utilizando o PowerShell ou a API:

* Para as redes virtuais em modelos de implementação do Resource Manager, o sufixo está disponível através de [cartão de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) recursos ou através do [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) cmdlet.    
* Modelos de implementação clássica, o sufixo está disponível através de [obter API de implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) chamar ou através do [Get-AzureVM-depurar](https://msdn.microsoft.com/library/azure/dn495236.aspx) cmdlet.

Se as consultas de reencaminhamento para o Azure não conforme as suas necessidades, terá de fornecer a sua própria solução DNS.  A solução DNS necessário:

* Fornecer a resolução de nome de anfitrião apropriado, por exemplo, através de [DDNS](virtual-networks-name-resolution-ddns.md).  Tenha em atenção que se utilizar DDNS poderá ter de desativar a eliminação de registos DNS como concessões DHCP do Azure são muito longo e a eliminação pode remover os registos DNS prematuramente. 
* Resolução recursiva adequadas para permitir a resolução de nomes de domínio externo.
* Estar acessível (TCP e UDP na porta 53) dos clientes que tem e de conseguir aceder à internet.
* Estar protegida contra acesso a partir da internet, a mitigar ameaças colocadas por agentes externos.

> [!NOTE]
> Para melhor desempenho, ao utilizar VMs do Azure como servidores DNS, IPv6 devem ser desativados e um [IP público de nível de instância](virtual-networks-instance-level-public-ip.md) deve ser atribuído a cada VM de servidor DNS.  Se optar por utilizar o Windows Server como servidor DNS, [neste artigo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fornece análises de desempenho adicionais e otimizações.
> 
> 

### <a name="specifying-dns-servers"></a>Especificar os servidores DNS
Ao utilizar os seus próprios servidores DNS, o Azure oferece a capacidade de especificar vários servidores DNS, por rede virtual ou por interface de rede (Resource Manager) / (clássica) do serviço em nuvem.  Servidores DNS especificados para uma interface de rede/serviço de nuvem obter precedência sobre as especificadas para a rede virtual.

> [!NOTE]
> Propriedades de ligação de rede, tais como o servidor DNS IPs, não deve ser editada diretamente no VMs do Windows, pode obter apagadas durante o serviço heal quando o adaptador de rede virtual obtém substituído. 
> 
> 

Ao utilizar o modelo de implementação Resource Manager, os servidores DNS podem ser especificados no Portal, API/modelos ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) ou o PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [ NIC](https://msdn.microsoft.com/library/mt619370.aspx)).

Quando utilizar o modelo de implementação clássica, servidores DNS para a rede virtual podem ser especificados no Portal ou [o *configuração de rede* ficheiro](https://msdn.microsoft.com/library/azure/jj157100).  Para serviços em nuvem, os servidores DNS especificados através de [o *a configuração do serviço* ficheiro](https://msdn.microsoft.com/library/azure/ee758710) ou do PowerShell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [!NOTE]
> Se alterar as definições de DNS para uma máquina virtual/virtual de rede que já tenha sido implementado, terá de reiniciar cada VM afetado para que as alterações entrem em vigor.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Modelo de implementação do Resource Manager:

* [Criar ou atualizar uma rede virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Criar ou atualizar uma placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [Novo-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [Novo AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

Modelo de implementação clássica:

* [Esquema de configuração do serviço do Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Esquema de configuração de rede virtual](https://msdn.microsoft.com/library/azure/jj157100)
* [Configurar uma rede Virtual, utilizando um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md) 


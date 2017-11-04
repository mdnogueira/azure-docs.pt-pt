---
title: "Opções de resolução de nome DNS de computadores virtuais Linux no Azure"
description: "Serviços de DNS de cenários para computadores virtuais Linux no IaaS do Azure, incluindo fornecidos de resolução de nome, o servidor DNS e traga a sua própria DNS externo no híbrida."
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: timlt
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: cc06ee9305b4d3034154a0825c1aea53fe446f80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opções de resolução de nomes DNS para computadores virtuais Linux no Azure
O Azure oferece resolução do nome DNS por predefinição para todas as máquinas virtuais que estão numa única rede virtual. Pode implementar a sua própria solução de resolução de nome DNS ao configurar os seus próprios serviços DNS no seu máquinas virtuais que aloja do Azure. Os cenários seguintes deverão ajudar a escolher aquele que funciona para a sua situação.

* [Resolução de nome fornecidos pelo Azure](#azure-provided-name-resolution)
* [Resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)

O tipo de resolução de nomes que utiliza depende de como as máquinas virtuais e instâncias de função têm de comunicar entre si.

A tabela seguinte ilustra os cenários e soluções de resolução de nome correspondente:

| **Cenário** | **Solução** | **Sufixo** |
| --- | --- | --- |
| Resolução de nome entre instâncias de função ou máquinas virtuais na mesma rede virtual |[Resolução de nome fornecidos pelo Azure](#azure-provided-name-resolution) |nome de anfitrião ou nome de domínio completamente qualificado (FQDN) |
| Resolução de nome entre instâncias de função ou máquinas virtuais em redes virtuais diferentes |Gerida pelo cliente servidores DNS que reencaminham consultas entre redes virtuais para a resolução pelo Azure (proxy DNS). Consulte [resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de serviço de instâncias de função ou máquinas virtuais no Azure e computadores no local |Gerida pelo cliente servidores DNS (por exemplo, o controlador de domínio no local, controlador de domínio só de leitura local ou um DNS secundário sincronizada com êxito utilizando transferências de zona). Consulte [resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| Resolução de nomes de anfitrião do Azure a partir de computadores no local |Consultas de reencaminhar para um servidor de proxy gerida pelo cliente DNS na rede virtual correspondente. O servidor de proxy reencaminha consultas para o Azure para a resolução. Consulte [resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server). |Apenas FQDN |
| Inversa de DNS para IPs interno |[Resolução de nomes utilizando o seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) |n/d |

## <a name="name-resolution-that-azure-provides"></a>Resolução de nome fornecidos pelo Azure
Juntamente com a resolução de nomes DNS públicos, o Azure oferece resolução dos nomes internos para máquinas virtuais e instâncias de função que estão na mesma rede virtual. Em redes virtuais que são baseadas no Azure Resource Manager, o sufixo DNS é consistente entre a rede virtual; o FQDN não é necessária. Nomes DNS podem ser atribuídos a máquinas virtuais e placas de interface de rede (NICs). Embora a resolução de nome fornecidos pelo Azure necessita de qualquer configuração, não é a opção adequada para todos os cenários de implementação, como mostrado na tabela anterior.

### <a name="features-and-considerations"></a>Funcionalidades e considerações
**Funcionalidades:**

* Não é necessária nenhuma configuração utilizem a resolução de nome fornecidos pelo Azure.
* O serviço de resolução de nome fornecidos pelo Azure está altamente disponível. Não precisa de criar e gerir clusters dos seus servidores DNS.
* O serviço de resolução de nome fornecidos pelo Azure pode ser utilizado, juntamente com os seus próprios servidores DNS para resolver os nomes de anfitrião do Azure e no local.
* Resolução de nomes é fornecida entre máquinas virtuais nas redes virtuais sem necessidade do FQDN.
* Pode utilizar nomes de anfitriões que melhor descrevem as implementações em vez de trabalhar com nomes gerado automaticamente.

**Considerações:**

* Não é possível modificar o sufixo DNS que o Azure cria.
* Não é possível registar manualmente os seus próprios registos.
* WINS e NetBIOS não são suportados.
* Os nomes de anfitrião tem de ser compatível com o DNS.
    Nomes têm de utilizar apenas 0-9, a-z, e '-', e não pode começar nem terminar com um '-'. Consulte RFC 3696 secção 2.
* O tráfego de consulta DNS está limitado para cada máquina virtual. Limitação não deve afetar a maioria das aplicações.  Se a limitação do pedido é observado, certifique-se de que a colocação em cache do lado do cliente está ativada.  Para obter mais informações, consulte [ao obter o máximo partido do resolução de nome fornecidos pelo Azure](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Obter o máximo da resolução do nome que Azure fornece
**A colocação em cache do lado do cliente:**

Algumas consultas DNS não são enviadas através da rede. Colocação em cache do lado do cliente ajuda a reduzir a latência e melhorar a maior resiliência às inconsistências de rede ao resolver as consultas DNS periódicas a partir de uma cache local. Registos DNS contêm uma Time-To-Live (TTL), que permite que a cache armazenar o registo para desde possíveis sem afetar a actualização de registo. Como resultado, a colocação em cache do lado do cliente é adequada para a maioria das situações.

Algumas distribuições de Linux não incluem a colocação em cache por predefinição. Recomendamos que adicione uma cache a cada máquina virtual do Linux, depois de verificar a que não é uma cache local já.

Estão disponíveis várias DNS diferentes pacotes, tais como dnsmasq, a colocação em cache. Eis os passos para instalar dnsmasq sobre as distribuições mais comuns:

**Ubuntu (utiliza resolvconf)**
  * Instale o pacote de dnsmasq ("sudo apt get instalação dnsmasq").

**SUSE (utiliza netconf)**:
1. Instale o pacote de dnsmasq ("sudo zypper instalação dnsmasq").
2. Ative o serviço de dnsmasq ("systemctl ativar dnsmasq.service").
3. Inicie o serviço de dnsmasq ("systemctl início dnsmasq.service").
4. Editar "/ etc/sysconfig/rede/configuração" e alterar NETCONFIG_DNS_FORWARDER = "" para "dnsmasq".
5. Atualize resolv.conf ("netconfig update") para definir a cache, como a resolução DNS local.

**CentOS pelo Software de Wave não autorizado (anteriormente OpenLogic; utiliza NetworkManager)**
1. Instale o pacote de dnsmasq ("sudo yum instalação dnsmasq").
2. Ative o serviço de dnsmasq ("systemctl ativar dnsmasq.service").
3. Inicie o serviço de dnsmasq ("systemctl início dnsmasq.service").
4. Adicionar "preceder nome-servidores domínio 127.0.0.1;" para "/etc/dhclient-eth0.conf".
5. Reinicie o serviço de rede ("serviço rede restart") para definir a cache, como a resolução DNS local

> [!NOTE]
> : O pacote 'dnsmasq' é apenas uma destas caches DNS muitos que estão disponíveis para Linux. Antes de o utilizar, verifique a respetiva adequação para as suas necessidades e outro cache não está instalado.
>
>

**Tentativas de lado do cliente**

O DNS é principalmente um protocolo UDP. Porque o protocolo UDP não garante a entrega de mensagens, o próprio protocolo DNS processa a lógica de repetição. Cada cliente DNS (sistema operativo) pode apresentem um lógica de repetição diferentes consoante preferência o criador:

* Sistemas operativos Windows novamente depois de um segundo e, em seguida, novamente após outro duas, quatro e outro quatro segundos.
* As repetições a configuração do Linux predefinida que se após cinco segundos.  Deve alterar isto para repetir cinco vezes em intervalos de um segundo.  

Para verificar as definições atuais numa máquina virtual Linux, 'cat /etc/resolv.conf' e verifique o registo de linha 'Opções', por exemplo:

    options timeout:1 attempts:5

O ficheiro de resolv.conf é gerado automaticamente e não deve ser editado. Os passos específicos que adicionar a linha de 'Opções' variam consoante a distribuição:

**Ubuntu** (utiliza resolvconf)
1. Adicione a linha de opções para ' / etc/resolveconf/resolv.conf.d/head'.
2. Execute 'resolvconf -u' para atualizar.

**SUSE** (utiliza netconf)
1. Adicionar 'timeout:1 tentativas: 5' para o NETCONFIG_DNS_RESOLVER_OPTIONS = "" parâmetro de '/ etc/sysconfig/rede/configuração'.
2. Execute 'netconfig update' para atualizar.

**CentOS pelo Software de Wave não autorizado (anteriormente OpenLogic)** (utiliza NetworkManager)
1. Adicionar 'eco "opções timeout:1 tentativas: 5" ' para ' / etc/NetworkManager/dispatcher.d/11-dhclient'.
2. Execute o reinício de rede de serviço para atualizar.

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nomes utilizando o seu próprio servidor DNS
Necessidades de resolução de nome poderão ir além da, as funcionalidades que o Azure oferece. Por exemplo, poderá necessitar de resolução de DNS entre redes virtuais. Para este cenário, pode utilizar os seus próprios servidores DNS.  

Servidores DNS dentro de uma rede virtual podem reencaminhar consultas DNS para resoluções de recursiva do Azure para resolver os nomes de anfitrião que estão na mesma rede virtual. Por exemplo, um servidor DNS que é executada no Azure pode responder às consultas DNS para os seus próprios ficheiros de zona DNS e reencaminhar todas as outras consultas para o Azure. Esta funcionalidade permite que as máquinas virtuais Consulte ambas as suas entradas nos seus ficheiros de zona e os nomes de anfitrião que o Azure oferece (através do reencaminhador). Acesso para as resoluções de recursiva do Azure é fornecido através de virtual IP 168.63.129.16.

Reencaminhamento de DNS também permite a resolução de DNS entre redes virtuais e permite que as máquinas no local resolver os nomes de anfitrião que o Azure oferece. Para resolver o nome de anfitrião de uma máquina virtual, a máquina de virtual do servidor DNS tem de residir na mesma rede virtual e de ser configurada para consultas de nome de anfitrião direta para o Azure. Porque o sufixo DNS é diferente em cada rede virtual, pode utilizar regras de reencaminhamento condicional para enviar consultas DNS para a rede virtual correta para a resolução. A imagem seguinte mostra duas redes virtuais e uma rede no local ao executar a resolução de DNS entre redes virtuais através deste método:

![Resolução de DNS entre redes virtuais](./media/azure-dns/inter-vnet-dns.png)

Quando utilizar a resolução de nome fornecidos pelo Azure, o sufixo DNS interno é fornecido para cada máquina virtual através da utilização de DHCP. Quando utilizar a sua própria solução de resolução de nome, este sufixo não é fornecido para máquinas virtuais porque o sufixo interfere com outras arquiteturas DNS. Para fazer referência aos máquinas através do FQDN, ou para configurar o sufixo na suas máquinas virtuais, pode utilizar o PowerShell ou a API para determinar o sufixo:

* Para as redes virtuais que são geridos pelo Gestor de recursos do Azure, o sufixo está disponível através de [cartão de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) recursos. Também pode executar o `azure network public-ip show <resource group> <pip name>` comando para apresentar os detalhes da sua IP público, que inclui o FQDN do NIC.

Se as consultas de reencaminhamento para o Azure não conforme as suas necessidades, terá de fornecer a sua própria solução DNS.  A solução DNS tem de:

* Fornecer a resolução de nome de anfitrião adequado, por exemplo [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Se utilizar DDNS, poderá ter de desativar a eliminação do registo DNS. Concessões DHCP do Azure são muito longos e eliminação poderá remover registos DNS prematuramente.
* Resolução recursiva adequadas para permitir a resolução de nomes de domínio externo.
* Estar acessível (TCP e UDP na porta 53) dos clientes que tem e de conseguir aceder à Internet.
* Estar protegida contra acesso a partir da Internet para mitigar ameaças colocadas por agentes externos.

> [!NOTE]
> Para melhor desempenho, quando utilizar máquinas virtuais em servidores de DNS do Azure, desative o IPv6 e atribuir um [IP público de nível de instância](../../virtual-network/virtual-networks-instance-level-public-ip.md) para cada máquina de virtual do servidor DNS.  
>
>

---
title: "Através do DNS dinâmico para registar os nomes de anfitrião"
description: "Esta página fornece detalhes sobre como configurar o DNS dinâmico para registar os nomes de anfitrião no seus próprios servidores DNS."
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: 
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
ms.openlocfilehash: 440a062e5fff73526b2d77d7d0a7c52ca72a66f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Através do DNS dinâmico para registar os nomes de anfitrião no servidor DNS
[O Azure oferece resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md) para máquinas virtuais (VMs) e instâncias de função. No entanto, quando a resolução do nome tem de passar para além dos fornecidos pelo Azure, pode fornecer os seus próprios servidores DNS. Isto dá-lhe a capacidade para personalizar a sua solução DNS para se adequarem às suas necessidades específicas. Por exemplo, precisa de aceder a recursos no local através do seu controlador de domínio do Active Directory.

Quando os servidores DNS personalizados estão alojados as VMs do Azure pode reencaminhar as consultas de nome de anfitrião para a mesma vnet para o Azure para resolver os nomes de anfitrião. Se não pretender utilizar esta rota, pode registar os nomes de anfitrião VM no seu servidor DNS através do DNS dinâmico.  Azure não tem a capacidade (por exemplo, credenciais) para criar diretamente os registos nos servidores DNS, pelo que disposições alternativas, muitas vezes, são necessários. Seguem-se alguns cenários comuns com alternativas.

## <a name="windows-clients"></a>Clientes Windows
Os clientes do Windows não-associados a um domínio a tentativa de atualizações de DNS dinâmicos (DDNS) não segura quando estes arrancam ou quando muda do endereço IP. O nome DNS é o nome de anfitrião e o sufixo DNS primário. Azure deixa o sufixo DNS primário em branco, mas pode definir esta opção na VM, através de [IU](https://technet.microsoft.com/library/cc794784.aspx) ou [utilizando Automatização](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Os clientes do Windows associados a um domínio registar os respetivos endereços IP com o controlador de domínio utilizando o DNS dinâmico segura. O processo de associação de domínio define o sufixo DNS primário no cliente e cria e mantém a relação de confiança.

## <a name="linux-clients"></a>Clientes do Linux
Clientes Linux, geralmente, não registar-se com o servidor DNS no arranque, assumem que o servidor de DHCP não-lo. Servidores DHCP do Azure não têm a capacidade ou credenciais de registo dos registos no seu servidor DNS.  Pode utilizar uma ferramenta chamada *nsupdate*, que está incluído no pacote de enlace, para enviar DNS dinâmico de atualizações. Porque está padronizado ao protocolo DNS dinâmico, pode utilizar *nsupdate* , mesmo quando não utilizar enlace no servidor DNS.

Pode utilizar os hooks que são fornecidos pelo cliente de DHCP para criar e manter a entrada de nome de anfitrião no servidor DNS. Durante o ciclo de DHCP, o cliente executa os scripts no */etc/dhcp/dhclient-exit-hooks.d/*. Isto pode ser utilizado para registar o novo endereço IP utilizando *nsupdate*. Por exemplo:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        
        

Também pode utilizar o *nsupdate* comando para efetuar atualizações de DNS dinâmicos seguras. Por exemplo, quando estiver a utilizar um servidor DNS de enlace, um par de chaves públicas-privadas é [gerado](http://linux.yyz.us/nsupdate/).  O servidor DNS [configurado](http://linux.yyz.us/dns/ddns-server.html) com a parte pública da chave, para que as TI podem verificar a assinatura no pedido. Tem de utilizar o *-k* opção de fornecer o par de chaves para *nsupdate* na ordem do DNS dinâmico actualizar o pedido ser assinado.

Quando estiver a utilizar um servidor DNS do Windows, pode utilizar a autenticação Kerberos com o *-g* parâmetro *nsupdate* (não está disponível na versão Windows *nsupdate*). Para tal, utilize *kinit* ao carregar as credenciais (por exemplo, a partir de um [keytab ficheiro](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Em seguida, *nsupdate -g* selecionará as credenciais da cache.

Se for necessário, pode adicionar um sufixo de procura DNS para as suas VMs. O sufixo DNS especificado no */etc/resolv.conf* ficheiro. A maioria das Linux distros gerir automaticamente o conteúdo deste ficheiro, por isso, normalmente, não é possível editá-lo. No entanto, pode substituir o sufixo, utilizando o cliente DHCP *substituem* comando. Para tal, no */etc/dhcp/dhclient.conf*, adicionar:

        supersede domain-name <required-dns-suffix>;


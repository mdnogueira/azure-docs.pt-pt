---
title: Configurar DHCPv6 para VMs com Linux | Microsoft Docs
description: Como configurar DHCPv6 para VMs com Linux.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móveis, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 84558cb6e3a5524969f590eb0272a64ad8839ab5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-dhcpv6-for-linux-vms"></a>Configurar DHCPv6 para VMs do Linux

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Algumas das imagens da máquina virtual do Linux no Azure Marketplace não dispõe de DHCPv6 configurado por predefinição. Para suportar IPv6, DHCPv6 tem de ser configurado no dentro de distribuição de SO Linux que está a utilizar. Diferentes formas de configurar DHCPv6 porque utilizam diferentes pacotes de ter diversas distribuições de Linux.

> [!NOTE]
> Imagens do SUSE Linux e CoreOS recentes no Azure Marketplace tem sido previamente configuradas com DHCPv6. Não existem alterações adicionais são necessárias quando utilizar essas imagens.

Este documento descreve como ativar DHCPv6 para que a máquina virtual do Linux obtém um endereço IPv6.

> [!WARNING]
> Incorretamente editar ficheiros de configuração de rede pode fazer com que perca o acesso de rede para a VM. Recomendamos que teste as alterações de configuração nos sistemas de não produção. As instruções neste artigo foram testadas nas versões mais recentes das imagens de Linux no Azure Marketplace. Consulte a documentação para a versão específica do Linux para obter instruções mais detalhadas.

## <a name="ubuntu"></a>Ubuntu

1. Edite o ficheiro `/etc/dhcp/dhclient6.conf` e adicione a seguinte linha:

        timeout 10;

2. Edite a configuração de rede para a interface eth0 com a seguinte configuração:

   * No **Ubuntu 12.04 e 14.04**, edite o ficheiro`/etc/network/interfaces.d/eth0.cfg`
   * No **Ubuntu 16.04**, edite o ficheiro`/etc/network/interfaces.d/50-cloud-init.cfg`

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Edite o ficheiro `/etc/dhcp/dhclient6.conf` e adicione a seguinte linha:

        timeout 10;

2. Edite o ficheiro `/etc/network/interfaces` e adicione a seguinte configuração:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. Edite o ficheiro `/etc/sysconfig/network` e adicione o seguinte parâmetro:

        NETWORKING_IPV6=yes

2. Edite o ficheiro `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione os dois parâmetros seguintes:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

As imagens recentes SLES e openSUSE no Azure tem sido previamente configuradas com DHCPv6. Não existem alterações adicionais são necessárias quando utilizar essas imagens. Se tiver uma VM com base numa imagem SUSE anterior ou personalizada, utilize os seguintes passos:

1. Instalar o `dhcp-client` do pacote, se necessário:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edite o ficheiro `/etc/sysconfig/network/ifcfg-eth0` e adicione o seguinte parâmetro:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE bissexto

As imagens recentes SLES e openSUSE no Azure tem sido previamente configuradas com DHCPv6. Não existem alterações adicionais são necessárias quando utilizar essas imagens. Se tiver uma VM com base numa imagem SUSE anterior ou personalizada, utilize os seguintes passos:

1. Edite o ficheiro `/etc/sysconfig/network/ifcfg-eth0` e substitua este parâmetro

        #BOOTPROTO='dhcp4'

    com o seguinte valor:

        BOOTPROTO='dhcp'

2. Adicione o seguinte parâmetro para `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Recentes CoreOS imagens no Azure tem sido previamente configuradas com DHCPv6. Não existem alterações adicionais são necessárias quando utilizar essas imagens. Se tiver uma VM com base numa imagem de CoreOS anterior ou personalizada, utilize os seguintes passos:

1. Edite o ficheiro`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renove o endereço IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```

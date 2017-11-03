---
title: "Validar o débito da VPN a uma rede Virtual do Microsoft Azure | Microsoft Docs"
description: "O objetivo deste documento é ajudar a um utilizador validar o débito de rede a partir dos respetivos recursos no local para uma máquina virtual do Azure."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2017
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 3a1a6e2acd2ff40c2b35a6099f8a9fc7eb104bbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Como validar o débito da VPN a uma rede virtual

Permite-lhe criar seguro uma ligação de gateway VPN, conectividade entre a rede Virtual no Azure e no local em vários locais infraestrutura de TI.

Este artigo mostra como validar o débito de rede de recursos no local a uma máquina virtual do Azure (VM). Também fornece orientações de resolução de problemas.

>[!NOTE]
>Este artigo destina-se para ajudar a diagnosticar e corrigir problemas comuns. Se não for possível resolver o problema utilizando as seguintes informações [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Descrição geral

A ligação de gateway VPN envolve os seguintes componentes:

- Dispositivo VPN no local (ver uma lista de [validado dispositivos VPN)](vpn-gateway-about-vpn-devices.md#devicetable).
- Internet pública
- Gateway VPN do Azure
- VM do Azure

O diagrama seguinte mostra a conectividade lógica de uma rede no local a uma rede virtual do Azure através de VPN.

![Conectividade de cliente rede lógica à rede MSFT através da VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Calcular a entrada/saída esperada máxima

1.  Determine os requisitos de débito de linha de base da sua aplicação.
2.  Determine os limites de débito do gateway de VPN do Azure. Para obter ajuda, consulte a secção "Débito agregado por tipo de SKU e a VPN" [planeamento e design para o Gateway de VPN](vpn-gateway-plan-design.md).
3.  Determinar o [orientações de débito de VM do Azure](../virtual-machines/virtual-machines-windows-sizes.md) para o tamanho da VM.
4.  Determine a largura de banda do fornecedor de serviços Internet (ISP).
5.  Calcular o débito esperado - menos largura de banda de Internet (ISP VM, Gateway,) * 0.8.

Se o débito calculado não cumpre os requisitos de débito de linha de base da sua aplicação, terá de aumentar a largura de banda do recurso que é identificado como valor. Redimensionar um Gateway de VPN do Azure, consulte [alterar um SKU de gateway](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Redimensionar uma máquina virtual, consulte [redimensionar uma VM](../virtual-machines/virtual-machines-windows-resize-vm.md). Se não ocorrerem esperada largura de banda de Internet, também poderá contactar o seu ISP.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Validar o débito de rede utilizando ferramentas de desempenho

Esta validação deve ser efetuada durante o horário de pico, como saturação de débito de túnel VPN durante o teste não atribua resultados precisos.

A ferramenta que utilizamos para este teste é iPerf, que funciona no Windows e Linux e tem os modos de cliente e servidor. Está limitado a 3 Gbps para VMs do Windows.

Esta ferramenta executa quaisquer operações de leitura/escrita no disco. Produz apenas tráfego TCP gerado automaticamente um ponto para outro. Gerou com base na experimentação que mede a largura de banda disponível entre os nós cliente e servidor de estatísticas. Ao testar entre dois nós, um atua como o servidor e os outros como um cliente. Depois deste teste está concluído, recomendamos que Inverte as funções para testar a ambos os carregar e transferir o débito em ambos os nós.

### <a name="download-iperf"></a>Transferir iPerf
Transferir [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Para obter mais informações, consulte [iPerf documentação](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Os produtos de terceiros que este artigo aborda são fabricados por empresas que são independentes da Microsoft. Microsoft não faz nenhuma garantia, implícita ou, caso contrário, sobre o desempenho ou a fiabilidade destes produtos.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Executar iPerf (iperf3.exe)
1. Ative uma regra NSG/ACL a permitir o tráfego (para o endereço IP público teste numa VM do Azure).

2. Em ambos os nós, ative uma exceção de firewall para a porta 5001.

    **Windows:** execute o seguinte comando como administrador:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Para remover a regra quando o teste estiver concluída, execute este comando:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Linux do Azure:** permissiva firewalls de ter imagens de Linux do Azure. Se existir uma aplicação à escuta numa porta, o tráfego é permitido através de. Imagens personalizadas que são protegidas podem ter portas abertas explicitamente. Firewalls de camada de SO Linux comuns incluem `iptables`, `ufw`, ou `firewalld`.

3. No nó de servidor, mude para o diretório onde iperf3.exe é extraído. Em seguida, executar iPerf no modo de servidor e configurá-lo para escutar numa porta 5001 como os seguintes comandos:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. No nó de cliente, mude para o diretório onde iperf ferramenta é extraída e, em seguida, execute o seguinte comando:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    O cliente é inducing tráfego na porta 5001 para o servidor para 30 segundos. O sinalizador '-P ' que indica que estamos a utilizar 32 ligações simultâneas para o nó de servidor.

    O ecrã seguinte mostra a saída neste exemplo:

    ![Saída](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (OPCIONAL) Para manter os resultados de teste, execute este comando:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Depois de concluir os passos anteriores, execute os mesmos passos com as funções invertidas, para que o nó de servidor serão agora o cliente e vice-versa.

## <a name="address-slow-file-copy-issues"></a>Resolver problemas de cópia de ficheiros lenta
Pode deparar-se ficheiro lento coping quando utilizar o Explorador do Windows ou a arrastar e largar através de uma sessão do RDP. Este problema é normalmente devido a um ou ambos os seguintes fatores:

- Aplicações de cópia de ficheiros, como o Explorador do Windows e RDP, não utilizam vários threads ao copiar ficheiros. Para um melhor desempenho, utilize uma aplicação de cópia de ficheiros com vários threads, como [Richcopy](https://technet.microsoft.com/en-us/magazine/2009.04.utilityspotlight.aspx) para copiar ficheiros através da utilização de 32 ou 16 threads. Para alterar o número de threads para cópia de ficheiros no Richcopy, clique em **ação** > **copiar opções** > **cópia do ficheiro**.<br><br>
![Problemas de cópia de ficheiros lenta](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Velocidade de leitura/escrita do disco VM insuficiente. Para obter mais informações, consulte [resolução de problemas de armazenamento do Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interface com acesso externo dispositivo no local
Se o dispositivo VPN no local endereço IP de acesso à Internet está incluído no [rede local](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definição no Azure, pode deparar-se impossibilidade de apresentada desliga a VPN, esporádicas ou problemas de desempenho.

## <a name="checking-latency"></a>A verificação de latência
Utilize tracert para rastreio para o dispositivo de limite do Microsoft Azure para determinar se existem quaisquer atrasos exceder os 100 ms entre saltos.

A partir da rede no local, execute *tracert* para o VIP do Gateway do Azure ou da VM. Assim que só vê * devolvido, sabe que atingiu o limite do Azure. Quando vir nomes DNS que incluem "MSN" devolvido, sabe que atingiu a estrutura principal Microsoft.<br><br>
![A verificação de latência](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações ou ajuda, consulte as ligações seguintes:

- [Otimizar o débito de rede para máquinas virtuais do Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

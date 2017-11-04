---
title: "Resolução de problemas de conectividade entre as VMs do Azure | Microsoft Docs"
description: Saiba como resolver problemas de conectividade entre as VMs do Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2017
ms.author: genli
ms.openlocfilehash: 3011ef7eced5a24ba07d06e2db2f5e4d344b94de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Resolução de problemas de conectividade entre as VMs do Azure

Podem ocorrer problemas de conectividade entre máquinas de virtuais (VMs) do Azure. Este artigo fornece os passos de resolução de problemas para o ajudar a resolver este problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma

Uma que VM do Azure não é possível ligar a outra VM do Azure.

## <a name="troubleshooting-guidance"></a>Orientação na resolução de problemas 

1. [Verifique se a NIC está configurada incorretamente](#step-1-check-whether-nic-is-misconfigured)
2. [Verifique se o tráfego de rede é bloqueado por NSG ou UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Verifique se o tráfego de rede é bloqueado pela firewall da VM](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Verifique se VM aplicação ou serviço está a escutar na porta](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Verifique se o problema é causado por realizar o SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Verifique se o tráfego está bloqueado por ACLs para as VMS clássicas](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Verifique se o ponto final é criado para as VMS clássicas](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Tente estabelecer ligação a uma partilha de rede VM](#step-8-try-to-connect-to-a-vm-network-share)
9. [Verifique a conectividade do Inter-Vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Siga estes passos para resolver o problema. Depois de completar cada passo, verifique se o problema está resolvido. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Passo 1: Verificar se a NIC está configurada incorretamente

Siga os passos no [como repor o interface de rede para a VM do Windows Azure](../virtual-machines/windows/reset-network-interface.md). 

Se o problema ocorrer depois de modificar a interface de rede (NIC), siga estes passos:

**Vários NICs VMs**

1. Adicionar uma NIC.
2. Resolva os problemas no NIC incorreto ou remova o NIC incorreto.  Em seguida, adicione novamente o NIC.

Para obter mais informações, consulte [interfaces de rede para adicionar ou remover provenientes de máquinas virtuais](virtual-network-network-interface-vm.md).

**VM NIC único** 

- [Reimplementar a VM do Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Reimplementar a VM com Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Passo 2: Verificar se o tráfego de rede é bloqueado por NSG ou UDR

Utilize [verificar do fluxo de rede do observador do IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) e [NSG fluxo registo](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se existe um grupo de segurança de rede (NSG) ou definidas pelo utilizador rota (UDR) que é interferir com o fluxo de tráfego.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Passo 3: Verificar se o tráfego de rede é bloqueado pela firewall da VM

Desativar a firewall e, em seguida, testar o resultado. Se o problema for resolvido, verifique as definições da firewall e, em seguida, volte a ativar a firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Passo 4: Verificar se VM aplicação ou serviço está a escutar na porta

Pode utilizar um dos seguintes métodos para verificar se a aplicação VM ou o serviço está a escutar na porta.

- Execute os seguintes comandos para verificar se o servidor está a escutar nessa porta.

**VM do Windows**

    netstat –ano

**VM do Linux**

    netstat -l

- Execute o **telnet** comando na máquina virtual automaticamente para a porta de teste. Se o teste falhar, a aplicação ou serviço não está configurado para escutar nessa porta.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Passo 5: Verificar se o problema é causado por realizar o SNAT

Em alguns cenários, a VM é colocada atrás de uma solução de balanceamento de carga que tem uma dependência de recursos fora do Azure. Nestes cenários, se surgirem problemas de ligação intermitente, o problema pode ser causado por [esgotamento de porta de realizar o SNAT](../load-balancer/load-balancer-outbound-connections.md). Para resolver o problema, crie um VIP (ou ILPIP para clássico) para cada VM que está atrás do Balanceador de carga e proteger com o NSG ou ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Passo 6: Verificar se o tráfego está bloqueado por ACLs para as VMS clássicas

Uma lista de controlo de acesso (ACL) fornece a capacidade de forma seletiva permitir ou negar o tráfego para um ponto final da máquina virtual. Para obter mais informações, consulte [gerir ACL num ponto final](../virtual-machines/windows/classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Passo 7: Verificar se o ponto final é criado para as VMS clássicas

Todas as VMs que criar no Azure utilizando o modelo de implementação clássica automaticamente podem comunicar através de um canal de rede privada com outras máquinas virtuais no mesmo serviço em nuvem ou de rede virtual. No entanto, os computadores em outras redes virtuais requerem pontos finais para direcionar o tráfego de rede de entrada para uma máquina virtual. Para obter mais informações, consulte [como configurar pontos finais](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Passo 8: Tentar ligar a uma partilha de rede VM

Se não conseguir ligar a uma partilha de rede VM, o problema pode ser causado por NICs disponíveis na VM. Para eliminar os NICs indisponíveis, consulte [como eliminar os NICs indisponíveis](../virtual-machines/windows/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Passo 9: A conectividade de verificação Inter-Vnet

Utilize [verificar do fluxo de rede do observador do IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) e [NSG fluxo registo](../network-watcher/network-watcher-nsg-flow-logging-overview.md) para determinar se existe um NSG ou UDR é interferir com o fluxo de tráfego. Também pode verificar a configuração do Inter-Vnet [aqui](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
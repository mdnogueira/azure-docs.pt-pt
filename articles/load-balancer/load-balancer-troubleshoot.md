---
title: Resolver problemas de Balanceador de carga do Azure | Microsoft Docs
description: Resolver problemas conhecidos com o Balanceador de carga do Azure
services: load-balancer
documentationcenter: na
author: RamanDhillon
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: bc059221656a695bb43af0dca06df941ca77c73d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-load-balancer"></a>Resolver problemas de Balanceador de carga do Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Esta página fornece informações de resolução de problemas para perguntas comuns do Balanceador de carga do Azure. Quando a conectividade de Balanceador de carga não estiver disponível, os sintomas mais comuns são os seguintes: 
- VMs por trás do Balanceador de carga não estão a responder às sondas de estado de funcionamento 
- VMs por trás do Balanceador de carga não estão a responder para o tráfego na porta configurada

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintoma: VMs por trás do Balanceador de carga não estão a responder para sondas de estado de funcionamento
Para os servidores de back-end participar no conjunto de Balanceador de carga, tem de passar a verificação de pesquisa. Para obter mais informações sobre as sondas de estado de funcionamento, consulte [Balanceador de carga de compreender as sondas](load-balancer-custom-probe-overview.md). 

O conjunto de back-end de Balanceador de carga VMs pode não estar a responder para pesquisas devido a qualquer um dos seguintes motivos: 
- O conjunto de back-end de Balanceador de carga VM está em mau estado de funcionamento 
- VM não está a escutar na porta sonda o conjunto back-end de Balanceador de carga 
- Firewall ou um grupo de segurança de rede está a bloquear a porta no conjunto de back-end de Balanceador de carga VMs 
- Outras configurações incorretas no balanceador de carga

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: Conjunto de back-end de Balanceador de carga VM é mau estado de funcionamento 

**Resolução e validação**

Para resolver este problema, inicie sessão no VMs participantes, verifique se o estado VM está em bom estado e pode responder a **PsPing** ou **TCPing** de outra VM no conjunto. Se a VM está danificada ou não consegue responder para a sonda, tem de resolver o problema e obter a VM para um bom estado de funcionamento antes-pode participar no balanceamento de carga.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: O conjunto de back-end de Balanceador de carga VM não esteja a escutar na porta sonda
Se a VM está em bom estada, mas não está a responder a pesquisa, em seguida, um motivo possível pode ser que a porta de sonda não está aberta no participar VM ou a VM não está à escuta nessa porta.

**Resolução e validação**

1. Inicie sessão no back-end da VM. 
2. Abra uma linha de comandos e execute o seguinte comando validar existe, é uma aplicação em escuta na porta de pesquisa:   
            netstat - an
3. Se o estado de porta não estiver listado como **LISTENING**, configurar a porta correta. 
4. Em alternativa, selecione outra porta, o que está listada como **LISTENING**e atualize a configuração de Balanceador de carga em conformidade.              

###<a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: Firewall ou um grupo de segurança de rede está a bloquear a porta no conjunto de back-end de Balanceador de carga VMs  
Se a firewall na VM está a bloquear a porta da sonda ou um ou mais grupos de segurança configurados na sub-rede ou na VM de rede, não está a permitir a pesquisa alcançar a porta, a VM não consegue responder para a sonda de estado de funcionamento.          

**Resolução e validação**

* Se a firewall estiver ativada, verifique se está configurada para permitir que a porta da sonda. Caso contrário, configure a firewall para permitir o tráfego na porta de pesquisa e teste novamente. 
* Na lista de grupos de segurança de rede, verifique se o tráfego de entrada ou de saída na porta sonda tem interferências. 
* Além disso, verifique se um **negar todos os** de regras de grupos de segurança de rede no NIC de VM ou a sub-rede que tenha uma prioridade mais alta do que a regra predefinida que lhe permite LB sondas & tráfego (grupos de segurança de rede têm de permitir IP Balanceador de carga 168.63.129.16). 
* Se qualquer um destas regras estão a bloquear o tráfego de pesquisa, remova e reconfigure as regras para permitir o tráfego de pesquisa.  
* Teste se a VM agora começou a responder às sondas de estado de funcionamento. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Outras configurações incorretas no balanceador de carga
Se as causas anteriores parecem ser validado e resolvida corretamente e o back-end VM ainda não responder para a sonda de estado de funcionamento, em seguida, manualmente testar a conectividade e recolher algumas rastreios para compreender a conectividade.

**Resolução e validação**

* Utilize **Psping** uma das outras VMs dentro da VNet para testar a resposta de porta de pesquisa (exemplo:.\psping.exe -t 10.0.0.4:3389) e registe os resultados. 
* Utilize **TCPing** uma das outras VMs dentro da VNet para testar a resposta de porta de pesquisa (exemplo:.\tcping.exe 10.0.0.4 3389) e registe os resultados. 
* Se for recebida nenhuma resposta nesses testes de ping, em seguida
    - Execute um rastreio Netsh simultâneo no conjunto de back-end de destino VM e outra VM de teste da mesma VNet. Agora, execute um teste de PsPing para algum tempo, recolher algumas rastreios de rede e, em seguida, pare o teste. 
    - Analise a captura de rede e se existem pacotes de entrada e saídas relacionadas com a consulta de ping. 
        - Se não existem pacotes de entrada são observados no conjunto de back-end VM, há potencialmente a grupos de segurança de rede ou de configuração incorrecta da UDR bloquear o tráfego. 
        - Se nenhum dos pacotes enviados são observados no conjunto de back-end VM, a VM tem de ser verificada para quaisquer problemas não relacionados (para eample, bloquear a porta da sonda de aplicação). 
    - Certifique-se de que o se os pacotes de sonda estão a ser forçados outro destino (possivelmente através de definições de UDR) antes de atingir o Balanceador de carga. Isto pode fazer com que o tráfego para nunca alcançam o back-end VM. 
* Alterar o tipo de pesquisa (por exemplo, HTTP para TCP) e configurar a porta correspondente ACLs de grupos de segurança de rede e de firewall para validar se o problema está na configuração da resposta de pesquisa. Para obter mais informações sobre a configuração de pesquisa do Estado de funcionamento, consulte [configuração balanceamento de carga de ponto final de sonda de estado de funcionamento](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintoma: VMs por trás do Balanceador de carga não estão a responder ao tráfego na porta de dados configurados

Se um conjunto de back-end VM está listada como o bom estado de funcionamento e responde a sondas de estado de funcionamento, mas ainda não está a participar no balanceamento de carga ou não está a responder para o tráfego de dados, poderá ser devido a qualquer um dos seguintes motivos: 
* VM não está a escutar a porta de dados de conjunto de back-end de Balanceador de carga 
* Grupo de segurança de rede está a bloquear a porta no conjunto de back-end de Balanceador de carga VM  
* Aceder ao balanceador de carga da mesma VM e NIC 
* Aceder ao VIP de Balanceador de carga de Internet a partir do conjunto de back-end de Balanceador de carga participantes VM 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: Conjunto de back-end de Balanceador de carga VM não esteja a escutar na porta de dados 
Se uma VM não responder para o tráfego de dados, poderá ser porque a porta de destino não está aberta na VM participantes, ou, a VM não está a escutar nessa porta. 

**Resolução e validação**

1. Inicie sessão no back-end da VM. 
2. Abra uma linha de comandos e execute o seguinte comando validar existe, é uma aplicação em escuta na porta de dados:  
            netstat - an 
3. Se a porta não estiver listada com o estado "ESCUTAR", configurar a porta do serviço de escuta adequada 
4. Se a porta está marcada como Listening, em seguida, verifique a aplicação de destino nessa porta para quaisquer problemas possíveis. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: Grupo de segurança de rede está a bloquear a porta no conjunto de back-end de Balanceador de carga VM  

Se um ou mais grupos de segurança de rede configuradas na sub-rede ou na VM, está a bloquear o IP de origem ou a porta, em seguida, a VM não é possível a responder.

* Lista os grupos de segurança de rede configurados no back-end da VM. Para obter mais informações, consulte:
    -  [Gerir grupos de segurança de rede através do Portal](../virtual-network/virtual-network-manage-nsg-arm-portal.md)
    -  [Gerir grupos de segurança de rede através do PowerShell](../virtual-network/virtual-network-manage-nsg-arm-ps.md)
* Na lista de grupos de segurança de rede, verifique se:
    - o tráfego de entrada ou de saída na porta de dados tem interferências. 
    - um **negar todos os** regra de grupo de segurança no NIC de VM ou a sub-rede que tenha uma prioridade mais alta do que as sondas a regra predefinida que permite que o Balanceador de carga de rede e de tráfego (grupos de segurança de rede têm de permitir IP de Balanceador de carga de 168.63.129.16, que é a porta da sonda) 
* Se qualquer uma das regras estão a bloquear o tráfego, remova e reconfigure essas regras para permitir o tráfego de dados.  
* Teste se a VM agora começou a responder às sondas de estado de funcionamento.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: Aceder ao balanceador de carga da mesma interface de rede e de VM 

Se a sua aplicação alojada no back-end da VM de um balanceador de carga está a tentar aceder a outra aplicação alojada no mesmo back-end da VM durante a mesma Interface de rede, é um cenário não suportado e irá falhar. 

**Resolução** pode resolver este problema através de um dos seguintes métodos:
* Configure o conjunto de back-end separado VMs por aplicação. 
* Configure a aplicação em VMs de NIC duplo para que cada aplicação utilizou a sua própria interface de rede e o endereço IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: Aceder ao VIP de Balanceador de carga interno do conjunto de back-end de Balanceador de carga participantes VM

Se um VIP ILB está configurado no interior de uma VNet e um das VMs do back-end participante está a tentar aceder ao VIP de Balanceador de carga interno, que resulta numa falha. Este é um cenário não suportado.
**Resolução** avaliar o Gateway de aplicação ou outros proxies (por exemplo, nginx ou haproxy) para suportar a esse tipo de cenário. Para obter mais informações sobre o Gateway de aplicação, consulte [descrição geral do Gateway da aplicação](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Captura de rede adicionais
Se optar por abrir um incidente de suporte, recolha as seguintes informações para uma resolução mais rápida. Escolha um back-end único VM para executar os seguintes testes:
- Utilize o Psping a partir de um back-end do VMs dentro da VNet para testar a resposta de porta de pesquisa (exemplo: psping 10.0.0.4:3389) e registe os resultados. 
- Utilizar TCPing a partir de um back-end do VMs dentro da VNet para testar a resposta de porta de pesquisa (exemplo: psping 10.0.0.4:3389) e registe os resultados.
- Se não for recebida nenhuma resposta nesses testes de ping, execute um rastreio Netsh simultâneo no back-end da VM e a VM de teste de VNet, enquanto que execute o PsPing e parar o rastreio Netsh. 
  
## <a name="next-steps"></a>Passos seguintes

Se os passos anteriores não resolverem o problema, abra uma [suporta permissão](https://azure.microsoft.com/support/options/).


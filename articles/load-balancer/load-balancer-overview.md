---
title: "Descrição geral do Balanceador de carga do Azure | Microsoft Docs"
description: "Descrição geral das funcionalidades, a arquitetura e a implementação de Balanceador de carga do Azure. Saiba como funciona o Balanceador de carga e aproveitam na nuvem."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ecf1fc38d2b9fd54fe5b00db616224a0848179fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-overview"></a>Azure Load Balancer overview (Descrição Geral do Balanceador de Carga do Azure)

O Balanceador de Carga do Azure oferece disponibilidade e desempenho da rede das suas aplicações. É um balanceador de carga de camada 4 (TCP, UDP) que distribui o tráfego de entrada entre instâncias de bom estado de funcionamento dos serviços definidos um conjunto com balanceamento de carga.

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Balanceador de carga do Azure pode ser configurado para:

* Carregar saldo tráfego de entrada à Internet para máquinas virtuais. Esta configuração é conhecida como [balanceamento de carga para a Internet](load-balancer-internet-overview.md).
* Tráfego de balanceamento de carga entre máquinas virtuais numa rede virtual, entre as máquinas virtuais nos serviços em nuvem ou entre computadores no local e as máquinas virtuais numa rede virtual em vários locais. Esta configuração é conhecida como [balanceamento de carga interna](load-balancer-internal-overview.md).
* Encaminhar o tráfego externo para uma máquina virtual específica.

Todos os recursos na nuvem precisam de um endereço IP público para ser acessível a partir da Internet. A infraestrutura de nuvem do Azure utiliza endereços não encaminháveis internos de IP para os respetivos recursos. Azure utiliza a tradução de endereços de rede (NAT) com endereços IP públicos para comunicar com a Internet.

## <a name="load-balancer-features"></a>Funcionalidades de Balanceador de carga

* Distribuição com base em hash

    Balanceador de carga do Azure utiliza um algoritmo de distribuição com base em hash. Por predefinição, utiliza um hash de 5 cadeias de identificação é composto por IP de origem, porta de origem, IP de destino, porta de destino e tipo de protocolo para mapear o tráfego para servidores disponíveis. Fornece apenas retenções *dentro* uma sessão de transporte. Pacotes na mesma sessão TCP ou UDP serão direcionados para a mesma instância atrás o ponto final com balanceamento de carga. Quando o cliente fecha e reopens a ligação ou inicia uma nova sessão a partir do mesmo IP de origem, altera a porta de origem. Isto pode fazer com que o tráfego Ir para outro ponto final no Centro de dados diferentes.

    Para obter mais detalhes, consulte [modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md). O gráfico seguinte mostra a distribuição com base em hash:

    ![Distribuição com base em hash](./media/load-balancer-overview/load-balancer-distribution.png)

    Figura - Hash baseada em distribuição

* Reencaminhamento de porta

    Pode controlar através de comunicação de entrada como é gerida do Azure fornecem de Balanceador de carga. Esta comunicação inclui tráfego iniciado a partir de anfitriões da Internet, as máquinas virtuais na nuvem ou outros serviços, redes virtuais. Este controlo é representado por um ponto final (também designado por um ponto final de entrada).

    Um ponto final escuta numa porta pública e reencaminha o tráfego para uma porta interno. Pode mapear as mesmas portas para um ponto final interno ou externo ou utilizar uma porta diferente para os mesmos. Por exemplo, pode ter um servidor web configurado para escutar a porta 81 enquanto o mapeamento de ponto final público é a porta 80. A criação de um ponto final público aciona a criação de uma instância de Balanceador de carga.

    Quando criado no portal do Azure, o portal cria automaticamente os pontos finais para a máquina virtual para o protocolo RDP (Remote Desktop Protocol) e tráfego de sessão do Windows PowerShell remoto. Pode utilizar estes pontos finais para administrar remotamente a máquina virtual através da Internet.

* Reconfiguração automática

    Balanceador de carga do Azure de forma instantânea próprio reconfigura quando dimensionar as instâncias ou reduzir verticalmente. Por exemplo, esta reconfiguração acontece quando aumenta o número de instâncias para a web/funções de trabalho num serviço em nuvem ou quando adicionar máquinas virtuais adicionais para o mesmo conjunto com balanceamento de carga.

* Monitorização do serviço

    Balanceador de carga do Azure pode sonda do Estado de funcionamento de várias instâncias de servidor. Quando uma sonda não responder, o Balanceador de carga para o envio de novas ligações para as instâncias de mau estado de funcionamento. As ligações existentes não são afetadas.

    Três tipos de sondas que são suportados:

    + **Pesquisa de agente do convidado (na plataforma como um serviço de máquinas virtuais apenas):** o Balanceador de carga utiliza o agente convidado dentro da máquina virtual. O agente convidado escuta e responde com uma resposta de HTTP 200 OK apenas quando a instância está no estado pronto (ou seja, a instância não está num estado como ocupadas, reciclagem ou a parar). Se o agente não conseguir responder com um HTTP 200 OK, o Balanceador de carga marca a instância como responder e deixa de envio de tráfego para essa instância. O Balanceador de carga continua a executar um ping a instância. Se o agente convidado responde com uma HTTP 200, o Balanceador de carga irá enviar o tráfego para essa instância novamente. Quando estiver a utilizar uma função da web, o código de site, normalmente, é executado em w3wp.exe, que não é monitorizado por do Azure agente convidado ou recursos de infraestrutura. Isto significa que não serão comunicadas falhas no w3wp.exe (por exemplo, as respostas HTTP 500) para o agente convidado e o Balanceador de carga não saberá que efetuar essa instância fora de rotação.
    + **Sonda personalizada HTTP:** esta pesquisa substitui a sonda de (agente do convidado) predefinida. Pode utilizá-lo para criar a sua própria lógica personalizada para determinar o estado de funcionamento da instância de função. O Balanceador de carga regularmente vai sondar o ponto final (cada 15 segundos, por predefinição). A instância for considerada no rotação se responder com uma confirmação de TCP ou HTTP 200 dentro do período de tempo limite (predefinição de segundos 31). Isto é útil para implementar a sua própria lógica para remover as instâncias de rotação do Balanceador de carga. Por exemplo, pode configurar a instância para devolver um Estado não 200 se a instância está acima da CPU de 90%. Para funções da web que utilizam w3wp.exe, utilizador também obtém automática de monitorização do seu site, uma vez que as falhas no seu código de site devolvem um Estado de não 200 para a sonda.
    + **Sonda personalizada TCP:** esta pesquisa depende do estabelecimento de sessão TCP com êxito a uma porta de pesquisa definidos.

    Para obter mais informações, consulte o [LoadBalancerProbe esquema](https://msdn.microsoft.com/library/azure/jj151530.aspx).

* Origem de NAT

    Todo o tráfego de saída à Internet que origina do seu serviço sofre origem NAT (realizar o SNAT) utilizando o mesmo endereço de VIP como o tráfego de entrada. Realizar o SNAT proporciona vantagens importantes:

    + Permite a fácil recuperação após desastre e atualização dos serviços, uma vez que o VIP pode ser atribuído dinamicamente a outra instância do serviço.
    + -Facilita a gestão de lista (ACL) de controlo de acesso. As ACLs expressadas em termos de VIPs não alteram os como serviços escala, baixo, ou obterem implementada novamente.

    A configuração de Balanceador de carga suporta cone completa NAT para UDP. Cone completa NAT é um tipo de NAT onde a porta permite ligações de entrada de qualquer anfitrião externo (em resposta a um pedido de saída).

    Para cada nova ligação de saída que inicia uma máquina virtual, uma porta de saída também é atribuída pelo balanceador de carga. O anfitrião externo vê o tráfego com uma porta virtual atribuída de IP VIP. Para cenários que necessitam de um grande número de ligações de saída, é recomendado utilizar [IP público de nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) endereços para que as VMs têm um endereço IP de saída dedicado para realizar o SNAT. Isto reduz o risco de esgotamento de porta.

    Consulte [ligações de saída](load-balancer-outbound-connections.md) artigo para obter mais detalhes sobre este tópico.

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>Suporte para vários com balanceamento de carga endereços IP para máquinas virtuais
Pode atribuir mais do que um balanceamento de carga endereço IP público para um conjunto de máquinas virtuais. Com esta capacidade, pode alojar vários sites SSL e/ou de vários serviços de escuta do grupo de Disponibilidade AlwaysOn do SQL Server no mesmo conjunto de máquinas virtuais. Para obter mais informações, consulte [vários VIPs por serviço em nuvem](load-balancer-multivip.md).

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="limitations"></a>Limitações

Conjuntos de back-end de Balanceador de carga podem conter qualquer SKU de VM, exceto o escalão básico.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Balanceador de carga para a Internet](load-balancer-internet-overview.md)

- Saiba mais sobre [descrição geral do Balanceador de carga interno](load-balancer-internal-overview.md)

- Criar um [Balanceador de carga para a Internet](load-balancer-get-started-internet-portal.md)

- Saiba mais sobre algumas da outra chave [capacidades de rede](../networking/networking-overview.md) do Azure


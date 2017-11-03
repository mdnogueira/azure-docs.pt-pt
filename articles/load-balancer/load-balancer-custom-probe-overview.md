---
title: "Monitorização de estado de funcionamento e das sondas personalizadas do Balanceador de carga | Microsoft Docs"
description: "Saiba como utilizar das sondas personalizadas para o Balanceador de carga do Azure para monitorizar instâncias por trás do Balanceador de carga"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 102c07ff0994b3b411f2a13d7a43c5398d5dfd42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-load-balancer-probes"></a>Compreender as sondas do balanceador de carga

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Balanceador de carga do Azure oferece a capacidade de monitorizar o estado de funcionamento de instâncias de servidor utilizando as pesquisas. Quando uma sonda não responder, o Balanceador de carga para a enviar novas ligações para a instância de mau estado de funcionamento. Não são afetadas as ligações existentes e novas ligações são enviadas para instâncias de bom estado de funcionamento.

Funções de serviço na nuvem (funções de trabalho e funções da web) utilizam o agente convidado para a monitorização de pesquisa. Tem de ser configuradas sondas personalizadas TCP ou HTTP ao utilizar máquinas virtuais por trás do Balanceador de carga.

## <a name="understand-probe-count-and-timeout"></a>Compreender a contagem de pesquisa e o tempo limite

Comportamento de pesquisa depende:

* O número de pesquisas com êxito que permitem que uma instância a ser assinalada como como operacional.
* O número de sondas de falha que fazer com que uma instância a ser assinalada como tão baixo.

O valor de frequência e tempo limite definido no SuccessFailCount determinar se uma instância é confirmada para estar em execução ou não está em execução. No portal do Azure, o tempo limite está definido para duas vezes o valor de frequência.

A configuração de pesquisa de todas as instâncias com balanceamento de carga para um ponto final (ou seja, um conjunto com balanceamento de carga) têm de ser iguais. Isto significa que não pode ter uma configuração de pesquisa diferente para cada máquina virtual ou instância de função no serviço alojado mesmo para uma combinação de ponto final específico. Por exemplo, cada instância tem de ter idênticas portas locais e tempos limite.

> [!IMPORTANT]
> Uma sonda do Balanceador de carga utiliza o endereço IP 168.63.129.16. Este endereço IP público facilita a comunicação para recursos de plataforma interna de bring-your-proprietário-IP cenário de rede Virtual do Azure. O endereço IP público 168.63.129.16 virtual é utilizado em todas as regiões e não será alterado. Recomendamos que permitem este endereço IP em quaisquer políticas de local firewall. Este não deve ser considerada um risco de segurança porque apenas interna plataforma do Azure pode originar uma mensagem desse endereço. Se não fizer isto, haverá um comportamento inesperado numa variedade de cenários, como configurar o intervalo de endereços IP mesmo de 168.63.129.16 e ter duplicado endereços IP.

## <a name="learn-about-the-types-of-probes"></a>Saiba mais sobre os tipos de pesquisas

### <a name="guest-agent-probe"></a>Pesquisa do agente convidado

Esta pesquisa só está disponível para serviços de nuvem do Azure. Balanceador de carga utiliza o agente convidado dentro da máquina virtual e, em seguida, escuta e responde com uma resposta de HTTP 200 OK apenas quando a instância está no estado pronto (ou seja, não no outro Estado como ocupado, a Reciclagem ou a parar).

Para obter mais informações, consulte [configurar o ficheiro de definição de serviço (. csdef) para sondas de estado de funcionamento](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [introdução à criação de um balanceador de carga para a Internet para serviços em nuvem](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>O que faz uma sonda do agente convidado marcar uma instância como estando danificado?

Se o agente convidado não conseguir responder com HTTP 200 OK, o Balanceador de carga marca a instância como responder e deixa de envio de tráfego para essa instância. O Balanceador de carga continua a executar um ping a instância. Se o agente convidado responde com uma HTTP 200, o Balanceador de carga envia tráfego a essa instância novamente.

Quando utiliza uma função da web, o código de site normalmente é executado no w3wp.exe, que não é monitorizado por do Azure agente convidado ou recursos de infraestrutura. Isto significa que não serão comunicadas falhas no w3wp.exe (por exemplo, as respostas HTTP 500) para o agente convidado e o Balanceador de carga não irá surtir essa instância fora de rotação.

### <a name="http-custom-probe"></a>Pesquisa HTTP personalizada

A sonda do Balanceador de carga de HTTP personalizada substitui a pesquisa do agente convidado predefinido, que significa que pode criar a seus próprios lógica personalizada para determinar o estado de funcionamento da instância de função. O Balanceador de carga as sondas o ponto final de cada 15 segundos, por predefinição. A instância for considerada na rotação de Balanceador de carga se responder com um HTTP 200 dentro do período de tempo limite (segundos 31 por predefinição).

Isto pode ser útil se pretender implementar a sua própria lógica para remover as instâncias de rotação de Balanceador de carga. Por exemplo, pode optar por remover uma instância se estiver acima de 90% de CPU e devolve um Estado não 200. Se tiver de funções da web que utilizam w3wp.exe, isto também significa que obtém automática de monitorização do seu site, porque as falhas no seu código do site irão devolver um Estado não 200 para a sonda do Balanceador de carga.

> [!NOTE]
> A pesquisa personalizada HTTP suporta caminhos relativos e apenas os protocolos HTTP. Não é suportado HTTPS.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>O que faz uma sonda personalizada de HTTP marcar uma instância como estando danificado?

* A aplicação de HTTP devolve um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Esta é uma confirmação positiva que a instância da aplicação deverão ser executada fora do serviço de imediato.
* O servidor de HTTP não responde em todas as após o período de tempo limite. Dependendo do valor de limite de tempo definido, isto poderá significar que sonda vários pedidos aceda unanswered antes da pesquisa obtém marcada como não está em execução (ou seja, antes de SuccessFailCount sondas são enviadas).
* O servidor fecha a ligação através de uma reposição do TCP.

### <a name="tcp-custom-probe"></a>Sonda personalizada TCP

As pesquisas de TCP iniciam uma ligação ao efetuar um handshake de três vias com a porta definido.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>O que faz uma sonda personalizada de TCP marcar uma instância como estando danificado?

* O servidor TCP não responde em todas as após o período de tempo limite. Quando a pesquisa está marcada como não está em execução depende do número de pedidos de sonda falhadas que foram configurados para ir unanswered antes de marcar a sonda como não está em execução.
* A sonda recebe um TCP repor a instância de função.

Para obter mais informações sobre como configurar uma sonda do Estado de funcionamento HTTP ou uma sonda TCP, consulte [introdução à criação de um balanceador de carga para a Internet no Gestor de recursos com o PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Adicionar as instâncias de bom estado de funcionamento no rotação de Balanceador de carga

As pesquisas de TCP e HTTP são consideradas em bom estado de funcionamento e marcar a instância de função quando bom estado de funcionamento:

* O Balanceador de carga obtém uma sonda positiva pela primeira vez a VM arranca.
* O número SuccessFailCount (descrito anteriormente) define o valor de pesquisas com êxito, que são necessárias para marcar a instância de função em bom estado. Se uma instância de função tiver sido removida, o número de pesquisas com êxito, sucessivas tem de ser igual ou exceder o valor de SuccessFailCount para marcar a instância de função em execução.

> [!NOTE]
> Se o estado de funcionamento de uma instância de função é fluctuating, o Balanceador de carga já aguarda antes de colocar a instância de função no bom estado de funcionamento. Isto é feito através da política para proteger a infraestrutura e o utilizador.

## <a name="use-log-analytics-for-load-balancer"></a>Análise de registos de utilização para o Balanceador de carga

Pode utilizar [análise de registo para o Balanceador de carga](load-balancer-monitor-log.md) para verificar o estado de funcionamento da pesquisa e a contagem de pesquisa. O registo pode ser utilizado com o Power BI ou informações operacionais do Azure para fornecer estatísticas sobre o estado de funcionamento do Balanceador de carga.

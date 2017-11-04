---
title: "Introdução ao observador de rede do Azure | Microsoft Docs"
description: "Esta página fornece uma descrição geral do serviço de observador de rede para a monitorização e visualizar rede ligada recursos no Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: eecb20d4a53478471c238018d8fbd5a5f9cb79d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-monitoring-overview"></a>Descrição geral de monitorização de rede do Azure

Os clientes criar uma rede ponto a ponto no Azure através da orquestração e composição vários recursos de rede individuais, tais como VNet, ExpressRoute, Gateway de aplicação, balanceadores de carga e muito mais. A monitorização está disponível em cada um dos recursos da rede. Vamos referir-se a esta monitorização como a monitorização de nível de recursos.

Rede ponto a ponto pode ter configurações complexas e as interações entre os recursos, criar cenários complexos que necessitam de baseada em cenários de monitorização através do observador de rede.

Este artigo abrange cenário e a monitorização de nível de recursos. Monitorização de rede no Azure é abrangente e abrange duas amplas categorias:

* [**Observador de rede** ](#network-watcher) -baseada em cenários de monitorização é fornecido com as funcionalidades do observador de rede. Este serviço inclui a captura de pacotes, o salto seguinte, o fluxo IP verificar, a vista de grupo de segurança, os registos de fluxo NSG. A monitorização ao nível do cenário fornece uma vista de ponto a ponto dos recursos de rede, ao contrário de monitorização de recursos de rede individuais.
* [**Monitorização de recursos** ](#network-resource-level-monitoring) -monitorização de nível de recursos é composto por de quatro funcionalidades, os registos de diagnóstico, métricas, resolução de problemas e estado de funcionamento do recurso. Todas estas funcionalidades são criadas ao nível de recursos de rede.

## <a name="network-watcher"></a>Observador de Rede

Observador de rede é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário rede, para e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam-na compreender, diagnosticar e obter informações sobre a sua rede no Azure.

Observador de rede atualmente tem as seguintes capacidades:

* **[Topologia](network-watcher-topology-overview.md)**  -fornece uma vista de nível de rede com as várias interconnections e as associações entre recursos de rede num grupo de recursos.
* **[Captura de pacotes variável](network-watcher-packet-capture-overview.md)**  -captura de dados do pacote e para uma máquina virtual. Opções de filtragem avançadas e controlos otimizados, tais como a capacidade de definir a hora e limitações de tamanho fornecem versatility. Os dados de pacote podem ser armazenados num arquivo de blob ou no disco local no formato .cap.
* **[Certifique-se de fluxo IP](network-watcher-ip-flow-verify-overview.md)**  -verifica se a um pacote é permitido ou negado com base nos parâmetros de pacotes de 5 cadeias de identificação do fluxo informações (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote é negado por um grupo de segurança, a regra e o grupo negado o pacote é devolvido.
* **[Próximo salto](network-watcher-next-hop-overview.md)**  -determina o salto seguinte para pacotes a ser encaminhados na infraestrutura de rede do Azure, permitindo-lhe diagnosticar qualquer configurada incorretamente rotas definidas pelo utilizador.
* **[Vista do grupo de segurança](network-watcher-security-group-view-overview.md)**  -obtém as regras de segurança eficaz e aplicados que são aplicadas numa VM.
* **[O registo de fluxo de NSG](network-watcher-nsg-flow-logging-overview.md)**  -registos de fluxo para grupos de segurança de rede permitem-lhe capturar registos relacionados com o tráfego que são permitidas ou negadas pelas regras de segurança no grupo. O fluxo é definido por uma informação de 5 cadeias de identificação – IP de origem, IP de destino, porta de origem, porta de destino e protocolo.
* **[Gateway de rede virtual e a resolução de problemas de ligação](network-watcher-troubleshoot-manage-rest.md)**  -fornece a capacidade de resolução de problemas de Gateways da Virtual Network e ligações.
* **[Limites de subscrição de rede](#network-subscription-limits)**  -permite-lhe ver utilização de recursos de rede contra os limites.
* **[Configurar registo de diagnóstico](#diagnostic-logs)**  – disponibiliza um painel único para ativar ou desativar os registos de diagnóstico para recursos de rede num grupo de recursos.
* **[Conectividade (pré-visualização)](network-watcher-connectivity-overview.md)**  -verifica a possibilidade de estabelecer uma ligação de TCP direta de uma máquina virtual para um ponto final especificado.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Controlo de acesso baseado em funções (RBAC) na observador de rede

Utilizações de observador de rede a [modelo de controlo de acesso em funções do Azure (RBAC)](../active-directory/role-based-access-control-what-is.md). As seguintes permissões são necessárias pelo observador de rede. É importante certificar-se de que a função utilizada para iniciar as APIs do observador de rede ou para utilizar o observador de rede do portal tem o acesso necessário.

|Recurso| Permissão|
|---|---| 
|Microsoft.Storage/ |Leitura|
|Microsoft.Authorization/| Leitura| 
|Microsoft.Resources/subscriptions/resourceGroups/| Leitura|
|Microsoft.Storage/storageAccounts/listServiceSas/ | Ação|
|Microsoft.Storage/storageAccounts/listAccountSas/ |Ação|
|Microsoft.Storage/storageAccounts/listKeys/ | Ação|
|Microsoft.Compute/virtualMachines/ |Leitura|
|Microsoft.Compute/virtualMachines/ |Escrita|
|Microsoft.Compute/virtualMachineScaleSets/ |Leitura|
|Microsoft.Compute/virtualMachineScaleSets/ |Escrita|
|Microsoft.Network/networkWatchers/packetCaptures/ |Leitura|
|Microsoft.Network/networkWatchers/packetCaptures/| Escrita|
|Microsoft.Network/networkWatchers/packetCaptures/| Eliminar|
|Microsoft.Network/networkWatchers/ |Escrita |
|Microsoft.Network/networkWatchers/| Leitura |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>Limites de subscrição de rede

Limites de subscrição de rede fornecem detalhes de utilização de cada recurso de rede numa subscrição numa região contra o número máximo de recursos disponíveis.

![limite de subscrição de rede][nsl]

## <a name="network-resource-level-monitoring"></a>Monitorização ao nível da rede recursos

As seguintes funcionalidades estão disponíveis para monitorização de nível de recursos:

### <a name="audit-log"></a>Registo de auditoria

Operações efetuadas como parte da configuração de redes são registadas. Estes registos podem ser visualizados no portal do Azure ou obtidos através de ferramentas da Microsoft, tais como o Power BI ou ferramentas de terceiros. Os registos de auditoria estão disponíveis através do portal, o PowerShell, a CLI e a Rest API. Para obter mais informações sobre os registos de auditoria, consulte [auditar operações com o Resource Manager](../resource-group-audit.md)

Os registos de auditoria estão disponíveis para operações efetuadas em todos os recursos de rede.

### <a name="metrics"></a>Métricas

As métricas são medidas de desempenho e contadores recolhidos durante um período de tempo. Métricas estão atualmente disponíveis para o Gateway de aplicação. Métricas podem ser utilizadas para acionar alertas com base num limiar. Consulte [diagnóstico do Gateway de aplicação](../application-gateway/application-gateway-diagnostics.md) para ver como métricas podem ser utilizadas para criar alertas.

![Vista de métricas][metrics]

### <a name="diagnostic-logs"></a>Registos de diagnósticos

Eventos periódicos e spontaneous são criados pelos recursos de rede e registados em contas do storage, enviadas para um Hub de eventos ou análise de registos. Estes registos fornecem informações sobre o estado de funcionamento de um recurso. Estes registos podem ser visualizados no ferramentas como o Power BI e análise de registos. Para saber como ver os registos de diagnóstico, visite [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

Os registos de diagnóstico estão disponíveis para [Balanceador de carga](../load-balancer/load-balancer-monitor-log.md), [grupos de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md), rotas, e [Gateway de aplicação](../application-gateway/application-gateway-diagnostics.md).

Observador de rede fornece que um diagnóstico os registos de vista. Esta vista contém todos os recursos de rede que suportam o registo de diagnóstico. Desta vista, pode ativar e desativar os recursos de rede rápida e convenientemente.

![registos][logs]

### <a name="troubleshooting"></a>Resolução de problemas

O painel de resolução de problemas, uma experiência de utilização no portal, é fornecido nos recursos de rede hoje para diagnosticar problemas comuns associados a um recurso individual. Esta experiência está disponível para os seguintes recursos de rede - ExpressRoute, Gateway de VPN, Gateway de aplicação, registos de segurança de rede, rotas, DNS, Balanceador de carga e do Traffic Manager. Para mais informações sobre resolução de problemas ao nível do recurso, visite [diagnosticar e resolver problemas com a resolução de problemas do Azure](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)

![informações de resolução de problemas][TS]

### <a name="resource-health"></a>Estado de funcionamento de recursos

O estado de funcionamento de um recurso de rede é fornecido numa base periódica. Esses recursos incluem o Gateway de VPN e túnel VPN. Estado de funcionamento do recurso está acessível no portal do Azure. Para mais informações sobre o estado de funcionamento de recursos, visite [descrição geral do Estado de funcionamento de recursos](../resource-health/resource-health-overview.md)

## <a name="next-steps"></a>Passos seguintes

Depois de aprender mais sobre o observador de rede, pode aprender a:

Efetue uma captura de pacotes na sua VM, visitando [captura de pacotes variável no portal do Azure](network-watcher-packet-capture-manage-portal.md)

Efetuar a monitorização proativa e diagnóstico utilizando [alerta acionado captura de pacotes](network-watcher-alert-triggered-packet-capture.md).

Detetar vulnerabilidades de segurança com [analisar a captura de pacotes com o Wireshark](network-watcher-deep-packet-inspection.md), utilizando ferramentas open source.

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png












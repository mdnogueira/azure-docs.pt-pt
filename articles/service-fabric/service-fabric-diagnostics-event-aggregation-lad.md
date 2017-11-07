---
title: "Agregação de eventos de recursos de infraestrutura de serviço do Azure com diagnósticos do Azure do Linux | Microsoft Docs"
description: "Saiba mais sobre agregar e recolha de eventos utilizando LAD para monitorização e diagnóstico de clusters de Service Fabric do Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: b70780b2e1d169aced3412f6fe6d13ad4dab82be
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Agregação de eventos e a coleção com Linux do diagnóstico do Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando estiver a executar um cluster do Service Fabric do Azure, é uma boa ideia para recolher os registos de todos os nós numa localização central. Com os registos numa localização central ajuda a analisar e resolver problemas no seu cluster, ou problemas em aplicações e serviços em execução nesse cluster.

Uma forma para carregar e recolher registos consiste em utilizar a extensão do Linux do Azure Diagnostics (LAD), que carrega registos para o Storage do Azure e tem também a opção para enviar registos ao Application Insights do Azure ou Event Hubs. Também pode utilizar o processo externo para leitura dos eventos de armazenamento e colocá-los como um produto de plataforma de análise, [análise de registos do OMS](../log-analytics/log-analytics-service-fabric.md) ou outra solução de análise do registo.

## <a name="log-and-event-sources"></a>Origens de registo e evento

### <a name="service-fabric-platform-events"></a>Eventos de plataforma do Service Fabric
Service Fabric emite alguns registos de out of box através de [LTTng](http://lttng.org), incluindo eventos operacionais ou eventos de tempo de execução. Estes registos são armazenados na localização que especifica o modelo de Gestor de recursos do cluster. Para obter ou definir os detalhes da conta de armazenamento, procure a tag **AzureTableWinFabETWQueryable** e procure **StoreConnectionString**.

### <a name="application-events"></a>Eventos de aplicações
 Eventos emitidos a partir do código as suas aplicações e dos serviços, conforme especificado por si quando instrumentação o software. Pode utilizar qualquer solução de registo que escreve os ficheiros de registo baseados em texto – por exemplo, LTTng. Para obter mais informações, consulte a documentação de LTTng no rastrear a aplicação.

[Monitorizar e diagnosticar os serviços de uma configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Implementar a extensão de diagnóstico
É o primeiro passo na recolher registos para implementar a extensão de diagnóstico em cada uma das VMs no cluster de Service Fabric. A extensão de diagnóstico recolhe registos de cada VM e carrega-os para a conta do storage que especificar. 

Para implementar a extensão de diagnóstico para as VMs no cluster como parte da criação do cluster, defina **diagnóstico** para **no**. Depois de criar o cluster, não é possível alterar esta definição através do portal, pelo que terá de efetuar as alterações adequadas no modelo do Resource Manager.

Esta ação configura o agente LAD para monitorizar os ficheiros de registo especificado. Sempre que uma nova linha é acrescentada ao ficheiro, cria uma entrada de syslog que é enviada para o armazenamento (tabela) que especificou.


## <a name="next-steps"></a>Passos seguintes

1. Para saber mais detalhadamente que eventos deve examinar durante a resolução de problemas, consulte [LTTng documentação](http://lttng.org/docs) e [utilizando LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
2. [Configurar o agente do OMS](service-fabric-diagnostics-event-analysis-oms.md) para ajudar a recolher métricas, monitorizar contentores implementadas no seu cluster e visualizar os seus registos 
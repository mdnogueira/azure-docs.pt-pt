---
title: "Azure Service Fabric - configurar a monitorização com o agente do OMS | Microsoft Docs"
description: "Saiba como configurar o agente do OMS para monitorização contentores e contadores de desempenho para os clusters de Service Fabric do Azure."
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
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: e1a45f9924291382bb1bbdc969e97ee54a7b6132
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Adicionar o agente do OMS a um cluster

Este artigo aborda os passos para adicionar o agente do OMS como um máquina virtual conjunto de dimensionamento de extensão para o cluster e ligá-lo à sua área de trabalho de análise de registos do OMS existente. Isto permite recolher dados de diagnóstico sobre contentores, aplicações e a monitorização de desempenho. Adicionando-o como uma extensão, do Azure Resource Manager garante que é instalado em cada nó, mesmo quando a dimensionar o cluster.

> [!NOTE]
> Este artigo pressupõe que tem uma área de trabalho de análise de registos do OMS já definida. Se não o fizer, aceda a [configurar a análise de registos do OMS](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Adicionar a extensão de agente, através da CLI do Azure

Através do dimensionamento da máquina virtual, a melhor forma de adicionar o agente do OMS para o cluster está definida APIs disponíveis com a CLI do Azure. Se não tiver a CLI do Azure ainda configurar, aceda ao portal do Azure e abrir um [nuvem Shell](../cloud-shell/overview.md) instância, ou [instalar o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Assim que for solicitada a Shell de nuvem, certifique-se de que está a trabalhar na mesma subscrição, como o recurso. Verificar isto com `az account show` e certifique-se de que o valor "name" corresponde da subscrição do cluster.

2. No Portal, navegue para o grupo de recursos onde está localizada a sua área de trabalho do OMS. Clique para o análise de registos recursos (o tipo de recurso será Log Analytics), no painel de navegação à direita, desloque para baixo e clique em **propriedades**.

    ![Página de propriedades do OMS](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Tome nota do seu `workspaceId`. 

3. Também terá do `workspaceKey` para implementar o agente. Para obter a chave, clique em **definições avançadas**, sob o *definições* secção do painel de navegação esquerdo. Clique em **servidores Windows** se que é colocado cópias de segurança num cluster do Windows, e **servidores Linux** se estiver a criar um cluster do Linux. Terá do *chave primária* que aparece para implementar os agentes, como o `workspaceKey`.

4. Execute o comando para instalar o agente do OMS no seu cluster, utilizando o `vmss extension set` API na sua Shell de Cloud:

    Para um cluster do Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Para um cluster do Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId'}":'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Eis um exemplo do agente do OMS que está a ser adicionado a um cluster do Windows.

    ![Comando de cli de agente do OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
    Isto deve demorar menos de 15 min adicionar com êxito o agente para os nós. Pode verificar se os agentes foram adicionados utilizando a `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Adicionar o agente através do modelo do Resource Manager

Modelos de Gestor de recursos de exemplo que implemente uma área de trabalho de análise de registos do OMS e adicionar um agente para cada um dos seus nós está disponível para [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) ou [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Pode transferir e modificar este modelo para implementar um cluster que melhor se adapta às suas necessidades.

## <a name="next-steps"></a>Passos Seguintes

* Recolher relevante [contadores de desempenho](service-fabric-diagnostics-event-generation-perf.md). Para configurar o agente do OMS para recolher contadores de desempenho específicos, cabeçalho para o Portal do OMS (ligado na parte superior do recurso de análise de registos do OMS). Em seguida, clique em **home page > Definições > dados > contadores de desempenho do Windows** ou **contadores de desempenho do Linux** e escolha os contadores que pretende recolher.
* Configurar OMS configurar [automatizada alertas](../log-analytics/log-analytics-alerts.md) para ajudar a detetar e diagnóstico

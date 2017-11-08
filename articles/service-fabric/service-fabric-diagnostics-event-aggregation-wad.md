---
title: "Agregação de eventos de recursos de infraestrutura de serviço do Azure com o Windows Azure Diagnostics | Microsoft Docs"
description: "Saiba mais sobre agregar e recolha de eventos utilizando WAD para monitorização e diagnóstico de clusters de Service Fabric do Azure."
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
ms.openlocfilehash: e417458a16a5f23d8b89cbf87ab2713fab352046
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregação de eventos e coleção utilizando o Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando estiver a executar um cluster do Service Fabric do Azure, é uma boa ideia para recolher os registos de todos os nós numa localização central. Com os registos numa localização central ajuda a analisar e resolver problemas no seu cluster, ou problemas em aplicações e serviços em execução nesse cluster.

Uma forma para carregar e recolher registos consiste em utilizar a extensão do Windows Azure Diagnostics (WAD), que carrega registos para o Storage do Azure e tem também a opção para enviar registos ao Application Insights do Azure ou Event Hubs. Também pode utilizar o processo externo para leitura dos eventos de armazenamento e colocá-los como um produto de plataforma de análise, [análise de registos do OMS](../log-analytics/log-analytics-service-fabric.md) ou outra solução de análise do registo.

## <a name="prerequisites"></a>Pré-requisitos
Estas ferramentas são utilizadas para executar algumas das operações neste documento:

* [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionadas com a Cloud Services do Azure, mas tem informações detalhadas e exemplos)
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Cliente do Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>Origens de registo e evento

### <a name="service-fabric-platform-events"></a>Eventos de plataforma do Service Fabric
Tal como explicado [neste artigo](service-fabric-diagnostics-event-generation-infra.md), Service Fabric configura, com algumas canais de registo de out of box, dos quais as seguintes canais facilmente estão configuradas com WAD para enviar a monitorização e os dados de diagnóstico para uma tabela de armazenamento ou noutro local:
  * Eventos operacionais: operações de nível mais elevadas que efetua a plataforma do Service Fabric. Os exemplos incluem a criação de aplicações e serviços, as alterações de estado do nó e informações de atualização. Estes são emitidos como registos de rastreio de eventos para o Windows (ETW)
  * [Eventos do modelo de programação de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Serviços fiáveis de eventos do modelo de programação](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>Eventos de aplicações
 Eventos emitidos a partir do código as suas aplicações e dos serviços e escritos utilizando a classe de programa auxiliar de EventSource fornecida nos modelos Visual Studio. Para obter mais informações sobre como escrever registos de EventSource da sua aplicação, consulte [monitorizar e diagnosticar os serviços de uma configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Implementar a extensão de diagnóstico
É o primeiro passo na recolher registos para implementar a extensão de diagnóstico em cada uma das VMs no cluster de Service Fabric. A extensão de diagnóstico recolhe registos de cada VM e carrega-os para a conta do storage que especificar. Os passos variam ligeiramente com base em se utilizar o portal do Azure ou do Azure Resource Manager. Os passos também variam com base em se a implementação está parte da criação do cluster ou para um cluster que já existe. Vamos ver os passos para cada cenário.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Implementar a extensão de diagnóstico como parte da criação do cluster através do portal do Azure
Para implementar a extensão de diagnóstico para as VMs no cluster como parte da criação do cluster, utilize o painel de definições de diagnóstico mostrado na imagem seguinte - Certifique-se de que o diagnóstico está definido como **no** (a predefinição). Depois de criar o cluster, não é possível alterar estas definições utilizando o portal.

![Definições de diagnóstico do Azure no portal para a criação do cluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

Quando estiver a criar um cluster utilizando o portal, é vivamente recomendado que transfira o modelo **antes de clicar em OK** para criar o cluster. Para obter detalhes, consulte [configurar um cluster do Service Fabric utilizando um modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Terá do modelo para efetuar as alterações mais tarde, porque não é possível efetuar algumas alterações através do portal.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Implementar a extensão de diagnóstico como parte da criação do cluster utilizando o Azure Resource Manager
Para criar um cluster utilizando o Gestor de recursos, terá de adicionar a configuração de diagnósticos JSON para o modelo de Gestor de recursos do cluster completo antes de criar o cluster. Podemos fornecer um modelo de Gestor de recursos de cluster de VM de cinco de exemplo com a configuração de diagnósticos adicionada como parte dos nossos exemplos de modelo do Resource Manager. Pode vê-lo nesta localização na galeria do Azure Samples: [cluster de cinco nós com o exemplo de modelo de Gestor de recursos de diagnóstico](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Para ver a definição de diagnóstico no modelo do Resource Manager, abra o ficheiro azuredeploy. JSON e procure **IaaSDiagnostics**. Para criar um cluster utilizando este modelo, selecione o **implementar no Azure** botão disponível na hiperligação anterior.

Em alternativa, pode transferir o exemplo de Gestor de recursos, efetuar alterações e criar um cluster com o modelo modificado utilizando o `New-AzureRmResourceGroupDeployment` comando numa janela do PowerShell do Azure. Consulte o seguinte código para os parâmetros que passa para o comando. Para obter informações detalhadas sobre como implementar um grupo de recursos com o PowerShell, consulte o artigo [implementar um grupo de recursos com o modelo Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Implementar a extensão de diagnóstico para um cluster existente
Se tiver um cluster existente que não tenha implementado o diagnóstico ou se pretender modificar uma configuração existente, pode adicionar ou atualizar. Modificar o modelo do Resource Manager que é utilizado para criar o cluster existente ou transferir o modelo do portal conforme descrito anteriormente. Modificar o ficheiro de Template efetuando as seguintes tarefas.

Adicione um novo recurso de armazenamento para o modelo, adicionando a secção de recursos.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Em seguida, adicione a secção de parâmetros apenas depois das definições de conta de armazenamento, entre `supportLogStorageAccountName` e `vmNodeType0Name`. Substitua o texto do marcador de posição *nome da conta de armazenamento aqui* com o nome da conta de armazenamento.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Em seguida, atualize o `VirtualMachineProfile` secção do ficheiro Template adicionando o seguinte código dentro da matriz de extensões. Lembre-se de que adicione uma vírgula no início ou fim, consoante onde é inserido.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Depois de modificar o ficheiro Template, tal como descrito, voltar a publicar o modelo do Resource Manager. Se o modelo foi exportado, executar o ficheiro deploy.ps1 republishes o modelo. Depois de implementar, certifique-se de que **ProvisioningState** é **com êxito**.

## <a name="collect-health-and-load-events"></a>Recolher o estado de funcionamento e eventos de carga

Eventos de métricos de estado de funcionamento e a carga a partir de com a versão 5.4 do Service Fabric, estão disponíveis para a coleção. Estes eventos refletir os eventos gerados pelo sistema ou o seu código utilizando o estado de funcionamento ou carregar APIs de relatórios, tais como [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) ou [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Isto permite para agregar e visualizar o estado de funcionamento do sistema ao longo do tempo e para alertas com base em eventos de estado de funcionamento ou de carregamento. Para ver estes eventos no Visualizador de eventos de diagnóstico do Visual Studio adicionar "Microsoft-ServiceFabric:4:0x4000000000000008" à lista de fornecedores ETW.

Para recolher eventos do cluster, modifique o `scheduledTransferKeywordFilter` no WadCfg do seu modelo do Resource Manager `4611686018427387912`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>Recolher eventos de proxy inverso

Começando com a versão 5.7 do Service Fabric, [proxy inverso](service-fabric-reverseproxy.md) eventos estão disponíveis para a coleção através dos canais de mensagens & dados. 

O proxy reverso pushes apenas eventos de erro através do canal de mensagens & dados principal - ao refletir o pedido de processamento falhas e os problemas críticos. O canal de detalhado contém eventos verbosos sobre todos os pedidos processados do proxy inverso. 

Para ver os eventos de erro no Visualizador de eventos de diagnóstico do Visual Studio adicionar "Microsoft-ServiceFabric:4:0x4000000000000010" à lista de fornecedores ETW. Para a telemetria de pedido, atualizar a entrada de Microsoft ServiceFabric na lista de fornecedor ETW para "Microsoft-ServiceFabric:4:0x4000000000000020".

Para clusters em execução no Azure:

Para recolher rastreios de canal de mensagens & dados principal, modifique o `scheduledTransferKeywordFilter` valor WadCfg do seu modelo do Resource Manager `4611686018427387920`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

Para recolher eventos de todo o processamento de pedido, ative o serviço de mensagens - & dados detalhadas canal alterando o `scheduledTransferKeywordFilter` valor WadCfg do seu modelo do Resource Manager `4611686018427387936`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

Ativar a recolha de eventos deste detalhadas resultados de canal em muitos rastreios a ser gerado rapidamente e pode consumir a capacidade de armazenamento. Apenas ative esta opção quando for absolutamente necessário.
Resolução de erros detalhada os eventos de proxy inverso, consulte o [guia de diagnóstico de proxy inverso](service-fabric-reverse-proxy-diagnostics.md).

## <a name="collect-from-new-eventsource-channels"></a>Recolher novos canais de EventSource

Para atualizar o diagnóstico para recolher registos de novos canais de EventSource que representam uma nova aplicação que está a sobre a implementação, efetue os mesmos passos conforme descritos anteriormente para o programa de configuração de diagnósticos para existente cluster.

Atualizar o `EtwEventSourceProviderConfiguration` secção no ficheiro de Template para adicionar entradas para os canais de EventSource novo antes de aplicar a configuração de atualização utilizando o `New-AzureRmResourceGroupDeployment` comando do PowerShell. O nome da origem de evento está definido como parte do seu código no ficheiro ServiceEventSource.cs gerado pelo Visual Studio.

Por exemplo, se a origem do evento com o nome My Eventsource, adicione o seguinte código para colocar os eventos a partir do meu Eventsource numa tabela com o nome MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para recolher contadores de desempenho ou os registos de eventos, modifique o modelo do Resource Manager utilizando os exemplos fornecidos no [criar máquina virtual do Windows com a monitorização e diagnóstico utilizando um modelo Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Em seguida, voltar a publicar o modelo do Resource Manager.

## <a name="collect-performance-counters"></a>Recolher contadores de desempenho

Para recolher métricas de desempenho do seu cluster, adicione os contadores de desempenho para o seu "WadCfg > DiagnosticMonitorConfiguration" no modelo do Resource Manager para o cluster. Consulte [monitorização de desempenho com WAD](service-fabric-diagnostics-perf-wad.md) para obter os passos sobre a modificação do `WadCfg` para recolher contadores de desempenho específicos. Referência [contadores de desempenho de recursos de infraestrutura de serviço](service-fabric-diagnostics-event-generation-perf.md) para contadores de uma lista de desempenho, recomendamos a recolher.
  
Se estiver a utilizar um receptor de Application Insights, conforme descrito na secção abaixo e pretender que estas métricas apareçam no Application Insights, em seguida, certifique-se de que adiciona o nome de receptores na secção "sinks", conforme mostrado acima. Isto irá enviar automaticamente os contadores de desempenho que são configurados individualmente para o recurso do Application Insights.


## <a name="send-logs-to-application-insights"></a>Enviar registos ao Application Insights

Enviar dados de monitorização e diagnóstico para o Application Insights (AI) pode ser feita como parte da configuração WAD. Se optar por utilizar AI para análise de eventos e visualização, leia [análise de eventos e visualização com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) para configurar um receptor de AI como parte da sua "WadCfg".

## <a name="next-steps"></a>Passos seguintes

Assim que tiver configurado corretamente o diagnóstico do Azure, verá dados as tabelas de armazenamento a partir de registos do ETW e EventSource. Se optar por utilizar o OMS, Kibana ou quaisquer outro dados e visualização de análise plataforma que não é diretamente configurada no modelo do Resource Manager, certifique-se configurar a plataforma à sua escolha para ler nos dados destas tabelas de armazenamento. Fazê-lo para OMS é relativamente trivial e é explicado no [análise de eventos e o registo através de OMS](service-fabric-diagnostics-event-analysis-oms.md). O Application Insights está pouco num caso especial neste sentido, uma vez que pode ser configurado como parte da configuração da extensão de diagnóstico, por isso, consulte o [artigo adequado](service-fabric-diagnostics-event-analysis-appinsights.md) se optar por utilizar AI.

>[!NOTE]
>Atualmente, não há nenhuma forma de filtro ou tratar os eventos que são enviados para a tabela. Se não implemente um processo para remover eventos da tabela, a tabela continua a crescer. Atualmente, não há um exemplo de um serviço de tratamento de dados em execução [exemplo Watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service), e é recomendado que escreve uma por si, a menos que exista um bom motivo para armazenar os registos para além de um período de tempo de dia 30 ou 90.

* [Saiba como recolher contadores de desempenho ou os registos ao utilizar a extensão de diagnóstico](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análise de eventos e visualização com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análise de eventos e visualização com o OMS](service-fabric-diagnostics-event-analysis-oms.md)
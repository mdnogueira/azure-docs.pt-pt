---
title: "Monitorizar operações, eventos e contadores NSGs | Microsoft Docs"
description: Saiba como ativar os contadores, eventos e registo operacional para NSGs
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 552f37dd704de25159bc0f0ad34fdae9ed8b73f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Log analytics for network security groups (NSGs) (Análise de registos de grupos de segurança de rede (NSGs))

Pode ativar as seguintes categorias de registo de diagnóstico para os NSGs:

* **Evento:** contém entradas para o NSG regras são aplicadas às VMs e funções com base no endereço MAC de instância. O estado para estas regras é recolhido a cada 60 segundos.
* **Contador de regra:** contém entradas quantas vezes cada NSG regra é aplicada a negar e permitir o tráfego.

> [!NOTE]
> Os registos de diagnóstico só estão disponíveis para os NSGs implementados através do modelo de implementação Azure Resource Manager. Não é possível ativar o registo de diagnóstico para os NSGs implementadas através do modelo de implementação clássica. Para uma melhor compreensão dos dois modelos, referenciar a [modelos de implementação do Azure de compreender](../resource-manager-deployment-model.md) artigo.

Registo de atividade (anteriormente conhecido como auditoria ou registos operacionais) está ativado por predefinição para os NSGs criados através de um modelo de implementação do Azure. Para determinar as operações foram concluídas no NSGs no registo de atividade, procure entradas que contêm os seguintes tipos de recursos: 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

Leia o [descrição geral do registo de atividade do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) artigo para obter mais informações sobre os registos de atividade. 

## <a name="enable-diagnostic-logging"></a>Ativar o registo de diagnósticos

Registo de diagnóstico tem de estar ativado para *cada* NSG que pretende recolher dados para. O [descrição geral do Azure os registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artigo explica onde é possível enviar os registos de diagnóstico. Se não tiver um NSG existente, concluir os passos a [criar um grupo de segurança de rede](virtual-networks-create-nsg-arm-pportal.md) artigo para criar um. Pode ativar o NSG diagnóstico de registo utilizando qualquer um dos seguintes métodos:

### <a name="azure-portal"></a>Portal do Azure

Para utilizar o portal para ativar o registo de início de sessão para o [portal](https://portal.azure.com). Clique em **mais serviços**, em seguida, escreva *grupos de segurança de rede*. Selecione o NSG que pretende ativar o registo de. Siga as instruções para os recursos de computação não no [ativar registos de diagnóstico no portal do](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) artigo. Selecione **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter**, ou ambas as categorias de registos.

### <a name="powershell"></a>PowerShell

Para utilizar o PowerShell para ativar o registo, siga as instruções no [ativar registos de diagnóstico através do PowerShell](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) artigo. Avalie as seguintes informações antes de introduzir um comando do artigo:

- Pode determinar o valor a utilizar para o `-ResourceId` parâmetro ao substituir o seguinte [texto], conforme adequado, em seguida, introduzir o comando `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`. A ID de saída do comando semelhante */subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} [nome da subscrição Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG]*.
- Se pretender recolher dados de categoria do registo adicionar `-Categories [category]` até ao final do comando no artigo, em que categoria é *NetworkSecurityGroupEvent* ou *NetworkSecurityGroupRuleCounter*. Se não utilizar o `-Categories` parâmetro, a recolha de dados está ativado para categorias de registo.

### <a name="azure-command-line-interface-cli"></a>Interface de linha de comandos (CLI) do Azure

Para utilizar a CLI para ativar o registo, siga as instruções de [ativar registos de diagnóstico através da CLI](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs) artigo. Avalie as seguintes informações antes de introduzir um comando do artigo:

- Pode determinar o valor a utilizar para o `-ResourceId` parâmetro ao substituir o seguinte [texto], conforme adequado, em seguida, introduzir o comando `azure network nsg show [resource-group-name] [nsg-name]`. A ID de saída do comando semelhante */subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname} [nome da subscrição Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG]*.
- Se pretender recolher dados de categoria do registo adicionar `-Categories [category]` até ao final do comando no artigo, em que categoria é *NetworkSecurityGroupEvent* ou *NetworkSecurityGroupRuleCounter*. Se não utilizar o `-Categories` parâmetro, a recolha de dados está ativado para categorias de registo.

## <a name="logged-data"></a>Dados registados

Dados formatado em JSON são escritos para ambos os registos. Os dados específicos escritos para cada tipo de registo estão listados nas secções seguintes:

### <a name="event-log"></a>Registo de eventos
Este registo contém informações sobre o NSG regras são aplicadas às VMs e instâncias de função de serviço, com base no endereço MAC de nuvem. Os dados de exemplo seguintes são registados para cada evento:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>Registo de contador de regra

Este registo contém informações sobre cada regra aplicada aos recursos. Os seguintes dados de exemplo são registados sempre que uma regra é aplicada:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>Ver e analisar registos

Para saber como ver dados de registo de atividade, leia o [descrição geral do registo de atividade do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artigo. Para saber como ver dados de diagnóstico de registo, leia o [descrição geral do Azure os registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artigo. Se enviar dados de diagnóstico para análise de registos, pode utilizar o [análise do grupo de segurança de rede de Azure](../log-analytics/log-analytics-azure-networking-analytics.md) solução de gestão (pré-visualização) para insights avançadas. 

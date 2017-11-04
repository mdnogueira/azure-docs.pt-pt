---
title: "Exemplos de início rápido do Azure do Monitor CLI 1.0. | Microsoft Docs"
description: "Comandos de exemplo CLI 1.0 para funcionalidades de monitorização do Azure. Monitor do Azure é um serviço Microsoft Azure permite-lhe enviar notificações de alerta, chamar URLs web com base nos valores de dados de telemetria configurado e serviços de Cloud de dimensionamento automático, as máquinas virtuais e aplicações Web."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Exemplos de início rápido 1.0 Monitor plataforma CLI do Azure
Este artigo mostra-lhe exemplo comandos de interface de linha de comandos (CLI) para o ajudar a aceder às funcionalidades de monitorização do Azure. Monitor do Azure permite-lhe para serviços de Cloud de dimensionamento automático, as máquinas virtuais e aplicações Web e enviar notificações de alerta ou chamar URLs web com base nos valores de dados de telemetria configurado.

> [!NOTE]
> Monitor do Azure é o novo nome para o que foi chamado "Informações do Azure" até 25th de Setembro de 2016. No entanto, os espaços de nomes e, consequentemente, os comandos abaixo contém ainda as "informações".
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não instalou a CLI do Azure, consulte [instalar a CLI do Azure](../cli-install-nodejs.md). Se não estiver familiarizado com a CLI do Azure, pode ler mais sobre-lo no [utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

No Windows, instale npm do [Web site do Node.js](https://nodejs.org/). Depois de concluir a instalação, utilizando CMD.exe com privilégios de executar como administrador, execute o seguinte a partir da pasta onde npm está instalado:

```console
npm install azure-cli --global
```

Em seguida, navegue para qualquer/localização da pasta que pretende e escreva na linha de comandos:

```console
azure help
```

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
O primeiro passo consiste em início de sessão para a sua conta do Azure.

```console
azure login
```

Depois de executar este comando, tem de iniciar sessão através das instruções no ecrã. Posteriormente, para ver a conta, TenantId e predefinido ID da subscrição. Todos os comandos funcionam no contexto da sua subscrição predefinida.

Para listar os detalhes da subscrição atual, utilize o seguinte comando.

```console
azure account show
```

Para alterar o contexto de trabalho para uma subscrição diferente, utilize o seguinte comando.

```console
azure account set "subscription ID or subscription name"
```

Para utilizar comandos do Azure Resource Manager e o Monitor do Azure, tem de estar no modo Azure Resource Manager.

```console
azure config mode arm
```

Para ver uma lista de todos os comandos de Azure Monitor suportados, execute o seguinte.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>Ver o registo de atividade para uma subscrição
Para ver uma lista de eventos de registo de atividade, execute o seguinte.

```console
azure insights logs list [options]
```

Tente o seguinte para ver todas as opções disponíveis.

```console
azure insights logs list -help
```

Eis um exemplo para registos de lista por um resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Exemplo para registos de lista pelo autor da chamada

```console
azure insights logs list --caller "myname@company.com"
```

Exemplo para registos de lista pelo autor da chamada num tipo de recurso, dentro de uma data de início e de fim

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas
Pode utilizar as informações na secção para trabalhar com alertas.

### <a name="get-alert-rules-in-a-resource-group"></a>Obter as regras de alerta num grupo de recursos
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Criar uma regra de alerta métrica
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>Criar regra de alerta do teste Web
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Eliminar uma regra de alerta
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Perfis de registo
Utilize as informações nesta secção para trabalhar com perfis de registo.

### <a name="get-a-log-profile"></a>Obter um perfil de registo
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Adicionar um perfil de registo sem retenção
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Adicionar um perfil de registo com retenção
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicionar um perfil de registo com retenção e EventHub
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnóstico
Utilize as informações nesta secção para trabalhar com definições de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obter uma definição de diagnóstico
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Desativar a definição de diagnóstico
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Ativar uma definição de diagnóstico sem retenção
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Dimensionamento Automático
Utilize as informações nesta secção para trabalhar com definições de dimensionamento automático. Terá de modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obter definições de dimensionamento automático para um grupo de recursos
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obter definições de dimensionamento automático por nome de um grupo de recursos
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Configurar as definições de auotoscale
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

---
title: "Arquivar os registos de diagnóstico do Azure | Microsoft Docs"
description: "Saiba como arquivar os registos de diagnóstico do Azure para a retenção de longa duração numa conta do storage."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 3a55c73f-2ef3-45f3-8956-bcf9c0cb7e05
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem
ms.openlocfilehash: dbc5f89001dcb6cd1ab061cb0a9632e4e5d2c1c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="archive-azure-diagnostic-logs"></a>Arquivar os registos de diagnóstico do Azure
Neste artigo, mostramos como pode utilizar o portal do Azure, os Cmdlets do PowerShell, CLI ou REST API para arquivar a [registos de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md) numa conta do storage. Esta opção é útil se gostaria de manter os seus registos de diagnóstico com uma política de retenção opcional para auditoria, análise estático ou cópia de segurança. A conta de armazenamento tem de ser na mesma subscrição, como o recurso emitir os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, terá de [criar uma conta de armazenamento](../storage/storage-create-storage-account.md) ao qual pode arquivar os registos de diagnóstico. Recomendamos vivamente que utilize uma conta de armazenamento existente que tenha outros não monitorização dados armazenados na mesma forma que melhor pode controlar o acesso a dados de monitorização. No entanto, se também são arquivar o registo de atividade e métricas de diagnóstico para uma conta de armazenamento, poderá fazer sentido para utilizar essa conta de armazenamento para os registos de diagnóstico para manter os dados de monitorização de todos os numa localização central. A conta de armazenamento que utiliza tem de ser uma conta de armazenamento de objetivo geral, não uma conta do blob storage.

## <a name="diagnostic-settings"></a>Definições de diagnóstico
Para arquivar os registos de diagnóstico utilizando qualquer um dos métodos abaixo, definir uma **definição de diagnóstico** para um recurso específico. Uma definição de diagnóstico para um recurso define as categorias dos registos e os dados métricos enviados para um destino (conta de armazenamento, os Event Hubs espaço de nomes ou Log Analytics). Também define a política de retenção (número de dias a manter) para eventos de cada categoria de registo e a métricos dados armazenados numa conta do storage. Se uma política de retenção é definida para zero, eventos dessa categoria de registo são armazenados indefinidamente (que é dizer indefinidamente). Uma política de retenção caso contrário, pode ser qualquer número de dias entre 1 e 2147483647. [Pode ler mais sobre definições de diagnóstico aqui](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings). As políticas de retenção são aplicada por-dia, no fim do dia (UTC), registos a partir do dia em que é agora a retenção política será eliminada. Por exemplo, se tiver uma política de retenção de um dia, no início do dia de hoje os registos de ontem de antes do dia seriam eliminados

## <a name="archive-diagnostic-logs-using-the-portal"></a>Arquivo os registos de diagnóstico com o portal
1. No portal, navegue para o Monitor do Azure e clique em **definições de diagnóstico**

    ![Secção de monitorização do Monitor do Azure](media/monitoring-archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Opcionalmente filtrar a lista por tipo de recurso ou grupo de recursos, em seguida, clique no recurso para o qual pretende configurar uma definição de diagnóstico.

3. Se não existem definições de existem no recurso que selecionou, são-lhe pedido para criar uma definição. Clique em "Ativar o diagnóstico".

   ![Adicionar definição de diagnóstico - sem definições existentes](media/monitoring-archive-diagnostic-logs/diagnostic-settings-none.png)

   Se existirem definições existentes no recurso, irá ver uma lista das definições já configurada neste recurso. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/monitoring-archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Dê a definição de um nome e a caixa de verificação **exportar para a conta de armazenamento**, em seguida, selecione uma conta de armazenamento. Opcionalmente, defina um número de dias a manter estes registos utilizando o **retenção (dias)** controlos de deslize. Uma retenção de zero dias armazena os registos indefinidamente.
   
   ![Adicionar definição de diagnóstico - existente definições](media/monitoring-archive-diagnostic-logs/diagnostic-settings-configure.png)
    
4. Clique em **Guardar**.

Após alguns instantes, a nova definição é apresentada na lista de definições para este recurso e os registos de diagnóstico estão arquivados para essa conta do storage, assim como novos dados de evento são gerados.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Registos de diagnóstico de arquivo através do PowerShell do Azure
```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| ResourceId |Sim |ID de recurso dos recursos no qual pretende configurar uma definição de diagnóstico. |
| StorageAccountId |Não |ID de recurso da conta do Storage para o qual os registos de diagnóstico deverá ser guardados. |
| Categorias |Não |Lista separada por vírgulas das categorias de registo para ativar. |
| Ativado |Sim |Valor boleano que indica se o diagnóstico está ativado ou desativado neste recurso. |
| RetentionEnabled |Não |Valor boleano que indica se uma política de retenção estão ativadas neste recurso. |
| retentionInDays |Não |Número de dias para o qual os eventos devem ser mantidos entre 1 e 2147483647. Um valor de zero armazena os registos indefinidamente. |

## <a name="archive-diagnostic-logs-via-the-cross-platform-cli"></a>Registos de diagnóstico de arquivo através da CLI de plataforma
```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| resourceId |Sim |ID de recurso dos recursos no qual pretende configurar uma definição de diagnóstico. |
| storageId |Não |ID de recurso da conta do Storage para o qual os registos de diagnóstico deverá ser guardados. |
| Categorias |Não |Lista separada por vírgulas das categorias de registo para ativar. |
| ativado |Sim |Valor boleano que indica se o diagnóstico está ativado ou desativado neste recurso. |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Registos de diagnóstico de arquivo através da API REST
[Consulte este documento](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings) para obter informações sobre como pode configurar uma definição de diagnóstico utilizando a API de REST de Monitor do Azure.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Esquema dos registos de diagnóstico na conta de armazenamento
Assim que tiver configurado a arquivo, um contentor de armazenamento é criado na conta de armazenamento, assim que um evento ocorre em uma das categorias de registo que tiver ativado. Os blobs no contentor siga o mesmo formato em todos os registos de diagnóstico e o registo de atividade. A estrutura destas blobs é:

> insights - registos-{nome da categoria de registo} / resourceId = / subscrições / {ID de subscrição} /RESOURCEGROUPS/ {nome do grupo de recursos} /PROVIDERS/ {nome do fornecedor de recursos} / {tipo de recurso} / {nome do recurso} / y = {com quatro dígitos numérico year} / m = {dois dígitos numérico month} / d = {dois dígitos numérico day} / h = {: hour}/m=00/PT1H.json dois dígitos relógio de 24 horas
> 
> 

Ou, mais simples,

> insights - registos-{nome da categoria de registo} / resourceId = / {Id do recurso} / y = {com quatro dígitos numérico year} / m = {dois dígitos numérico month} / d = {dois dígitos numérico day} / h = {: hour}/m=00/PT1H.json dois dígitos relógio de 24 horas
> 
> 

Por exemplo, poderá ser um nome de blob:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro da hora especificada no URL do blob (por exemplo, h = 12). Durante a hora presente, os eventos são acrescentados para o ficheiro PT1H.json à medida que ocorrem. O valor de minuto (m = 00) é sempre 00, uma vez que os eventos de registo de diagnóstico são divididos em blobs individuais por hora.

No ficheiro PT1H.json cada evento está armazenado na matriz "registos", segue este formato:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nome do elemento | Descrição |
| --- | --- |
| hora |Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure. |
| resourceId |ID de recurso do recurso afetado. |
| operationName |Nome da operação. |
| categoria |Categoria de registo do evento. |
| propriedades |Conjunto de `<Key, Value>` pares (ou seja, dicionário) que descrevem os detalhes do evento. |

> [!NOTE]
> As propriedades e a utilização dessas propriedades podem variar consoante o recurso.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Transferir blobs para análise](../storage/storage-dotnet-how-to-use-blobs.md)
* [Registos de diagnóstico de fluxo para um espaço de nomes de Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Leia mais sobre os registos de diagnóstico](monitoring-overview-of-diagnostic-logs.md)

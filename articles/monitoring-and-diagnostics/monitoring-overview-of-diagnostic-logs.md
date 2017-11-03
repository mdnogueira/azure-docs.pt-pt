---
title: "Descrição geral dos registos de diagnóstico do Azure | Microsoft Docs"
description: "Saiba quais são os registos de diagnóstico do Azure e como pode utilizá-los para compreender os eventos de ocorrer dentro de um recurso do Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem; magoedte
ms.openlocfilehash: d59abde29fc7b73a799e5bf3659b02f824b693de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Recolher e consumir dados de registo dos seus recursos do Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Quais são os registos de diagnóstico de recursos do Azure
**Os registos de diagnóstico ao nível de recursos do Azure** são registos emitidos por um recurso que fornecem dados avançados, frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso. Por exemplo, os contadores de regra de grupo de segurança de rede e de auditorias do Cofre de chaves são duas categorias de registos de recursos.

Os registos de diagnóstico de nível de recursos sejam diferentes do [registo de atividade](monitoring-overview-activity-logs.md). O registo de atividade fornece informações sobre as operações que foram executadas no recursos na sua subscrição com o Resource Manager, por exemplo, criar uma máquina virtual ou eliminar uma aplicação lógica. O registo de atividade é um registo de nível de subscrição. Os registos de diagnóstico de nível de recursos fornecem informações aprofundadas operações que foram efetuadas no recurso de si próprio, por exemplo, obter um segredo do Cofre de chaves.

Os registos de diagnóstico de nível de recursos também diferem dos registos de diagnóstico de nível de SO convidado. Os registos de diagnóstico de SO convidado são esses recolhidos por um agente em execução dentro de uma máquina virtual ou outros suportado o tipo de recurso. Os registos de diagnóstico de nível de recursos requerem sem dados de recursos específicos de agente e captura da plataforma do Azure, enquanto os registos de diagnóstico de nível de SO convidado capturar os dados do sistema operativo e aplicações em execução numa máquina virtual.

Nem todos os recursos de suportar o novo tipo de recurso os registos de diagnóstico descrito aqui. Este artigo contém uma secção lista os tipos de recursos suportam os novos registos de diagnóstico de nível de recursos.

![Registos de diagnóstico de recurso vs outros tipos de registos ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>O que pode fazer com os registos de diagnóstico de nível de recursos
Seguem-se algumas das ações que pode fazer com os registos de diagnóstico de recursos:

![Posicionamento de lógico de registos de diagnóstico de recursos](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Guardá-las para um [ **conta de armazenamento** ](monitoring-archive-diagnostic-logs.md) de inspeção de auditoria ou manual. Pode especificar o tempo (em dias) de retenção utilizando **definições de diagnóstico de recurso**.
* [Fluxo sejam **Event Hubs** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) para ingestão por um serviço independente ou uma solução de análise personalizada, tais como o Power BI.
* Analisá-los com [análise de registos do OMS](../log-analytics/log-analytics-azure-storage.md)

Pode utilizar uma conta de armazenamento ou espaço de nomes de Event Hubs que não se encontra na mesma subscrição que emitir os registos. O utilizador que configura a definição tem de ter o acesso adequado do RBAC para ambas as subscrições.

## <a name="resource-diagnostic-settings"></a>Definições de diagnóstico de recursos
Registos de diagnóstico de recursos de não-computação recursos estão configurados com as definições de diagnóstico de recursos. **Definições de diagnóstico de recurso** para um controlo de recursos:

* Onde os registos de diagnóstico de recursos e as métricas são enviadas (conta de armazenamento, os Event Hubs, e/ou análise de registos do OMS).
* As categorias de registo são enviadas e se os dados métricos também são enviados.
* Quanto cada categoria de registo deve ser mantida na conta de armazenamento
    - Uma retenção de zero dias significa que os registos são mantidos indefinidamente. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.
    - Se as políticas de retenção estão definidas, mas armazenar os registos numa conta do Storage está desativada (por exemplo, se apenas as opções de Event Hubs ou OMS estão selecionadas), as políticas de retenção não tem qualquer efeito.
    - As políticas de retenção são aplicada por-dia, no fim do dia (UTC), registos a partir do dia em que é agora a retenção política são eliminadas. Por exemplo, se tiver uma política de retenção de um dia, no início do dia de hoje os registos de ontem de antes do dia seriam eliminados.

Estas definições são facilmente configuradas através de definições de diagnóstico para um recurso no portal do Azure, através do Azure PowerShell e comandos da CLI ou através de [API REST da Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Os registos de diagnóstico e métricas de camada de SO convidado de utilização de recursos (por exemplo, VMs ou Service Fabric) de computação de [um mecanismo separado para a configuração e seleção de saídas](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Como ativar a coleção de registos de diagnóstico de recursos
Coleção de registos de diagnóstico de recurso pode ser ativada [como parte da criação de um recurso num modelo do Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) ou depois de um recurso é criado a partir de página desse recurso no portal. Também pode ativar a coleção em qualquer momento utilizando os comandos do Azure PowerShell ou o CLI ou utilizando a API de REST de Monitor do Azure.

> [!TIP]
> Estas instruções não aplicadas diretamente a cada recurso. Consulte as ligações de esquema na parte inferior desta página para compreender os passos especiais que possam ser aplicadas a determinados tipos de recursos.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Ativar a recolha de registos de diagnóstico de recurso no portal
Pode ativar a coleção de registos de diagnóstico de recurso no portal do Azure depois de um recurso foi criado, acedendo a um recurso específico ou ao navegar para o Monitor do Azure. Para ativar esta opção através do Azure Monitor:

1. No [portal do Azure](http://portal.azure.com), navegue para o Monitor do Azure e clique em **definições de diagnóstico**

    ![Secção de monitorização do Monitor do Azure](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Opcionalmente filtrar a lista por tipo de recurso ou grupo de recursos, em seguida, clique no recurso para o qual pretende configurar uma definição de diagnóstico.

3. Se não existem definições de existem no recurso que selecionou, são-lhe pedido para criar uma definição. Clique em "Ativar o diagnóstico".

   ![Adicionar definição de diagnóstico - sem definições existentes](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   Se existirem definições existentes no recurso, irá ver uma lista das definições já configurada neste recurso. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Atribua a um nome da definição, marque as caixas para cada destino que pretende enviar dados e configurar o recurso é utilizado para cada destino. Opcionalmente, defina um número de dias a manter estes registos utilizando o **retenção (dias)** controlos de deslize (apenas aplicáveis para o destino de conta de armazenamento). Uma retenção de zero dias armazena os registos indefinidamente.
   
   ![Adicionar definição de diagnóstico - existente definições](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)
    
4. Clique em **Guardar**.

Após alguns instantes, a nova definição é apresentada na sua lista de definições para este recurso, e os registos de diagnóstico são enviados para os destinos especificados, assim como novos dados de evento são gerados.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Ativar a recolha de registos de diagnóstico de recursos através do PowerShell
Para ativar a recolha de registos de diagnóstico de recursos através do Azure PowerShell, utilize os seguintes comandos:

Para ativar o armazenamento dos registos de diagnóstico numa conta do storage, utilize este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

O ID de conta de armazenamento é o ID de recurso para a conta de armazenamento para o qual pretende enviar os registos.

Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos, utilize este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

O ID de regra de barramento de serviço é uma cadeia com este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para permitir o envio de registos de diagnóstico para uma área de trabalho de análise de registos, utilize este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Pode obter o ID de recurso da sua área de trabalho de análise de registos com o seguinte comando:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Ativar a recolha de registos de diagnóstico de recursos através da CLI
Para ativar a recolha de registos de diagnóstico de recursos através da CLI do Azure, utilize os seguintes comandos:

Para ativar o armazenamento dos registos de diagnóstico numa conta do Storage, utilize este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

O ID de conta de armazenamento é o ID de recurso para a conta de armazenamento para o qual pretende enviar os registos.

Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos, utilize este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

O ID de regra de barramento de serviço é uma cadeia com este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para permitir o envio de registos de diagnóstico para uma área de trabalho de análise de registos, utilize este comando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Ativar a recolha de registos de diagnóstico de recursos através de REST API
Para alterar as definições de diagnóstico utilizando a API de REST de Monitor do Azure, consulte [neste documento](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gerir definições de diagnóstico de recursos no portal
Certifique-se de que todos os seus recursos estão configurados com definições de diagnóstico. Navegue para **Monitor** no portal e abra **definições de diagnóstico**.

![Diagnóstico registos painel no portal](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Poderá ter de clicar em "Mais serviços" para localizar a secção do Monitor.

Aqui pode ver e filtrar todos os recursos que suportam as definições de diagnóstico para ver se têm diagnóstico ativado. Também pode desagregar Consulte se várias definições estão definidas num recurso e verifique que a conta de armazenamento, o espaço de nomes de Event Hubs e/ou as área de trabalho de análise de registos que são enviados dados para.

![Resultados de registos de diagnóstico no portal](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Adicionar uma definição de diagnóstico aparece a vista de definições de diagnóstico, onde pode ativar, desativar ou modificar as definições de diagnóstico para o recurso selecionado.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Serviços suportados, categorias e esquemas de registos de diagnóstico de recursos
[Consulte este artigo](monitoring-diagnostic-logs-schema.md) para uma lista completa dos serviços suportados e categorias de registo e esquemas utilizadas por esses serviços.

## <a name="next-steps"></a>Passos seguintes

* [Transmitir os registos de diagnóstico de recursos para **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Alterar definições de diagnóstico de recursos através da API de REST de Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analisar os registos do armazenamento do Azure com a análise de registos](../log-analytics/log-analytics-azure-storage.md)

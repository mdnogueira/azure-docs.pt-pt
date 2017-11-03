---
title: "Transmitir os registos de diagnóstico do Azure ao Log Analytics | Microsoft Docs"
description: "Saiba como transmitir os registos de diagnóstico do Azure para uma área de trabalho de análise de registos."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: johnkem
ms.openlocfilehash: 9440bd7f872914887c1f6e50f08a3c273536fcf8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Transmitir os registos de diagnóstico do Azure ao Log Analytics
**[Os registos de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)**  pode transmissão em fluxo em tempo real para análise de registos do Azure utilizando o portal, os cmdlets do PowerShell ou o CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>O que pode fazer com o diagnóstico de registos de análise de registos

Análise de registos do Azure é uma ferramenta de pesquisa e a análise de registo flexível que permite-lhe obter conhecimentos aprofundados sobre os dados em bruto de registo gerados a partir de recursos do Azure. Algumas funcionalidades incluem:

* **Pesquisa de registo** -escrita avançado consultas através de dados de registo, correlacionar os registos de várias origens e até mesmo geram gráficos podem afixar ao dashboard do Azure.
* **Alertas** -detetar quando um ou mais eventos correspondem a uma consulta específica e ficarem notificados com uma chamada de e-mail ou o webhook.
* **Soluções** -utilizar pré-criadas vistas e dashboards dão-lhe informações imediatas sobre os seus dados de registo.
* **Análise avançada** - aplicar aprendizagem e padrão algoritmos correspondentes para identificar possíveis problemas revelados pelos seus registos.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Ative a transmissão em fluxo de registos de diagnóstico ao Log Analytics
Pode ativar a transmissão em fluxo de registos de diagnóstico programaticamente, através do portal, ou utilizando o [as APIs REST da Azure Monitor](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). Qualquer forma, pode cria uma definição de diagnóstico no que especificou uma área de trabalho de análise de registos e as categorias de registo e métricas que pretende enviar para essa área de trabalho. Um diagnóstico **categoria de registo** é um tipo de registo que pode fornecer um recurso.

A área de trabalho de análise de registos não tem de estar na mesma subscrição, como o recurso emitir os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para ambas as subscrições.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Registos de diagnóstico de fluxo através do portal
1. No portal, navegue para o Monitor do Azure e clique em **definições de diagnóstico**

    ![Secção de monitorização do Monitor do Azure](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Opcionalmente filtrar a lista por tipo de recurso ou grupo de recursos, em seguida, clique no recurso para o qual pretende configurar uma definição de diagnóstico.

3. Se não existem definições de existem no recurso que selecionou, são-lhe pedido para criar uma definição. Clique em "Ativar o diagnóstico".

   ![Adicionar definição de diagnóstico - sem definições existentes](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Se existirem definições existentes no recurso, irá ver uma lista das definições já configurada neste recurso. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Dê a definição de um nome e a caixa de verificação **enviar ao Log Analytics**, em seguida, selecione uma área de trabalho de análise de registos.
   
   ![Adicionar definição de diagnóstico - existente definições](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

4. Clique em **Guardar**.

Após alguns instantes, a nova definição é apresentada na sua lista de definições para este recurso, e os registos de diagnóstico são transmissão em fluxo para essa área de trabalho, assim como novos dados de evento são gerados. Tenha em atenção que podem ser quinze minutos entre quando é emitido um evento e quando for apresentada na análise de registos.

### <a name="via-powershell-cmdlets"></a>Através de Cmdlets do PowerShell
Para ativar a transmissão em fluxo através de [Cmdlets do PowerShell do Azure](insights-powershell-samples.md), pode utilizar o `Set-AzureRmDiagnosticSetting` cmdlet com os seguintes parâmetros:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Tenha em atenção que a propriedade workspaceID demora o ID de recurso do Azure completo da área de trabalho, não a área de trabalho ID/chave apresentada no portal da análise de registos.

### <a name="via-azure-cli"></a>Através da CLI do Azure
Para ativar a transmissão em fluxo através de [CLI do Azure](insights-cli-samples.md), pode utilizar o `insights diagnostic set` comando como esta:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --workspaceId <workspace resource ID> --categories <list of categories> --enabled true
```

Tenha em atenção que a propriedade workspaceId demora o ID de recurso do Azure completo da área de trabalho, não a área de trabalho ID/chave apresentada no portal da análise de registos.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Como posso consultar os dados na análise de registos?

No painel pesquisa de registo no portal ou a experiência de análise avançadas como parte da análise de registos, pode consultar os registos de diagnóstico como parte da solução de gestão do registo em tabela AzureDiagnostics. Também existem [várias soluções para os recursos do Azure](../log-analytics/log-analytics-add-solutions.md) pode instalar para obter informações imediatas sobre os dados de registo está a enviar para análise de registos.


## <a name="next-steps"></a>Passos seguintes
* [Leia mais sobre os registos de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)

---
title: "Recolher e analisar registos de eventos do Windows na análise de registos do OMS | Microsoft Docs"
description: "Registos de eventos do Windows são uma das origens de dados mais comuns utilizadas pela análise de registos.  Este artigo descreve como configurar a recolha de registos de eventos do Windows e detalhes dos registos que criarem no repositório de OMS."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: bwren
ms.openlocfilehash: ddead0903c7c5f29bc996e305699ced596d0a4f5
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Origens de dados de registo de eventos de Windows na análise de registos
Registos de eventos do Windows são uma das mais comuns [origens de dados](log-analytics-data-sources.md) para recolher dados com agentes do Windows, uma vez que muitas aplicações escrevem o registo de eventos do Windows.  Pode recolher eventos a partir de registos de padrão, tais como o sistema e de aplicações, além de especificar quaisquer registos personalizados criados pelas aplicações que precisar de monitorizar.

![Eventos do Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Os registos de eventos do Windows configurar
Configurar os registos de eventos do Windows do [menu dados nas definições de análise do registo](log-analytics-data-sources.md#configuring-data-sources).

Análise de registos só recolhe os eventos dos registos de eventos do Windows que são especificados nas definições.  Pode adicionar um registo de eventos, escrevendo o nome do registo e clicando em  **+** .  Para cada registo, apenas os eventos com as gravidades selecionados são recolhidos.  Verifique as gravidades para o registo específico que pretende recolher.  Não é possível fornecer quaisquer critérios adicionais para filtrar eventos.

À medida que escreve o nome de um registo de eventos, análise de registos fornece sugestões de nomes comuns de registo de eventos. Se o registo de que pretende adicionar não aparecer na lista, ainda pode adicioná-lo ao escrever o nome completo do registo. Pode encontrar o nome completo do registo utilizando o Visualizador de eventos. No Visualizador de eventos, abra o *propriedades* página para o registo e copie a cadeia do *nome completo* campo.

![Configurar os eventos do Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Recolha de dados
Análise de registos recolhe cada evento que corresponda a uma gravidade de um registo de eventos monitorizado selecionada como o evento é criado.  O agente regista o seu lugar em cada registo de eventos que recolhe a partir de.  Se o agente fique offline durante um período de tempo, em seguida, análise de registos recolhe os eventos de onde pela última vez foi deixada, mesmo que esses eventos foram criados enquanto o agente estava offline.  Há a possibilidade de, estes eventos para não ser recolhidas se o registo de eventos encapsula num wrapper com eventos uncollected seja substituídos enquanto o agente está offline.

>[!NOTE]
>Análise de registos não recolhe os eventos de auditoria criados pelo SQL Server a partir da origem *MSSQLSERVER* com o ID de evento 18453 que contém as palavras-chave - *clássico* ou *auditoria êxito* e palavra-chave *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Propriedades de registos de eventos do Windows
Registos de eventos do Windows têm um tipo de **eventos** e ter as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Nome do computador que o evento foi recolhido a partir de. |
| EventCategory |Categoria do evento. |
| EventData |Todos os dados de eventos em formato não processado. |
| EventID |Número de evento. |
| eventLevel |Gravidade do evento no formato numérico. |
| EventLevelName |Gravidade do evento no formato de texto. |
| Registo de eventos |Nome do registo de eventos a partir do foi recolhido o evento. |
| ParameterXml |Valores de parâmetro de eventos no formato XML. |
| ManagementGroupName |Nome do grupo de gestão de agentes do System Center Operations Manager.  Para outros agentes, este valor é AOI-<workspace ID> |
| RenderedDescription |Descrição do evento com valores de parâmetro |
| Origem |Origem do evento. |
| SourceSystem |Tipo de agente que foi recolhido o evento de. <br> Ligar OpsManager – o agente do Windows, é direta ou gerido do Operations Manager <br> Linux – todos os agentes Linux  <br> AzureStorage – diagnóstico do Azure |
| TimeGenerated |Data e hora em que o evento foi criado no Windows. |
| Nome de utilizador |Nome de utilizador da conta que registou o evento. |

## <a name="log-searches-with-windows-events"></a>Registo de pesquisas com eventos do Windows
A tabela seguinte fornece exemplos diferentes de pesquisas de registo que obter registos de eventos do Windows.

| Consulta | Descrição |
|:---|:---|
| Evento |Todos os eventos do Windows. |
| Evento &#124; onde EventLevelName = = "erro" |Todos os eventos do Windows com uma gravidade de erro. |
| Evento &#124; resumir existente por origem |Eventos de contagem de Windows pela origem. |
| Evento &#124; onde EventLevelName = = "error" &#124; resumir existente por origem |Eventos de erro de contagem de Windows pela origem. |


## <a name="next-steps"></a>Passos seguintes
* Configurar a análise de registos para recolher outros [origens de dados](log-analytics-data-sources.md) para análise.
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções.  
* Utilize [campos personalizados](log-analytics-custom-fields.md) para analisar os registos de eventos em campos individuais.
* Configurar [recolha de contadores de desempenho](log-analytics-data-sources-performance-counters.md) de agentes do Windows.

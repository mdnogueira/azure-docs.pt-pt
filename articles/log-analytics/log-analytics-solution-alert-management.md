---
title: "Alerta de solução de gestão no Operations Management Suite (OMS) | Microsoft Docs"
description: "A solução de gestão de alertas no Log Analytics ajuda a analisar todos os alertas no seu ambiente.  Para além de consolidar os alertas gerados no OMS,-importa alertas de grupos de gestão do System Center Operations Manager ligados para análise de registos."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: bwren
ms.openlocfilehash: 4a394ead09794c52e92dbd2db8be8166d1fab83b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="alert-management-solution-in-operations-management-suite-oms"></a>Solução de gestão de alerta no Operations Management Suite (OMS)

![Ícone de gestão de alertas](media/log-analytics-solution-alert-management/icon.png)

A solução de gestão de alertas ajuda a analisar todos os alertas no seu repositório de análise de registos.  Estes alertas podem ter provenientes de uma variedade de origens, incluindo as origens [criados pela análise de registos](log-analytics-alerts.md) ou [importados a partir da Nagios ou da Zabbix](log-analytics-linux-agents.md).  A solução também importa alertas a partir de qualquer [ligado grupos de gestão do System Center Operations Manager](log-analytics-om-agents.md).

## <a name="prerequisites"></a>Pré-requisitos
A solução funciona com qualquer registos no repositório de análise de registos com um tipo de **alerta**, por isso, tem de efetuar qualquer configuração é necessária para recolher estes registos.

- Existência de alertas de análise de registos, [criar regras de alertas](log-analytics-alerts.md) para criar registos de alerta diretamente no repositório.
- Existência de alertas da Nagios e da Zabbix, [configurar esses servidores](log-analytics-linux-agents.md) para enviar alertas para análise de registos.
- Existência de alertas do System Center Operations Manager, [ligue o grupo de gestão do Operations Manager a sua área de trabalho de análise de registos](log-analytics-om-agents.md).  Todos os alertas criados no System Center Operations Manager são importados para análise de registos.  

## <a name="configuration"></a>Configuração
Adicionar a solução de gestão de alertas a sua área de trabalho do OMS utilizando o processo descrito no [adicionar soluções](log-analytics-add-solutions.md).  Não há nenhuma configuração adicional.

## <a name="management-packs"></a>Pacotes de gestão
Se o grupo de gestão do System Center Operations Manager está ligado à sua área de trabalho do OMS, em seguida, os seguintes pacotes de gestão estão instalados no System Center Operations Manager ao adicionar esta solução.  Não há nenhuma configuração ou a manutenção dos pacotes de gestão necessários.  

* Gestão de alertas do Microsoft System Center Advisor (Microsoft.IntelligencePacks.AlertManagement)

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](log-analytics-om-agents.md).

## <a name="data-collection"></a>Recolha de dados
### <a name="agents"></a>Agentes
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suporte | Descrição |
|:--- |:--- |:--- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não |Agentes diretos do Windows não gerar alertas.  É possível criar alertas de análise do registo de eventos e dados de desempenho recolhidos a partir do Windows, os agentes. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não |Diretos agentes Linux não gerar alertas.  Alertas de análise do registo podem ser criadas de eventos e os dados de desempenho recolhidas de agentes Linux.  Alertas da Nagios e da Zabbix são recolhidas a partir desses servidores que requerem o agente Linux. |
| [Grupo de gestão do System Center Operations Manager](log-analytics-om-agents.md) |Sim |Alertas que são gerados agentes do Operations Manager são fornecidos para o grupo de gestão e, em seguida, reencaminhados para a análise de registos.<br><br>Não é necessária uma ligação direta de agentes do Operations Manager para análise de registos. Dados de alerta são reencaminhados do grupo de gestão para o repositório de análise de registos. |


### <a name="collection-frequency"></a>Frequência da recolha
- Registos de alerta estão disponíveis para a solução, assim que são armazenados no repositório.
- Dados de alertas são enviados do grupo de gestão do Operations Manager para análise de registos a cada três minutos.  

## <a name="using-the-solution"></a>Utilizar a solução
Quando adiciona a solução de gestão de alertas a sua área de trabalho do OMS, a **gestão de alertas** mosaico é adicionado ao seu dashboard do OMS.  Este mosaico mostra uma contagem e a representação gráfica do número de alertas atualmente ativos que foram gerados nas últimas 24 horas.  Não é possível alterar este intervalo de tempo.

![Mosaico de gestão de alertas](media/log-analytics-solution-alert-management/tile.png)

Clique em de **gestão de alertas** mosaico para abrir o **gestão de alertas** dashboard.  O dashboard inclui as colunas da tabela seguinte.  Cada coluna apresenta os alertas de 10 principais por contagem correspondentes aos critérios dessa coluna para o âmbito especificado e o intervalo de tempo.  Pode executar uma pesquisa de registo que fornece a lista completa clicando **ver todos os** na parte inferior da coluna ou ao clicar no cabeçalho da coluna.

| Coluna | Descrição |
|:--- |:--- |
| Alertas críticos |Todos os alertas com uma gravidade de crítico agrupado por nome do alerta.  Clique num nome do alerta para executar uma pesquisa de registo devolver todos os registos para esse alerta. |
| Alertas de aviso |Todos os alertas com uma gravidade de aviso agrupado por nome do alerta.  Clique num nome do alerta para executar uma pesquisa de registo devolver todos os registos para esse alerta. |
| Alertas ativos do SCOM |Todos os alertas recolhidos a partir do Operations Manager com qualquer Estado diferente de *fechado* agrupados por origem que gerou o alerta. |
| Todos os alertas ativos |Todos os alertas com qualquer gravidade agrupados por nome do alerta. Incluir apenas alertas do Operations Manager com qualquer Estado diferente de *fechado*. |

Se deslocar para a direita, o dashboard apresenta uma lista de várias consultas comuns que pode clicar em para efetuar um [pesquisa registo](log-analytics-log-searches.md) para dados de alertas.

![Dashboard de gestão de alertas](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução de gestão de alertas analisa qualquer registo com um tipo de **alerta**.  Alertas criados pela análise de registos ou recolhidos a partir da Nagios ou da Zabbix não são recolhidas diretamente pela solução.

A solução de importar alertas do System Center Operations Manager e cria um registo correspondente para cada um com um tipo de **alerta** e um SourceSystem de **OpsManager**.  Estes registos de ter as propriedades na tabela seguinte:  

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*OpsManager* |
| AlertContext |Detalhes do item de dados que causou o alerta ser gerado no formato XML. |
| AlertDescription |Descrição detalhada do alerta. |
| AlertId |GUID do alerta. |
| AlertName |Nome do alerta. |
| AlertPriority |Nível de prioridade do alerta. |
| AlertSeverity |Nível de gravidade do alerta. |
| AlertState |Mais recente estado de resolução do alerta. |
| LastModifiedBy |Nome do utilizador que o alerta da última modificação. |
| ManagementGroupName |Nome do grupo de gestão onde o alerta foi gerado. |
| RepeatCount |Número de vezes que o mesmo alerta foi gerado para o mesmo monitorizados objeto desde que está a ser resolvido. |
| ResolvedBy |Nome do utilizador que o alerta resolvido. Vazio se o alerta ainda não foi resolvido. |
| SourceDisplayName |Nome a apresentar do objecto de monitorização que gerou o alerta. |
| SourceFullName |Nome completo do objeto de monitorização que gerou o alerta. |
| TicketId |ID de permissão para o alerta se o ambiente do System Center Operations Manager está integrado com um processo para atribuir permissões para alertas.  ID vazio de nenhuma permissão está atribuído. |
| TimeGenerated |Data e hora em que o alerta foi criado. |
| TimeLastModified |Data e hora em que o alerta foi alterado pela última vez. |
| TimeRaised |Data e hora em que o alerta foi gerado. |
| TimeResolved |Data e hora em que o alerta foi resolvido. Vazio se o alerta ainda não foi resolvido. |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela seguinte fornece pesquisas de registo de exemplo para registos alertas recolhidos por esta solução: 

| Consulta | Descrição |
|:--- |:--- |
| Tipo = SourceSystem alerta = OpsManager AlertSeverity = erro TimeRaised > agora 24 horas |Alertas críticos gerados nas últimas 24 horas |
| Tipo = AlertSeverity alerta = aviso TimeRaised > agora 24 horas |Alertas de aviso gerados nas últimas 24 horas |
| Tipo = SourceSystem alerta = OpsManager AlertState! = TimeRaised fechado > agora 24 horas &#124; medida existente como contagem por SourceDisplayName |Origens com alertas ativos gerados nas últimas 24 horas |
| Tipo = SourceSystem alerta = OpsManager AlertSeverity = erro TimeRaised > agora 24 horas AlertState! = fechado |Alertas críticos gerados nas últimas 24 horas que continuam ativos |
| Tipo = SourceSystem alerta = OpsManager TimeRaised > agora 24 horas AlertState = fechado |Alertas gerados nas últimas 24 horas que agora estão fechadas |
| Tipo = SourceSystem alerta = OpsManager TimeRaised > agora - 1 dia &#124; medida existente como contagem por AlertSeverity |Alertas gerados durante o último dia agrupado pelo respetivo grau de gravidade |
| Tipo = SourceSystem alerta = OpsManager TimeRaised > agora - 1 dia &#124; Ordenar RepeatCount desc |Alertas gerados durante o último dia, ordenados pelo respetivo valor de contagem de repetições |


>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, as consultas anteriores alteraria o seguinte:
>
>| Consulta | Descrição |
|:---|:---|
| Alertar &#124; onde SourceSystem = = "OpsManager" e AlertSeverity = = "error" e TimeRaised > ago(24h) |Alertas críticos gerados nas últimas 24 horas |
| Alertar &#124; onde AlertSeverity = = "aviso" e TimeRaised > ago(24h) |Alertas de aviso gerados nas últimas 24 horas |
| Alertar &#124; onde SourceSystem = = "OpsManager" e AlertState! = "Fechado" e TimeRaised > ago(24h) &#124; resumir contagem = existente pelo SourceDisplayName |Origens com alertas ativos gerados nas últimas 24 horas |
| Alertar &#124; onde SourceSystem = = "OpsManager" e AlertSeverity = = "error" e TimeRaised > ago(24h) e AlertState! = "Fechado" |Alertas críticos gerados nas últimas 24 horas que continuam ativos |
| Alertar &#124; onde SourceSystem = = "OpsManager" e TimeRaised > ago(24h) e AlertState = = "Fechado" |Alertas gerados nas últimas 24 horas que agora estão fechadas |
| Alertar &#124; onde SourceSystem = = "OpsManager" e TimeRaised > ago(1d) &#124; resumir contagem = existente pelo AlertSeverity |Alertas gerados durante o último dia agrupado pelo respetivo grau de gravidade |
| Alertar &#124; onde SourceSystem = = "OpsManager" e TimeRaised > ago(1d) &#124; Ordenar por RepeatCount desc |Alertas gerados durante o último dia, ordenados pelo respetivo valor de contagem de repetições |


## <a name="next-steps"></a>Passos seguintes
* Veja o artigo [Alerts in Log Analytics](log-analytics-alerts.md) (Alertas no Log Analytics) para obter detalhes sobre a geração de alertas do Log Analytics.

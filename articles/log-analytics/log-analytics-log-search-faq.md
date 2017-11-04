---
title: "Pesquisa de registo do registo análise nova perguntas mais frequentes | Microsoft Docs"
description: "Este artigo fornece perguntas mais frequentes sobre a atualização de análise de registos para o novo idioma de consulta."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: bwren
ms.openlocfilehash: 1ec815a12cea98228dd4b7ac7361fe5e3554b5d3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Análise de registos nova registo pesquisa FAQ e problemas conhecidos

Este artigo inclui perguntas mais frequentes e problemas conhecidos sobre a atualização da [análise de registos para o novo idioma de consulta](log-analytics-log-search-upgrade.md).  Deve Leia este artigo completo antes de efetuar a decisão de atualizar a sua área de trabalho.


## <a name="alerts"></a>Alertas

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Pergunta: Tenho uma grande quantidade de regras de alertas. É necessário para criá-los novamente no novo idioma depois atualizo?  
Não, as regras de alerta são automaticamente convertidas para o novo idioma de pesquisa durante a atualização.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Pergunta: Tenho de regras de alertas com ações de webhook e o runbook. Estes irão continuar a funcionar durante a atualização?

Não, existem algumas alterações as ações de webhook e runbook que exijam a efetuar alterações em como processar o payload. Fizemos estas alterações para normalizar os vários formatos de saída e reduzir o tamanho da payload do. Detalhes sobre estes formatos são no [adicionar ações para regras de alertas na análise de registos](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Grupos de computadores

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Pergunta: Posso estou obter erros quando tenta utilizar grupos de computadores.  Alterou as respetivas sintaxe?
Sim, a sintaxe para utilizar o computador agrupa as alterações quando a sua área de trabalho é atualizada.  Consulte [pesquisas de registo de grupos de computadores na análise de registos](log-analytics-computer-groups.md) para obter mais detalhes.


## <a name="dashboards"></a>Dashboards

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Pergunta: Posso continuar a utilizar dashboards em uma área de trabalho atualizada?
A atualização, estão a começar o processo de depracating **My Dashboard**.  Pode continuar a utilizar qualquer mosaicos que adicionou ao dashboard antes da sua área de trabalho foi atualizada, mas não é possível editar os mosaicos ou adicionar novos.  Pode continuar a criar e editar as vistas de [estruturador de vistas](log-analytics-view-designer.md), que tem uma funcionalidade mais rica definida e também criar dashboards no portal do Azure.


## <a name="log-searches"></a>Pesquisas de registo

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Pergunta: Posso ter pesquisas guardadas fora da minha área de trabalho atualizada. Posso convertê-los para o novo idioma de pesquisa automaticamente?
Pode utilizar a ferramenta de idioma na página de pesquisa de registo para converter a cada um deles.  Não há nenhum método para converter automaticamente pesquisas vários sem atualizar a área de trabalho.

### <a name="question-why-are-my-query-results-not-sorted"></a>Pergunta: Por que razão são os meus os resultados da consulta não ordenados?
Resultados não são ordenados por predefinição no novo idioma de consulta.  Utilize o [operador de ordenação](https://go.microsoft.com/fwlink/?linkid=856079) para ordenar os resultados por uma ou mais propriedades.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Pergunta: Onde a vista de métricas aceda após posso atualizado?
A vista de métricas forneceu uma representação gráfica dos dados de desempenho a partir de uma pesquisa de registo.  Esta vista já não está disponível após a atualização.  Pode utilizar o [compor operador](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) à saída de formato de uma consulta um timechart.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Pergunta: Onde minify aceda depois posso atualizado?
Minify é uma funcionalidade que fornece uma vista resumida dos resultados da pesquisa.  Após a atualização, a opção de Minify já não é apresentada no portal de registo de pesquisa.  Pode obter uma funcionalidade semelhante com a nova pesquisa idioma a utilizar [reduzir](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) ou [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>API de Pesquisas de Registos

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Pergunta: A API de pesquisa de registo for atualizada quando atualizo?
O legado [API de pesquisa de registo](log-analytics-log-search-api.md) deixará de funcionar quando atualizar a sua área de trabalho.  Consulte [API de REST de análise de registos do Azure](https://dev.loganalytics.io/) para obter detalhes sobre a nova API.


## <a name="portals"></a>Portais

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Pergunta: Deve posso utilizar o novo portal de análise avançadas ou continuar a utilizar o portal de pesquisa de registo?
Pode ver uma comparação dos dois portais em [portais para criação e edição de consultas de registo no Log Analytics do Azure](log-analytics-log-search-portals.md).  Cada um tem vantagens distintas para que possa escolher a melhor uma para os seus requisitos.  É comum para escrever consultas no portal da análise avançada e cole-os outros locais como o estruturador de vistas.  Deverá ler sobre [problemas a considerar](log-analytics-log-search-portals.md#advanced-analytics-portal) quando fazer.


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Pergunta: Após a atualização, posso obter um erro ao tentar executar consultas e também estou a ver erros nas minhas vistas.

O browser necessita de acesso para os seguintes endereços para executar consultas de análise de registos após a atualização.  Se o seu browser está a aceder ao portal do Azure através de uma firewall, tem de ativar o acesso a estes endereços.

| URI | IP | Portas |
|:---|:---|:---|
| Portal.loganalytics.IO | Dinâmica | 80,443 |
| API.loganalytics.IO    | Dinâmica | 80,443 |
| Docs.loganalytics.IO   | Dinâmica | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Pergunta: Altera nada com a integração do Power BI?
Sim.  Assim que tiver sido atualizada a sua área de trabalho, em seguida, o processo para exportar dados de análise de registos para o Power BI deixará de funcionar.  As agendas existentes que criou antes de atualizar irão tornar-se desativada.  

Após a atualização, o Log Analytics do Azure utiliza a mesma plataforma do Application Insights e utilizar o mesmo processo para exportar as consultas de análises de registo para o Power BI como [o processo para exportar consultas do Application Insights para o Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Exportar para o Power BI agora ponto final de chamadas diretamente a API. Isto permite-lhe obter até 500 000 linhas ou 64,000,000 bytes de dados, exportar consultas longas e personalizar o tempo limite da consulta (tempo limite predefinido é 3 minutos e o tempo limite máximo é de 10 minutos).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Pergunta: O cmdlet do PowerShell de pesquisa de registo for atualizado quando atualizo?
O [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) vão ser preteridas depois de concluída a atualização de todas as áreas de trabalho.  Utilize o [cmdlet Invoke-LogAnalyticsQuery](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) para efetuar pesquisas de registo em áreas de trabalho atualizadas.




## <a name="resource-manager-templates"></a>Modelos do Resource Manager

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Pergunta: Posso criar uma área de trabalho atualizada com um modelo do Resource Manager?
Sim.  Tem de utilizar uma versão de API de 2017-03-15-preview e incluir um **funcionalidades** secção no seu modelo como no exemplo seguinte.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>Soluções

### <a name="question-will-my-solutions-continue-to-work"></a>Pergunta: Meu soluções vão continuar a funcionar?
Todas as soluções continuarão a funcionar numa área de trabalho atualizada, embora o seu desempenho irá melhorar se estes são convertidos para o novo idioma de consulta.  Não existe são problemas conhecidos com algumas soluções existentes que são descritas nesta secção.

### <a name="known-issue-perspectives-in-application-insights-connector"></a>Problema de conhecido: Perspetivas no conector do Application Insights
Perspetivas no [solução Application Insights conector](log-analytics-app-insights-connector.md) já não são suportadas na solução de conector do Application Insights.  Pode utilizar o estruturador de vistas para criar vistas personalizadas com dados do Application Insights.

### <a name="known-issue-backup-solution"></a>Problema de conhecido: solução de cópia de segurança
A solução de cópia de segurança não poderá recolher dados, se tiver sido instalado antes de atualizar uma área de trabalho. Desinstalar a solução e, em seguida, instale a versão mais recente.  A nova versão da solução não suporta a clássicos cofres de cópia de segurança, pelo que terá também de atualizar para os cofres dos serviços de recuperação continuar a utilizar a solução.

## <a name="upgrade-process"></a>Processo de atualização

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Pergunta: Tenho várias áreas de trabalho. Pode atualizar todas as áreas de trabalho ao mesmo tempo?  
Não.  Atualização aplica-se a uma única área de trabalho cada vez. Atualmente não há nenhuma forma de atualizar várias áreas de trabalho em simultâneo. Tenha em atenção que outros utilizadores da área de trabalho atualizado serão afetados bem.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Pergunta: Serão recolhidos na minha área de trabalho de dados de registo existentes modificados se atualizo?  
Não. Os dados de registo disponíveis para a sua pesquisas de área de trabalho não são afetados pela atualização. Pesquisas guardadas, alertas e vistas serão convertidas para o novo idioma de pesquisa automaticamente.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Pergunta: O que acontece se não atualizar o minha área de trabalho?  
A pesquisa de registo legado vai ser preterida nos próximos meses. Áreas de trabalho que não estão a ser atualizadas por essa hora serão automaticamente atualizadas.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Pergunta: Posso reverter novamente depois de atualizo?
Antes da disponibilidade geral, pode reverter a sua área de trabalho após a atualização.  Agora que o novo idioma atingiu disponibilidade geral, esta capacidade foi removida como podemos começar a extinguir a plataforma de legado.



## <a name="views"></a>Vistas

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Pergunta: Como criar uma nova vista com o estruturador de vistas?
Antes da atualização, pode criar uma nova vista com o estruturador de vistas a partir de um mosaico no dashboard principal.  Quando a sua área de trabalho estiver atualizada, este mosaico é removido.  Pode criar uma nova vista com o estruturador de vistas no portal do OMS, clicando na verde + clique no botão no menu à esquerda.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [atualizar a sua área de trabalho para a nova consulta de análise de registos idioma](log-analytics-log-search-upgrade.md).

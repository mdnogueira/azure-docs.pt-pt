---
title: "Solução Funcionamento de Agente no OMS| Microsoft Docs"
description: "Este artigo destina-se a ajudá-lo a aprender a utilizar esta solução para monitorizar o funcionamento dos seus agentes que reportam diretamente ao OMS ou ao System Center Operations Manager."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: magoedte
ms.openlocfilehash: b810e37e393ddab55500f636b72450789285a4f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#  <a name="agent-health-solution-in-oms"></a>Solução Funcionamento de Agente no OMS
A solução Funcionamento de Agente no OMS ajuda-o a determinar que agentes não respondem e que agentes estão a submeter dados operacionais, relativamente a todos os agentes que reportam diretamente à área de trabalho do OMS ou a um grupo de gestão do System Center Operations Manager ligado ao OMS.  Também pode controlar a quantidade de agentes que estão implementados, onde estão distribuídos geograficamente e fazer outras consultas, para estar a par da distribuição dos agentes implementados no Azure, noutros ambientes na cloud ou no local.    

## <a name="prerequisites"></a>Pré-requisitos
Antes de implementar esta solução, confirme que tem integrados na sua área de trabalho [agentes do Windows](../log-analytics/log-analytics-windows-agents.md) que reportam à área de trabalho do OMS ou a um [grupo de gestão do Operations Manager](../log-analytics/log-analytics-om-agents.md) atualmente suportados.    

## <a name="solution-components"></a>Componentes da solução
Esta solução consiste nos recursos seguintes que são adicionados à sua área de trabalho e a agentes ligados diretamente ou a grupos de gestão ligados do Operations Manager.

### <a name="management-packs"></a>Pacotes de gestão
Se o seu grupo de gestão do System Center Operations Manager estiver ligado a uma área de trabalho do OMS, são instalados os pacotes de gestão seguintes no Operations Manager.  Estes pacotes de gestão também são instalados em computadores Windows ligados diretamente após adicionar esta solução. Estes pacotes de gestão não envolvem qualquer configuração ou gestão.

* Pacote de Informações de Canal Direto de HealthAssessment do Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Pacote de Informações de Canal de Servidor de HealthAssessment do Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Configuração
Adicione a solução Funcionamento de Agente à sua área de trabalho do OMS através do processo descrito em [Add solutions](../log-analytics/log-analytics-add-solutions.md) (Adicionar soluções). Não há nenhuma configuração adicional.


## <a name="data-collection"></a>Recolha de dados
### <a name="supported-agents"></a>Agentes suportados
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows | Sim | Os eventos de heartbeat são recolhidos de agentes do Windows diretos.|
| Grupo de gestão do System Center Operations Manager | Sim | Os eventos de heartbeat são recolhidos de agentes que reportam ao grupo de gestão a cada 60 segundos e, depois, reencaminhados para o Log Analytics. Não é necessária uma ligação direta a partir dos agentes do Operations Manager ao Log Analytics. Os dados de eventos de heartbeat são reencaminhados do grupo de gestão para o repositório do Log Analytics.|

## <a name="using-the-solution"></a>Utilizar a solução
Quando adiciona a solução à sua área de trabalho do OMS, é adicionado o mosaico **Funcionamento de Agente** ao dashboard do OMS. Esse mosaico mostra o número total de agentes e o número de agentes sem resposta nas últimas 24 horas.<br><br> ![Mosaico Solução Funcionamento de Agente no dashboard](./media/oms-solution-agenthealth/agenthealth-solution-tile-homepage.png)

Clique no mosaico **Funcionamento de Agente** para abrir o dashboard **Funcionamento de Agente**.  O dashboard inclui as colunas da tabela seguinte. Cada coluna mostra os dez principais eventos por ocorrência que correspondem aos critérios dessa coluna relativamente ao intervalo de tempo especificado. Pode executar uma pesquisa de registos que forneça a lista completa ao selecionar **Ver tudo**, no canto inferior direito de cada coluna, ou ao clicar no cabeçalho das colunas.

| Coluna | Descrição |
|--------|-------------|
| Contagem de agentes ao longo do tempo | Uma tendência da contagem de agentes ao longo de um período de sete dias, para agentes do Linux e do Windows.|
| Contagem de agentes sem resposta | Uma lista dos agentes que não enviaram um heartbeat nas últimas 24 horas.|
| Distribuição por Tipo de SO | Uma partição do número de agentes do Windows e do Linux que tem no seu ambiente.|
| Distribuição por Versão de Agente | Uma partição das diferentes versões de agentes instaladas no seu ambiente e uma contagem de cada uma das versões.|
| Distribuição por Categoria de Agente | Uma partição das diferentes categorias de agentes que estão a enviar eventos de heartbeat: agentes diretos, agentes do OpsMgr ou o Servidor de Gestão do OpsMgr.|
| Distribuição por Grupo de Gestão | Uma partição dos diferentes Grupos de gestão do SCOM no seu ambiente.|
| Localização Geográfica dos Agentes | Uma partição dos diferentes países em que tem agentes e uma contagem total do número de agentes que foram instalados em cada país.|
| Contagem de Gateways Instalados | O número de servidores que têm instalado o Gateway do OMS e uma lista desses servidores.|

![Exemplo de dashboard da Solução Funcionamento de Agente](./media/oms-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução cria um tipo de registo no repositório do OMS.  

### <a name="heartbeat-records"></a>Registos de heartbeats
É criado um registo com o tipo de **Heartbeat**.  Estes registos têm as propriedades da tabela seguinte.  

| Propriedade | Descrição |
| --- | --- |
| Tipo | *Heartbeat*|
| Categoria | O valor é *Agente Direto*, *Agente do SCOM* ou *Servidor de Gestão do SCOM*.|
| Computador | O nome do computador.|
| OSType | Sistema operativo Windows ou Linux.|
| OSMajorVersion | Versão principal do sistema operativo.|
| OSMinorVersion | Versão inferior do sistema operativo.|
| Versão | Versão do Agente do OMS ou do Agente do Operations Manager.|
| SCAgentChannel | O valor é *Direto* e/ou *SCManagementServer*.|
| IsGatewayInstalled | Se o Gateway do OMS estiver instalado, o valor é *true*; caso contrário, é *false*.|
| ComputerIP | O endereço IP do computador.|
| RemoteIPCountry | A localização geográfica onde o computador está implementado.|
| ManagementGroupName | O nome do grupo de gestão do Operations Manager.|
| SourceComputerId | ID exclusivo do computador.|
| RemoteIPLongitude | Longitude da localização geográfica do computador.|
| RemoteIPLatitude | Latitude da localização geográfica do computador.|

Cada agente que reporta a um servidor de gestão do Operations Manager envia dois heartbeats e valor da propriedade SCAgentChannel vai incluir quer **Direct**, quer **SCManagementServer**, dependendo das origens de dados e das soluções do Log Analytics que ativou na sua subscrição do OMS. Se bem se lembra, os dados das soluções são enviados diretamente de um servidor de gestão do Operations Manager para o serviço Web do OMS ou, devido ao volume de dados recolhidos no agente, são enviados diretamente do agente para o serviço Web do OMS. Para eventos de heartbeat que têm o valor **SCManagementServer**, o valor de ComputerIP é o endereço IP do servidor de gestão, pois os dados são efetivamente carregados por este.  Relativamente a heatbeats em que SCAgentChannel está definido como **Direto**, é o endereço IP público do agente.  

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos que esta solução recolhe.

| Consulta | Descrição |
| --- | --- |
| Type=Heartbeat &#124; distinct Computer |Número total de agentes |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-24HOURS |Contagem de agentes sem resposta nas últimas 24 horas |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-15MINUTES |Contagem de agentes sem resposta nos últimos 15 minutos |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer IN {Type=Heartbeat TimeGenerated>NOW-24HOURS &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Computadores online (nas últimas 24 horas) |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer NOT IN {Type=Heartbeat TimeGenerated>NOW-30MINUTES &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Total de Agentes Offline nos Últimos 30 minutos (nas últimas 24 horas) |
| Type=Heartbeat &#124; measure countdistinct(Computer) by OSType |Ver uma tendência do número de agentes ao longo do tempo por OSType|
| Type=Heartbeat&#124;measure countdistinct(Computer) by OSType |Distribuição por Tipo de SO |
| Type=Heartbeat&#124;measure countdistinct(Computer) by Version |Distribuição por Versão de Agente |
| Type=Heartbeat&#124;measure count() by Category |Distribuição por Categoria de Agente |
| Type=Heartbeat&#124;measure countdistinct(Computer) by ManagementGroupName | Distribuição por Grupo de Gestão |
| Type=Heartbeat&#124;measure countdistinct(Computer) by RemoteIPCountry |Localização Geográfica dos Agentes |
| Type=Heartbeat IsGatewayInstalled=true&#124;Distinct Computer |Número de Gateways do OMS Instalados |


>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para a [nova linguagem de consulta do Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), as consultas acima serão alteradas para as seguintes.
>
>| Consulta | Descrição |
|:---|:---|
| Heartbeat &#124; distinct Computer |Número total de agentes |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Contagem de agentes sem resposta nas últimas 24 horas |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Contagem de agentes sem resposta nos últimos 15 minutos |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computadores online (nas últimas 24 horas) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Total de Agentes Offline nos Últimos 30 minutos (nas últimas 24 horas) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Ver uma tendência do número de agentes ao longo do tempo por OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribuição por Tipo de SO |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribuição por Versão de Agente |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribuição por Categoria de Agente |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribuição por Grupo de Gestão |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Localização Geográfica dos Agentes |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Número de Gateways do OMS Instalados |

## <a name="next-steps"></a>Passos seguintes

* Veja o artigo [Alerts in Log Analytics](../log-analytics/log-analytics-alerts.md) (Alertas no Log Analytics) para obter detalhes sobre a geração de alertas do Log Analytics.

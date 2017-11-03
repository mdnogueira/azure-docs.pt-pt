---
title: "Adicione as soluções de gestão do Log Analytics do Azure | Microsoft Docs"
description: "Operations Management Suite (OMS) soluções de gestão de análise de registos são uma coleção de regras de aquisição lógica, visualização e os dados que fornecem métricas pivoted em torno de uma área de problema específico."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ba1bb02c27fa040cc2daef4baf5c9ecc827d323
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>Adicione as soluções de gestão do Log Analytics do Azure à sua área de trabalho

Soluções de gestão de análise do registo são uma coleção de **lógica**, **visualização**, e **regras de aquisição de dados** que fornece métricas pivoted em torno de um determinado área de problema. Este artigo apresenta uma lista de soluções de gestão suportadas pela análise de registos e mostra como adicionar e remover para uma área de trabalho utilizando o portal do Azure. Também pode adicionar soluções no portal do OMS utilizando a Galeria de soluções.

Soluções de gestão permitem a informações mais aprofundadas para:

* Ajudar a investigar e resolver problemas operacionais mais rápida
* Recolher e correlacionar vários tipos de dados da máquina
* Ajudar a ser proativa com atividades que expõe a solução.

> [!NOTE]
> Análise de registos inclui funcionalidades de pesquisa de registo, por isso não terá de instalar uma solução de gestão para ativá-la. No entanto, obter visualizações de dados, pesquisa sugerida e insights, adicionando as soluções de gestão para a sua área de trabalho.

Utilizar este artigo, adicione as soluções de gestão para uma área de trabalho no portal do Azure Marketplace. Depois de adicionar uma solução, os dados são recolhidos a partir de servidores na sua infraestrutura e enviados para o serviço do OMS. Processamento pelo OMS serviço normalmente demora alguns minutos para uma hora. Depois do serviço processa os dados, que o possa visualizar na OMS.

Pode remover facilmente uma solução de gestão quando este já não é necessário. Quando remover uma solução de gestão, os respetivos dados não são enviados à OMS. Se tiver o escalão de preço gratuito, a remoção de uma solução pode reduzir a quantidade de dados utilizados, ajudando-o a se manter no quota diária de dados.

## <a name="view-available-management-solutions"></a>Soluções de gestão disponíveis de vista

No Azure marketplace contém a lista de [soluções de gestão para análise de registos](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Pode instalar as soluções de gestão do Azure marketplace clicando a **obtê-lo agora** ligação na parte inferior de cada solução.

## <a name="add-a-management-solution"></a>Adicionar uma solução de gestão
1. Se ainda não o fez, inicie sessão no [portal do Azure](https://portal.azure.com) através da sua subscrição do Azure.
2. No **novo** painel em **Marketplace**, selecione **monitorização + gestão**.
3. No **monitorização + gestão** painel, clique em **ver todos os**.  
    ![Monitorização + painel de gestão](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. À direita do **soluções de gestão**, clique em **mais**.
5. No **soluções de gestão** painel, selecione uma solução de gestão que pretende adicionar a uma área de trabalho.  
    ![Monitorização + painel de gestão](./media/log-analytics-add-solutions/management-solutions.png)  
6. No painel de solução de gestão, reveja as informações sobre a solução de gestão e, em seguida, clique em **criar**.
7. No painel *nome da solução de gestão*, selecione uma área de trabalho que queira associar à solução de gestão.
8. Opcionalmente, altere as definições da área de trabalho para a subscrição do Azure, o grupo de recursos e a localização. Também pode optar por **opções de automatização**. Clique em **Criar**.  
    ![área de trabalho da solução](./media/log-analytics-add-solutions/solution-workspace.png)  
9. Para começar a utilizar a solução de gestão que adicionou à sua área de trabalho, navegue até à **Log Analytics** > **subscrições** > ***denomedeáreadetrabalho***  >  **Descrição geral**. É apresentado um novo mosaico da sua solução de gestão. Clique no mosaico para abri-lo e começar a utilizar a solução após a recolha de dados para a solução.

## <a name="remove-a-management-solution"></a>Remover uma solução de gestão

1. No [portal do Azure](https://portal.azure.com), navegue para **Log Analytics** > **subscrições** > ***nome da área de trabalho*** e em seguida, no ***nome da área de trabalho*** painel, clique em **soluções**.
2. Na lista de soluções de gestão, selecione a solução que pretende remover.
3. No painel da solução para a sua área de trabalho, clique em **eliminar**.  
    ![eliminar a solução](./media/log-analytics-add-solutions/solution-delete.png)  
4. Na caixa de diálogo de confirmação, clique em **Sim**.

## <a name="offers-and-pricing-tiers"></a>Ofertas e escalões de preços

A tabela seguinte identifica que soluções de gestão pertencem a cada oferta do Operations Management Suite.
A tabela também identifica os escalões de preços que estão disponíveis para cada solução de gestão.
Todas as soluções na tabela seguinte são disponíveis a partir do portal do Azure e a Galeria de soluções no portal da análise de registos.

| Solução de gestão                                                                       | Oferta                                                                     | Escalões de preço<sup>1</sup>                                                 | Notas |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [Log Analytics da Atividade](log-analytics-activity.md)                                                                   | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | 90 dias de dados estão disponíveis gratuitamente<br>Dados não sujeitos a extremidade do escalão gratuito |
| [Avaliação do AD](log-analytics-ad-assessment.md)                                           | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Estado de Replicação do AD](log-analytics-ad-replication-status.md)                           | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [Agente de Funcionamento de Agente](../operations-management-suite/oms-solution-agenthealth.md)                                                                                | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | Dados não sujeitos a extremidade do escalão gratuito<br> Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [Gestão de Alertas](log-analytics-solution-alert-management.md)                            | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [Conector do Application Insights (pré-visualização)](log-analytics-app-insights-connector.md)                                               | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Automatização de trabalho híbrida](../automation/automation-hybrid-runbook-worker.md)                                                                     | <ul><li>Automatização e controlo</li></ul>                                  | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)                                                                         | Requer a sua área de trabalho de análise de registos para ser ligada a uma conta de automatização |
| [Análise de Gateway de aplicação do Azure](log-analytics-azure-networking-analytics.md)    | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Análise de grupo de segurança de rede do Azure](log-analytics-azure-networking-analytics.md)     | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Análise de SQL do Azure (pré-visualização)](log-analytics-azure-sql.md)                                                       | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br>Por&nbsp;nó&nbsp;(OMS)                                                                          | Requer a sua área de trabalho de análise de registos para ser ligada a uma conta de automatização|
| [Análise de Aplicações Web do Azure](log-analytics-azure-web-apps-analytics.md)     | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
|[Cópia de segurança](../backup/backup-introduction-to-azure-backup.md)                                                                                 | <ul><li>Conhecimentos aprofundados e análise</li></ul>                                   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)                                                                       | Necessita de um cofre de cópia de segurança clássico.<br> Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [Capacidade e o desempenho (pré-visualização)](log-analytics-capacity.md)                                                   | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Monitorização de Alterações](log-analytics-change-tracking.md)                                       | <ul><li>Automatização e controlo</li></ul>                                  | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)                                                                         | Requer a sua área de trabalho de análise de registos para ser ligada a uma conta de automatização |
| [Contentores](log-analytics-containers.md)                                                 | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [IT Service conector de gestão (pré-visualização)](log-analytics-itsmc-overview.md)                                              | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)     | |
| Monitorização de HBase do HDInsight <br>(Pré-visualização)                                                  | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Análise do Cofre de Chaves](log-analytics-azure-key-vault.md)                   | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Logic Apps B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)                    | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [Avaliação de Software Maligno](log-analytics-malware.md)                                            | <ul><li>Segurança e Conformidade</li></ul>                                 | Gratuito<br> Autónomo<br>Por&nbsp;nó&nbsp;(OMS)                                                                           | Se adicionar as soluções de segurança e conformidade depois 19 de Junho de 2017 [faturação seja por nó](https://azure.microsoft.com/pricing/details/security-compliance/), independentemente da área de trabalho de escalão de preço. Os primeiro 60 dias são gratuitos.  |
| [Monitor de Desempenho da Rede](log-analytics-network-performance-monitor.md) <br>  | <ul><li>Conhecimentos aprofundados e análise</li></ul>                                   | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)                                                                         | |
| [Análise do Office 365 (pré-visualização)](../operations-management-suite/oms-solution-office-365.md)                                                       | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Auditoria e segurança](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>Segurança&nbsp;e&nbsp;conformidade</li></ul>                       | Gratuito<br> Autónomo<br>Por&nbsp;nó&nbsp;(OMS)                                                                           | Recolher registos de eventos de segurança requer que esta solução<br>Se adicionar as soluções de segurança e conformidade depois 19 de Junho de 2017 [faturação seja por nó](https://azure.microsoft.com/pricing/details/security-compliance/), independentemente da área de trabalho de escalão de preço. Os primeiro 60 dias são gratuitos. |
| [Análise de recursos de infraestrutura de serviço (pré-visualização)](log-analytics-service-fabric.md)                     | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Mapa de serviço (pré-visualização)](../operations-management-suite/operations-management-suite-service-map.md) | <ul><li>Conhecimentos aprofundados e análise</li></ul>                      | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)                                                                         | Disponível nos EUA leste, Europa Ocidental e EUA Centro Oeste    |
| [Site Recovery](../site-recovery/site-recovery-overview.md)                                                                               | <ul><li>Conhecimentos aprofundados e análise</li></ul>                                   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)                                                                       | Necessita de um cofre de recuperação de Site clássico.<br> Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [Avaliação do SQL](log-analytics-sql-assessment.md)                                         | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| Iniciar/Parar VMs durante horas de inatividade<br>(Pré-visualização)                                              | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)                                                                         | Requer a sua área de trabalho de análise de registos para ser ligada a uma conta de automatização |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [O System Center Operations Manager Assessment (pré-visualização)](log-analytics-scom-assessment.md)  | <ul><li>Conhecimentos aprofundados e análise</li><li>Log Analytics</li></ul>        | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Gestão de Atualizações](../operations-management-suite/oms-solution-update-management.md)                                                                         | <ul><li>Automatização e controlo</li></ul>                                  | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)                                                                         | Requer a sua área de trabalho de análise de registos para ser ligada a uma conta de automatização |
| [Conformidade de atualização (pré-visualização)](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)                                                             | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | Sem encargos de dados ou nós<br>Dados não sujeitos a extremidade do escalão gratuito.<br> Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [Atualizar Preparação](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started)                                                          | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | Sem encargos de dados ou nós<br>Dados não sujeitos a extremidade do escalão gratuito.<br> Não está disponível para adicionar a partir do portal do Azure/marketplace. |
| [VMware monitorização (pré-visualização)](log-analytics-vmware.md)                                | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Standard<br> Premium&nbsp;(OMS)<br> Por&nbsp;GB&nbsp;(autónomo)<br> Por&nbsp;nó&nbsp;(OMS)   | |
| [Dados por fio 2.0 (pré-visualização)](log-analytics-wire-data.md)                                                                 | <ul><li>Conhecimentos aprofundados e análise</li></ul>                                   | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)                                                                         | Disponível nos EUA leste, Europa Ocidental e EUA Centro Oeste |

<sup>1</sup> o *padrão* e *Premium (OMS)* escalões de preço só estão disponível para os clientes que criou a sua área de trabalho de análise de registos antes de 21 de Setembro de 2016.

### <a name="community-provided-management-solutions"></a>Comunidade fornecida soluções de gestão

Soluções de Comunidade fornecida estão disponíveis a partir de [Galeria de modelo do Azure](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) e direta de autores.

| Solução de gestão               | Oferta                                                                     | Escalões de preço                         | Notas |
| ---                               | ---                                                                       | ---                                   | ---   |
| Todas as soluções de Comunidade fornecida  | <ul><li>Conhecimentos aprofundados&nbsp;e&nbsp;análise</li><li>Log Analytics</li></ul>   | Gratuito<br> Por&nbsp;nó&nbsp;(OMS)     |   Requer a sua área de trabalho de análise de registos para ser ligada a uma conta de automatização |




## <a name="data-collection-details"></a>Detalhes de recolha de dados
As tabelas seguintes mostram os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para soluções de gestão de análise de registos e as origens de dados. As tabelas são categorizadas por ofertas de solução, o que equacionar para [subscrição escalões de preço](https://go.microsoft.com/fwlink/?linkid=827926). A solução de análise de registos de atividade está disponível para todos os escalões de preços gratuitamente.

O agente de Windows de análise do registo e o agente do System Center Operations Manager são essencialmente iguais. O agente do Windows inclui funcionalidades adicionais para permitir que estabeleça ligação à área de trabalho OMS e encaminhar através de um proxy. Se utilizar um agente do Operations Manager, tem de ser visada como um agente do OMS para comunicar com o OMS. Agentes do Operations Manager nesta tabela são agentes do OMS que estão ligados ao Operations Manager. Consulte [estabelecer a ligação do Operations Manager ao Log Analytics](log-analytics-om-agents.md) para obter informações sobre como ligar o seu ambiente existente do Operations Manager a OMS.

> [!NOTE]
> O tipo de agente que utilizar determina a forma como os dados são enviados para o OMS, com as seguintes condições:
> - Pode utiliza o agente do Windows ou um agente do OMS anexado no Operations Manager.
> - Quando o Operations Manager é necessário, dados de agente do Operations Manager para a solução sempre são enviados à OMS a utilizar o grupo de gestão do Operations Manager. Além disso, quando o Operations Manager for necessário, apenas o agente do Operations Manager é utilizado pela solução.
> - Quando o Operations Manager não é necessário e a tabela mostra esses dados de agente do Operations Manager são enviadas à OMS a utilizar o grupo de gestão e, em seguida, dados de agente do Operations Manager sempre são enviados à OMS através de grupos de gestão. Agentes Windows ignorar o grupo de gestão e enviam os dados diretamente para OMS.
> - Quando os dados de agente do Operations Manager não são enviados através de um grupo de gestão, os dados com a em seguida, são enviados diretamente para o OMS, ignorando o grupo de gestão.

### <a name="insight--analytics--log-analytics"></a>Conhecimentos aprofundados & análise / análise de registo

| Solução de gestão | Plataforma | Agente de monitorização da Microsoft | Agente do Operations Manager | Storage do Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Log Analytics de Atividades | Azure |   |   |   |   |   | na notificação |
| Avaliação do AD |Windows |&#8226; |&#8226; |  |  |&#8226; |7 dias |
| Estado de Replicação do AD |Windows |&#8226; |&#8226; |  |  |&#8226; |5 dias |
| Estado de Funcionamento do Agente | Windows e Linux | &#8226; | &#8226; |   |   | &#8226; | um minuto |
| Gestão de alertas (da Nagios) |Linux |&#8226; |  |  |  |  |sobre chegada |
| Gestão de alertas (da Zabbix) |Linux |&#8226; |  |  |  |  |um minuto |
| Gestão de alertas (Operations Manager) |Windows |  |&#8226; |  |&#8226; |&#8226; |3 minutos |
| Conector do Application Insights (pré-visualização) | Azure |   |   |   |   |   | na notificação |
| Análise de Gateway de aplicação do Azure | Azure |   |   |   |   |   | na notificação |
| Análise de grupo de segurança de rede do Azure | Azure |   |   |   |   |   | na notificação |
| Análise de SQL do Azure (pré-visualização) |Windows |  |  |  |  |  | 10 minutos |
| Gestão de Capacidade |Windows |&#8226; |&#8226; |  |  |&#8226; |sobre chegada |
| Contentores | Windows e Linux | &#8226; | &#8226; |   |   |   | 3 minutos |
| Análise do Cofre de chaves |Windows |  |  |  |  |  |na notificação |
| Monitor de Desempenho da Rede | Windows | &#8226; | &#8226; |   |   |   | TCP handshakes a cada cinco segundos, dados enviados a cada 3 minutos |
| Análise do Office 365 (pré-visualização) |Windows |  |  |  |  |  |na notificação |
| Análise de recursos de infraestrutura de serviço |Windows |  |  |&#8226; |  |  |5 minutos |
| Mapa de Serviços | Windows e Linux | &#8226; | &#8226; |   |   |   | 15 segundos |
| Avaliação do SQL |Windows |&#8226; |&#8226; |  |  |&#8226; |7 dias |
| SurfaceHub |Windows |&#8226; |  |  |  |  |sobre chegada |
| O System Center Operations Manager Assessment (pré-visualização) | Windows | &#8226; | &#8226; |   |   | &#8226; | sete dias |
| Análise de atualização (pré-visualização) | Windows | &#8226; |   |   |   |   | 2 dias |
| VMware monitorização (pré-visualização) | Linux | &#8226; |   |   |   |   | 3 minutos |
| Ligar Dados |Windows (2012 R2 / 8.1 ou posterior) |&#8226; |&#8226; |  |  |  | um minuto |


### <a name="automation--control"></a>Automatização e controlo

| Solução de gestão | Plataforma | Agente de monitorização da Microsoft | Agente do Operations Manager | Storage do Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Automatização de trabalho híbrida | Windows | &#8226; | &#8226; |   |   |   | n/d |
| Monitorização de Alterações |Windows |&#8226; |&#8226; |  |  |&#8226; |hora a hora |
| Monitorização de Alterações |Linux |&#8226; |  |  |  |  |hora a hora |
| Gestão de Atualizações | Windows |&#8226; |&#8226; |  |  |&#8226; |pelo menos 2 vezes por dia e 15 minutos depois de instalar uma atualização |

### <a name="security--compliance"></a>Segurança e Conformidade

| Solução de gestão | Plataforma | Agente de monitorização da Microsoft | Agente do Operations Manager | Storage do Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Avaliação de antimalware |Windows |&#8226; |&#8226; |  |  |&#8226; |hora a hora |
| Auditoria e segurança<sup>1</sup> | Windows e Linux | parcial | parcial | parcial |   | parcial | vários |

<sup>1</sup> solução de segurança e de auditoria pode recolher registos de agentes do Windows, o Operations Manager e o Linux. Consulte [origens de dados](#data-sources) para obter informações de recolha de dados sobre:

- Syslog
- Registos de eventos de segurança do Windows
- Registos de firewall do Windows
- Registos de eventos do Windows



### <a name="protection--recovery"></a>Proteção e Recuperação

| Solução de gestão | Plataforma | Agente de monitorização da Microsoft | Agente do Operations Manager | Storage do Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Cópia de segurança | Azure |   |   |   |   |   | n/d |
| Azure Site Recovery | Azure |   |   |   |   |   | n/d |


### <a name="data-sources"></a>Origens de dados


| Origem de dados | Plataforma | Agente de monitorização da Microsoft | Agente do Operations Manager | Storage do Azure | O Operations Manager necessárias? | Dados de agente do Operations Manager enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Registos de atividade do Azure |Windows |  |  |  |  |  |na notificação |
| Registos de diagnóstico do Azure |Windows |  |  |  |  |  |na notificação |
| Métricas de diagnóstico do Azure |Windows |  |  |  |  |  |na notificação |
| ETW |Windows |  |  |&#8226; |  |  |5 minutos |
| Registos de IIS |Windows |&#8226; |&#8226; |&#8226; |  |  |5 minutos |
| Contadores de Desempenho |Windows |&#8226; |&#8226; |  |  |  |Mínimo de 10 segundos, conforme agendado |
| Contadores de Desempenho |Linux |&#8226; |  |  |  |  |Mínimo de 10 segundos, conforme agendado |
| Syslog |Linux |&#8226; |  |  |  |  |storage do Azure: 10 minutos; do agente: sobre chegada |
| Registos de eventos de segurança do Windows |Windows |&#8226; |&#8226; |&#8226; |  |  |armazenamento do Azure: 10 min; para o agente: sobre chegada |
| Registos de firewall do Windows |Windows |&#8226; |&#8226; |  |  |  |sobre chegada |
| Registos de eventos do Windows |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; |armazenamento do Azure: 10 min; para o agente: sobre chegada |



## <a name="preview-management-solutions-and-features"></a>Funcionalidades e soluções de gestão de pré-visualização
Ao executar um serviço e seguir práticas de devops, mas é possível para as parcerias com os clientes para desenvolver funcionalidades e soluções.

Durante pré-visualização privada, que lhe damos um pequeno grupo de acesso de clientes para uma implementação da funcionalidade ou solução para obter comentários e melhorar o antecipado. Esta implementação antecipada tem mínimas funcionalidades e capacidades operacionais.

O nosso objetivo é tentar coisas rapidamente para que possa encontrar o que funciona e o que não funciona. Iremos itere através deste processo até os comentários de clientes a pré-visualização privada informar-nos que está tudo prontos para uma versão de pré-visualização pública.

Durante a pré-visualização pública, iremos tornar a funcionalidade ou solução disponível para todos os utilizadores para obter mais comentários e validar a nossa dimensionamento e eficiência. Durante esta fase:

* Funcionalidades de pré-visualização aparecem no separador Definições e podem ser ativadas por qualquer utilizador.
* Pré-visualização soluções são adicionadas através da galeria do ou através de um script.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>O que posso saber sobre as funcionalidades de pré-visualização e soluções?
Estamos entusiasmados sobre as novas funcionalidades e soluções de gestão e adoram trabalhar consigo para desenvolvê-las.

Funcionalidades de pré-visualização e soluções não são mais adequadas para todos os utilizadores. Antes de pedir associar uma pré-visualização privada ou ativar uma versão de pré-visualização pública, certifique-se de que está a trabalhar OK com algo que está em desenvolvimento.

Quando ativar a funcionalidade de pré-visualização através do portal, verá um aviso reminding que a funcionalidade está em pré-visualização.

#### <a name="for-both-private-and-public-preview"></a>Para ambos *privada* e *pública* pré-visualização
As seguintes informações aplicam-se para a pré-visualizações públicas e privadas:

* Coisas sempre podem não a funcionar corretamente.
  * Problemas de intervalo de ser um annoyance secundária através para algo não está a funcionar de todo.
* Não há potencial para a pré-visualização ter um impacto negativo no seus sistemas / ambiente.
  * Vamos tentar evitar coisas negativos acontecer para os sistemas que estiver a utilizar com o OMS, mas por vezes, inesperadas coisas ocorrerem.
* Perda de dados / poderão ocorrer danos.
* Iremos poderá pedir-lhe recolher registos de diagnóstico ou outros dados para ajudar a resolver problemas.
* A funcionalidade ou solução, poderá ser removida (temporariamente ou permanentemente).
  * Com base no nosso learnings durante a pré-visualização, pode decidir não libertar a funcionalidade ou solução.
* Pré-visualizações poderão não funcionar ou poderão não foi testados com todas as configurações e poderá limitamos a:
  * Os sistemas operativos que podem ser utilizados (por exemplo, uma funcionalidade poderá só se aplicam ao Linux no pré-visualização).
  * O tipo de agente (MMA, Operations Manager) que pode ser utilizada (por exemplo, uma funcionalidade poderá não funcionar com o Operations Manager em pré-visualização).  
* Soluções de pré-visualização e funcionalidades não são abrangidas pelo contrato de nível de serviço.
* Utilização das funcionalidades de pré-visualização incorreu custos de utilização.
* Funcionalidades ou capacidades que precisa para a funcionalidade / solução para ser útil poderá estar em falta ou incompletas.
* Funcionalidades / soluções podem não estar disponíveis em todas as regiões.
* Funcionalidades / soluções não podem ser localizadas.
* Funcionalidades / soluções podem ter um limite no número de clientes ou dispositivos que podem utilizá-lo.
* Terá de utilizar os scripts para efetuar a configuração e para ativar a funcionalidade/solução.
* A interface de utilizador (IU) está incompleta e podem ser alteradas a partir do dia a dia.
* Pré-visualizações públicos não podem ser apropriada para a produção / críticos sistemas.

#### <a name="for-private-preview"></a>Para *privada* pré-visualização
Para além dos itens que se segue, são específicas do pré-visualizações privadas as seguintes informações:

* Esperamos que seja fornecem-nos comentários sobre a sua experiência, para que iremos pode melhorar a funcionalidade/solução.
* Iremos poderá contactá-lo para comentários utilizando inquéritos, chamadas de telefone ou e-mail.
* Coisas sempre não funcionam corretamente.
* Iremos pode necessitar de uma divulgação não contrato (contrato de confidencialidade) para participação ou pode incluir conteúdo confidencial.
  * Antes de blogging, tweeting ou, caso contrário a comunicar com terceiros, consulte com o Gestor de programa que é responsável pela pré-visualização compreender as restrições de divulgação.
* Não execute produção / críticos sistemas.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Como posso obter acesso a funcionalidades de pré-visualização privada e soluções?
Convidamo-aos clientes pré-visualizações privadas através de várias maneiras diferentes consoante a pré-visualização.

* A resposta de inquéritos mensal do cliente e nos dar permissão a seguir a cópia de segurança com melhora as possibilidades de que está a ser convidado para uma versão de pré-visualização privada.
* A equipa da conta Microsoft para pode indicar-lhe.
* Pode inscrever-com base nos detalhes do publicadas no twitter [msopsmgmt](https://twitter.com/msopsmgmt).
* Pode inscrever-com base em eventos de Comunidade partilhado detalhes – procure-nos em cumprem ups conferences e no comunidades online.

## <a name="next-steps"></a>Passos seguintes
* [Pesquisar registos](log-analytics-log-searches.md) para ver informações detalhadas recolhidas por soluções de gestão.

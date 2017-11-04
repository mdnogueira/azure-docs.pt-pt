---
title: Azure de registo e auditoria | Microsoft Docs
description: "Saiba mais sobre como pode utilizar dados de registo para obter conhecimentos aprofundados sobre a sua aplicação."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: TomSh
ms.openlocfilehash: 9e5c929251259a86944121e504dc033bc99e3bc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-logging-and-auditing"></a>Auditoria e registo do Azure
## <a name="introduction"></a>Introdução
### <a name="overview"></a>Descrição geral
Para ajudar a atuais e potenciais clientes do Azure compreender e utilizar as diversas relacionadas com segurança capacidades disponíveis no e envolvente a plataforma do Azure, a Microsoft desenvolveu uma série de documentos técnicos, descrições gerais de segurança, as melhores práticas e listas de verificação. Os tópicos em termos de volume e a profundidade de intervalo e são atualizados periodicamente. Este documento faz parte da série, conforme resumido na secção seguinte abstracta.
### <a name="azure-platform"></a>Plataforma do Azure
Azure é uma plataforma de serviço de nuvem aberta e flexível que suporte a seleção mais amplas de sistemas operativos, programação idiomas, estruturas, ferramentas, bases de dados e dispositivos.

Pode, por exemplo:
-   Execute os contentores de Linux com a integração do Docker.

-   Criar aplicações com JavaScript, Python, .NET, PHP, Java e Node.js

-   Compilação back-ends para iOS, Android e Windows dispositivos.

Serviços em nuvem pública do Azure suportam as mesmas tecnologias milhões de programadores e profissionais de TI já dependem e de confiança.

Quando criar ou migrar os recursos IT à, um fornecedor de nuvem, é depender capacidades da organização para proteger as suas aplicações e dados com os serviços e os controlos fornecem para gerir a segurança dos seus recursos baseados na nuvem.

A infraestrutura do Azure foi concebida a partir da facilidade que as aplicações têm para alojar milhões de clientes em simultâneo e proporciona uma fundação fidedigna com a qual as empresas podem satisfazer as suas necessidades de segurança. Além disso, o Azure disponibiliza-lhe um vasto leque de opções de segurança configuráveis e a capacidade para controlá-las de modo a que possa personalizar a segurança e satisfazer os requisitos únicos das suas implementações. Este documento ajuda-que cumpra estes requisitos.

### <a name="abstract"></a>Abstrato
Auditoria e registo de eventos relacionados com a segurança e alertas relacionados, são componentes importantes numa estratégia de proteção de dados em vigor. Registos de segurança e relatórios de fornecem um registo de atividades suspeitas e ajuda a detetar padrões que possam indicar tentada ou com êxito penetração externa da rede, bem como a ataques internos eletrónico. Pode utilizar a auditoria para monitorizar a atividade do utilizador, a conformidade regulamentar documento, executar análises forenses e muito mais. Os alertas fornecem notificação imediata quando ocorrem eventos de segurança.

Produtos e serviços Microsoft Azure fornecem-lhe configurável segurança de auditoria e registo opções para o ajudar a identificar lacunas na sua políticas de segurança e mecanismos e os intervalos para ajudar a evitar falhas de endereços. Serviços da Microsoft proporcionam algumas (e, em alguns casos, todas as) das seguintes opções: monitorização centralizada, registo e sistemas de análise para fornecer visibilidade contínua; alertas atempadas; e relatórios para ajudar a gerir a grande quantidade de informações geradas pelos dispositivos e serviços.

Dados de registo do Microsoft Azure podem ser exportados para sistemas de incidente de segurança e Event Management (SIEM) para análise e integra-se com soluções de auditorias de terceiros.

Este documento fornece uma introdução para gerar, recolher e analisar registos de segurança de serviços alojados no Azure e pode ajudar a obter informações de segurança para as implementações do Azure. O âmbito deste documento técnico está limitado a aplicações e serviços incorporados e implementado no Azure.

> [!Note]
> Algumas recomendações contidas neste documento podem resultar numa maior dados, a rede ou a utilização de recursos de computação e aumentar os custos de licenciamento ou de subscrição.

## <a name="types-of-logs-in-azure"></a>Tipos de registos no Azure
Aplicações em nuvem são complexas com várias partes mover. Fornecem registos de dados para se certificar de que a aplicação permanece cópias de segurança e em execução em bom estado. Também o ajuda a stave desativar potenciais problemas ou resolver passado aqueles. Além disso, pode utilizar dados de registo para obter conhecimentos aprofundados sobre a sua aplicação. Conhecimentos podem ajudar a melhorar o desempenho da aplicação ou maintainability ou automatizar ações que caso contrário necessitem intervenção manual.

Azure produz um vasto conjunto registo para cada serviço do Azure. Estes registos são categorizados por estes tipos principais:
-   **Registos de controlo/gestão** dar visibilidade para as operações do Azure Resource Manager criar, ATUALIZAR e eliminar. [Os registos de atividade do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) é um exemplo deste tipo de registo.

-   **Dados plane registos** dar visibilidade sobre os eventos gerados como parte da utilização de um recurso do Azure. Os exemplos deste tipo de registo são os eventos do Windows sistema, segurança, e os registos de aplicação numa máquina virtual e o [registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) configurada por meio do Monitor do Azure


-   **Processar eventos** fornecer informações sobre analisados/alertas de eventos que foram processados em seu nome. Os exemplos deste tipo são [alertas do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) onde [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) tem processado e analisados a sua subscrição e fornece alertas de segurança concisa

A tabela seguinte lista o tipo mais importante de registos disponíveis no Azure.

| Categoria de registo | Tipo de registo | Utilizações | Integração |
| ------------ | -------- | ------ | ----------- |
|[Registos de atividade](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Eventos de controlo plane nos recursos do Azure Resource Manager| Fornece informações sobre as operações que foram executadas no recursos na sua subscrição.| API de REST & [Monitor do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Registos de diagnóstico do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|dados frequentes sobre o funcionamento dos recursos do Azure Resource Manager na subscrição|   Fornecem informações aprofundadas operations que o seu recurso efetuadas próprio| Monitor do Azure, [fluxo](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Relatórios do AAD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-azure-portal)|Os registos e relatórios|Atividades de início de sessão de utilizador e as informações de atividade do sistema sobre utilizadores e gestão de grupo|[Graph API](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Máquinas virtuais e serviços Cloud](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Registo de eventos do Windows e Linux Syslog|  Captura de dados de sistema e dados de registo nas máquinas virtuais e transferências de dados para uma conta de armazenamento à sua escolha.|   Utilizar o Windows [WAD](https://docs.microsoft.com/en-us/azure/azure-diagnostics) (armazenamento de diagnóstico do Windows Azure) e Linux no monitor do Azure|
|[Análise de Armazenamento](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/storage-analytics)|Registo de armazenamento e fornece dados de métricas para uma conta de armazenamento|Fornece informações para pedidos de rastreio, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.|  REST API ou o [biblioteca de clientes](https://msdn.microsoft.com/en-us/library/azure/mt347887.aspx)|
|[Registos de fluxo NSG (grupo de segurança de rede)](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formato JSON e mostra os fluxos de saída e entrados numa base por regra|Ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede|[Observador de rede](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview)|
|[Informações de aplicação](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview)|Os registos, exceções e diagnóstico personalizado|  Serviço de gestão de desempenho (APM) de aplicação para programadores de web em várias plataformas.| REST API, [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/)|
|Processar os dados / alerta de segurança| Alerta de centro de segurança do Azure, o alerta do OMS| As informações de segurança e alertas.|   REST APIs, JSON|

### <a name="activity-log"></a>Registo de Atividades
O [registo de atividade do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), fornece informações sobre as operações que foram executadas no recursos na sua subscrição. O registo de atividade era anteriormente conhecido como "Registos de auditoria" ou "Registos operacionais", uma vez que esta comunica [eventos de controlo plane](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) para as suas subscrições. Utilizar o registo de atividade, poderá determinar o "o que, quem e quando" para quaisquer operações (PUT, POST, DELETE) efetuadas nos recursos na sua subscrição de escrita. Também pode compreender o estado da operação e outras propriedades relevantes. O registo de atividade não inclui as operações de leitura (GET).

Aqui PUT, POST, DELETE refere-se a todos os contém o registo de atividade de operações de escrita nos recursos. Por exemplo, pode utilizar os registos de atividade para encontrar um erro quando a resolução de problemas ou para monitorizar a forma como um utilizador na sua organização modificou um recurso.

![Registo de Atividades](./media/azure-log-audit/azure-log-audit-fig1.png)


Pode obter eventos a partir do seu registo de atividade no portal do Azure, [CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), cmdlets do PowerShell, e [API REST da Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Registos de atividade com o período de retenção de dados 19 dias.

Cenários de integração
-   [Crie um alerta de e-mail ou webhook que aciona desativar um evento de registo de atividade.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Transmiti-lo para um Hub de eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) para ingestão por um serviço independente ou uma solução de análise personalizada, tais como o Power BI.

-   Analisá-lo no Power BI utilizando o [pacote de conteúdos do Power BI.](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)

-   [Guarde-o para uma conta de armazenamento de inspeção de arquivo ou manual.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) Pode especificar o período de retenção (em dias) através de perfis de registo.

-   Consultar e vê-la no portal do Azure.

-   Consultá-lo através do Cmdlet do PowerShell, a CLI ou a REST API.

-   Exportar o registo de atividade com perfis de registo para [Iniciar análise](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Pode utilizar uma conta de armazenamento ou [espaço de nomes de hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) que não está na mesma subscrição, como a um registo de emissão. O utilizador que configura a definição tem de ter o adequado [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) acesso para ambas as subscrições
### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure
Os registos de diagnóstico do Azure são emitidos por um recurso que fornecem dados avançados, frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso (por exemplo, [registos de sistema de eventos do Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)são uma categoria de registo de diagnóstico para VMs e [blob, tabela e fila registos](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) são categorias de registos de diagnóstico para contas de armazenamento) e é diferente do registo de atividade, que fornece informações sobre as operações que foram executadas no recursos na sua subscrição.

![Registos de diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Os registos de diagnóstico do Azure oferecem várias opções de configuração que é, o portal do Azure, com o PowerShell, a interface de linha de comandos (CLI) e a REST API.

**Cenários de integração**
-   Guardá-las para um [conta de armazenamento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) de inspeção de auditoria ou manual. Pode especificar o período de retenção (em dias) utilizando as definições de diagnóstico.

-   [Transmiti-las para os Event Hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) para ingestão por um serviço independente ou uma solução de análise personalizada, tal como [PowerBI.](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

-   Analisá-los com [análise de registos do OMS.](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

**Serviços, suportados pelo esquema para os registos de diagnóstico e categorias de registo suportado por tipo de recurso**


| Serviço | Esquema & Docs | Tipo de Recurso | Categoria |
| ------- | ------------- | ------------- | -------- |
|Balanceador de Carga| [Análise de registos para o Balanceador de carga do Azure (pré-visualização)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|  LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Grupos de Segurança de Rede|[Análise de registos para grupos de segurança de rede (NSGs) (Log analytics for network security groups (NSGs))](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Gateways da Aplicação|[Registo de diagnóstico para o Gateway de aplicação](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)|Network/applicationgateways|ApplicationGatewayAccessLog|
|||Network/applicationgateways|ApplicationGatewayPerformanceLog|
|||Network/applicationgateways|ApplicationGatewayFirewallLog|
|Cofre de Chaves|[Registo do Cofre de Chaves do Azure](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Ativar e utilizar a análise de tráfego de pesquisa](https://docs.microsoft.com/en-us/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[Aceder a registos de diagnóstico para o Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Auditoria|
|Data Lake Analytics|[Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Auditoria|
|||Microsoft.DataLakeAnalytics/accounts|Pedidos|
|||Microsoft.DataLakeStore/accounts|Pedidos|
|Aplicações Lógicas|[Esquema de controlo personalizado B2B de Aplicações Lógicas](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Registo de diagnóstico de lote do Azure](https://docs.microsoft.com/en-us/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Automatização do Azure|[Análise de registos para a automatização do Azure](https://docs.microsoft.com/en-us/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Event Hubs|[Registos de diagnóstico dos Event Hubs do Azure](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[Registos de diagnóstico de tarefa](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Execução|
|||Microsoft.StreamAnalytics/streamingjobs|Criação|
|Service Bus|[Registos de diagnóstico de Service Bus do Azure](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Relatórios do Azure Active Directory
O Azure Active Directory (Azure AD) inclui relatórios de segurança, atividade e auditoria para o diretório. O [relatório de auditoria do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ajuda os clientes para identificar ações privilegiadas ocorridas no seu Azure Active Directory. Ações privilegiadas incluem alterações de elevação (por exemplo, a criação da função ou a reposição de palavra-passe), alterar as configurações da política (por exemplo políticas de palavra-passe) ou as alterações à configuração de diretório (por exemplo, alterações às definições de Federação do domínio).

Os relatórios fornecem o registo de auditoria para o nome de evento, o ator que efetuou a ação, o recurso de destino afetado por alteração e a data e hora (em UTC). Os clientes conseguem obter a lista de eventos de auditoria para o seu Azure Active Directory através do [portal do Azure](https://portal.azure.com/), conforme descrito nas [ver os registos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Aqui está uma lista dos relatórios incluídos:

| Relatórios de segurança | Relatórios de atividade | Relatórios de auditoria |
| :--------------- | :--------------- | :------------ |
|Inícios de sessão de fontes desconhecidas| Utilização da aplicação: resumo| Relatório de auditoria de diretório|
|Inícios de sessão após várias falhas|  Utilização da aplicação: detalhado||
|Inícios de sessão de várias localizações geográficas|    Dashboard de aplicações||
|Inícios de sessão de endereços IP com atividade suspeita|   Erros de aprovisionamento de contas||
|Atividades irregulares de início de sessão|    Dispositivos de utilizadores individuais||
|Inícios de sessão de dispositivos possivelmente infetados|   Atividade de utilizadores individuais||
|Utilizadores com atividade anómala de início de sessão| Relatório de atividade de grupos||
||Relatório de atividade de registo de reposição de palavra-passe||
||Atividade de reposição de palavra-passe|||

Os dados destes relatórios podem ser úteis para as suas aplicações, como sistemas SIEM, auditoria e ferramentas de business intelligence. As APIs dos relatórios do Azure AD proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas [APIs](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) de vários idiomas e as ferramentas de programação.

Eventos de relatório de auditoria do Azure AD são retidos durante 180 dias.

> [!Note]
> Para obter mais informações sobre a retenção em relatórios, consulte [políticas de retenção de relatório do Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)

Para clientes interessados em armazenar os respetivos eventos de auditoria para períodos de retenção mais longos, a API de relatórios podem ser utilizado para solicitar a regularmente [eventos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) para um arquivo de dados separada.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Registos de máquina virtual utilizando o diagnóstico do Azure
[Diagnóstico do Azure](https://docs.microsoft.com/azure/azure-diagnostics) é a capacidade no Azure que permite a recolha de dados de diagnóstico sobre uma aplicação implementada. Pode utilizar a extensão de diagnóstico de várias origens diferentes. São atualmente suportados [Web de serviço de nuvem do Azure e funções de trabalho](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me),

![Registos de máquina virtual utilizando o diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

[Máquinas virtuais do Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) com o Microsoft Windows e [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

Pode ativar diagnóstico do Azure na máquina virtual a utilizar seguintes:

-   Com o Visual Studio, consulte [utilize o Visual Studio para rastreio Virtual Machines do Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [Configurar o diagnóstico do Azure numa máquina Virtual do Azure remotamente](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Utilize o PowerShell para configurar diagnósticos em Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Criar uma Máquina Virtual do Windows com a monitorização e diagnóstico com o modelo do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Análise de Armazenamento
[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) efetua o registo e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Registo de análise de armazenamento está disponível para o [serviços tabela, fila e Blob.](https://docs.microsoft.com/azure/storage/storage-introduction) Análise de armazenamento regista informações detalhadas sobre pedidos com êxito e a um serviço de armazenamento.

Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas com um serviço de armazenamento. Pedidos são registados numa base de melhor esforço. Entradas de registo são criadas apenas se não existirem pedidos efetuados relativamente ao ponto final de serviço. Por exemplo, se uma conta de armazenamento tem de atividade no respetivo ponto final do Blob, mas não no respetivos pontos finais de tabelas ou filas, apenas registos relativos ao serviço Blob é criado.

Para utilizar a análise de armazenamento, terá de a ativar individualmente para cada serviço que pretende monitorizar. Pode ativá-la no [portal do Azure](https://portal.azure.com/); para obter mais informações, consulte [monitorizar uma conta de armazenamento no portal do Azure.](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) Também pode ativar a análise de armazenamento através de programação através da API REST ou biblioteca de clientes. Utilize a operação definir propriedades de serviço para ativar a análise de armazenamento individualmente para cada serviço.

Os dados agregados são armazenados num blob bem conhecido (para o registo) e em tabelas bem conhecidas (para métricas), que podem ser acedidas através do serviço Blob e APIs de serviço tabela.

Análise de armazenamento tem um limite de 20 TB, a quantidade de dados armazenados, que é independentes do limite total para a sua conta de armazenamento. Todos os registos são armazenados no [blobs de blocos](https://docs.microsoft.com/azure/storage/storage-analytics) num contentor com o nome $logs, que são criados automaticamente quando a análise de armazenamento está ativada para uma conta de armazenamento.

> [!Note]
> Para obter mais informações sobre as políticas de retenção de dados e de faturação, consulte [faturação e de análise de armazenamento.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)
>
> [!Note]
> Para obter mais informações sobre limites de conta de armazenamento, consulte [metas de desempenho e escalabilidade do Storage do Azure.](https://docs.microsoft.com/azure/storage/storage-scalability-targets)

Os seguintes tipos de pedidos autenticados e anónimos são registados.



| Autenticado  | Anónimo|
| :------------- | :-------------|
| Pedidos com êxito | Pedidos com êxito |
|Falha de pedidos, incluindo o tempo limite, limitação, rede, autorização e outros erros | Pedidos utilizando um acesso assinatura partilhado (SAS), incluindo pedidos falhados e bem-sucedidas |
| Pedidos utilizando um acesso assinatura partilhado (SAS), incluindo pedidos falhados e bem-sucedidas |Erros de tempo limite para o cliente e servidor |
|   Pedidos de dados de análise |    Pedidos falhados de GET com o código de erro 304 (não modificados) |
| Pedidos efetuados pelo armazenamento Analytics ela própria, tal como criação de registo ou eliminação, não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [operações de registadas análise de armazenamento e as mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise do armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) tópicos. | Todos os outros pedidos anónimos falhados não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [operações de registadas análise de armazenamento e as mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise do armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Registos de rede do Azure
Rede de registo e monitorização no Azure é abrangente e abrange duas amplas categorias:

-   [Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) -monitorização de rede com base no cenário é fornecido com as funcionalidades do observador de rede. Este serviço inclui a captura de pacotes, o salto seguinte, o fluxo IP verificar, a vista de grupo de segurança, os registos de fluxo NSG. A monitorização ao nível do cenário fornece uma vista de ponto a ponto dos recursos de rede, ao contrário de monitorização de recursos de rede individuais.

-   [Monitorização de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) -monitorização de nível de recursos é composto por de quatro funcionalidades, os registos de diagnóstico, métricas, resolução de problemas e estado de funcionamento do recurso. Todas estas funcionalidades são criadas ao nível de recursos de rede.

![Registos de rede do Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Observador de rede é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário rede, para e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam-na compreender, diagnosticar e obter informações sobre a sua rede no Azure.

**O registo de fluxo de NSG** -registos de fluxo para grupos de segurança de rede permitem-lhe capturar registos relacionados com o tráfego que são permitidas ou negadas pelas regras de segurança no grupo. Estes registos de fluxo são escritos no formato JSON e Mostrar fluxos de saída e entrados numa base por regra, o NIC o fluxo de mensagens em fila aplica-se a informações de 5 cadeias de identificação sobre o fluxo (origem/destino IP, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

### <a name="network-security-group-flow-logging"></a>Registo de fluxo do grupo de segurança de rede

[Os registos de fluxo do grupo de segurança de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são uma funcionalidade do observador de rede que permite-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede. Estes registos de fluxo são escritos no formato JSON e Mostrar fluxos de saída e entrados numa base por regra, o NIC o fluxo de mensagens em fila aplica-se a informações de 5 cadeias de identificação sobre o fluxo (origem/destino IP, porta de origem/destino, protocolo), e se o tráfego foi permitido ou negado.

Enquanto o fluxo de registos de grupos de segurança de rede de destino, não são apresentados os mesmos como os outros registos. Registos de fluxo são armazenados apenas dentro de uma conta de armazenamento.

As políticas de retenção mesmo como visualizado outros registos em aplicam para registos de fluxo. Os registos de tem uma política de retenção que pode ser definida a partir do dia 1 a 365 dias. Se não definir uma política de retenção, os registos são mantidos para sempre.

**Registos de diagnóstico**

Eventos periódicos e spontaneous são criados pelos recursos de rede e registados em contas do storage, enviadas para um Hub de eventos ou análise de registos. Estes registos fornecem informações sobre o estado de funcionamento de um recurso. Estes registos podem ser visualizados no ferramentas como o Power BI e análise de registos. Para saber como ver os registos de diagnóstico, visite [análise de registos.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)

![Registos de diagnósticos](./media/azure-log-audit/azure-log-audit-fig5.png)

Os registos de diagnóstico estão disponíveis para [Balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), rotas, e [Gateway de aplicação.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

Observador de rede fornece que um diagnóstico os registos de vista. Esta vista contém todos os recursos de rede que suportam o registo de diagnóstico. Desta vista, pode ativar e desativar os recursos de rede rápida e convenientemente.


Para além das precedente capacidades de registo, o observador de rede tem atualmente as seguintes capacidades:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -fornece uma vista de nível de rede com as várias interconnections e as associações entre recursos de rede num grupo de recursos.

- [Captura de pacotes variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -captura de dados do pacote e para uma máquina virtual. Opções de filtragem avançadas e controlos otimizados, tais como a capacidade de definir a hora e limitações de tamanho fornecem versatility. Os dados de pacote podem ser armazenados num arquivo de blob ou no disco local no formato .cap.

-   [Fluxo IP verifica](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) -verifica se a um pacote é permitido ou negado com base nos parâmetros de pacotes de 5 cadeias de identificação do fluxo informações (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote é negado por um grupo de segurança, a regra e o grupo negado o pacote é devolvido.

-   [Próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) -determina o salto seguinte para pacotes a ser encaminhados na infraestrutura de rede do Azure, permitindo-lhe diagnosticar qualquer configurada incorretamente rotas definidas pelo utilizador.

-   [Vista do grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -obtém as regras de segurança eficaz e aplicados que são aplicadas numa VM.

-   [Gateway de rede virtual e a resolução de problemas de ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) -fornece a capacidade de resolução de problemas de Gateways da Virtual Network e ligações.

-   [Limites de subscrição de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) -permite-lhe ver utilização de recursos de rede contra os limites.

### <a name="application-insight"></a>Informações de aplicação

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gestão de desempenho de aplicações (APM) extensível para programadores de web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. É automaticamente detetar anomalias de desempenho. inclui ferramentas de análise poderosas para o ajudar a diagnosticar problemas e a compreender o que os utilizadores efetivamente fazem com a sua aplicação.

 Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

 Funciona para aplicações em diversas plataformas, incluindo .NET, Node.js e J2EE, alojadas no local ou na cloud. Integra-se com o processo de devOps e pontos de ligação para várias ferramentas de desenvolvimento.

![Informações de aplicação](./media/azure-log-audit/azure-log-audit-fig6.png)

O Application Insights destina-se à equipa de programação, para o ajudar a compreender o desempenho da aplicação e como está a ser utilizada. Monitoriza:

-   **Taxas de pedidos, tempos de resposta e taxas de falhas** - saiba quais são as páginas mais populares, em que alturas do dia e onde estão localizados os seus utilizadores. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as taxas de falhas aumentarem quando há mais pedidos, é possível que tenha um problema relativo a recursos.

-   **Taxas de dependência, tempos de resposta e taxas de falhas** - veja se os serviços externos o estão a atrasar.

-   **Exceções** - analisar as estatísticas agregadas, ou escolha instâncias específicas e explorar o rastreio da pilha e os pedidos relacionados. São reportadas exceções do servidor e do browser.

-   **Vistas de página e desempenho de carga** - reportados pelos browsers dos utilizadores.

-   **Chamadas AJAX** das páginas Web - taxas, tempos de resposta e taxas de falhas.

-   **Contagens de utilizadores e sessões**.

-   **Contadores de desempenho** dos computadores de servidor Windows ou Linux, como CPU, memória e utilização de rede.

-   **Diagnóstico do anfitrião** do Docker ou do Azure.

-   **Registos de rastreio de diagnóstico** da sua aplicação - para que possa correlacionar eventos de rastreio a pedidos.

-   **Eventos e métricas personalizadas** que escreve manualmente no código do cliente ou do servidor, para controlar eventos comerciais, como artigos vendidos ou jogos ganhos.

**Lista de cenários de integração e a descrição:**

| Cenários de integração | Descrição |
| --------------------- | :---------- |
|[Mapeamento de aplicações](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-app-map)|Os componentes da sua aplicação, com as principais métricas e alertas.||
|[Pesquisa de diagnóstico para a instância de dados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-diagnostic-search)| Procure e filtre eventos como pedidos, exceções, chamadas de dependências, rastreios de registo e visualizações de página.||
|[Explorador de métricas para os dados agregados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-metrics-explorer)|Explore, filtre e segmente dados agregados, como taxas de pedidos, falhas e exceções, tempos de resposta e tempos de carregamento de páginas.||
|[Dashboards](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-dashboards#dashboards)|Combine dados de vários recursos e partilhe-os com outras pessoas. Ideais para aplicações com vários componentes e para visualização contínua na sala de equipa.||
|[Transmissão de métricas em direto](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-live-stream)|Quando implementa uma compilação nova, veja estes indicadores de desempenho em tempo quase real, para ter a certeza de que está tudo a funcionar conforme esperado.||
|[Análise](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics)|Responda a perguntas difíceis sobre o desempenho e a utilização da sua aplicação através desta poderosa linguagem de consultas.||
|[Manuais e automáticas de alertas](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-alerts)|Os alertas automáticos adaptam-se aos padrões normais da sua aplicação relativamente a telemetria e são acionados se ocorrer algo fora do padrão habitual. Também pode definir alertas em níveis específicos de métricas personalizadas ou padrão.||
|[Visual Studio](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio)|Veja os dados de desempenho no código. Aceda ao código a partir dos rastreios de pilha.||
|[Power BI](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-power-bi)|Integre métricas de utilização com outras métricas de business intelligence.||
|[API REST](https://dev.applicationinsights.io/)|Escreva código para executar consultas nas métricas e nos dados não processados.||
|[Exportação contínua](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry)|Exportação em massa de dados não processados para o armazenamento quando este chega.||

### <a name="azure-security-center-alerts"></a>Alertas do Centro de segurança do Azure
[Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) automaticamente recolhe, analisa e integra-se dados de registo dos seus recursos do Azure, rede e soluções de parceiros ligadas, como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque.

A deteção de ameaças do Centro de Segurança funciona através da recolha automática de informações de segurança a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas. Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça.

![Centro de Segurança do Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Avanços em dados de grandes dimensões e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tecnologias são aplicadas para avaliar eventos em todos os recursos de infraestrutura de nuvem completo – detetar ameaças que seriam impossíveis de identificar através de abordagens manuais e previsão da evolução de ataques. Estas análises de segurança incluem:

-   **Ameaças integradas:** procura mal-intencionadas conhecidas aplicando ameaças globais de produtos da Microsoft e serviços, a unidade Microsoft Crimes digitais (DCU), o Microsoft Security Response Center (MSRC) e de feeds externos.

-   **A análise comportamental:** aplica padrões conhecidos para detetar um comportamento malicioso.

-   **Deteção de anomalias:** utiliza a criação de perfis estatística para criar uma linha de base histórica. Alerta sobre os desvios de linhas de base estabelecidos em conformidade com um potencial vetor de ataque.


Muitas operações de segurança e as equipas de resposta a incidentes dependem de uma solução Security Information and Event Management (SIEM) como ponto de partida para a triagem e investigar alertas de segurança. Com a integração de registos do Azure, os clientes podem sincronizar alertas do Centro de segurança e eventos de segurança de máquina virtual, recolhidos por diagnósticos do Azure e os registos de auditoria do Azure, com a sua análise de registos ou solução SIEM em tempo real.


## <a name="log-analytics"></a>Log Analytics

Análise de registos é um serviço no [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) que ajuda a recolher e analisar dados gerados pelos recursos na sua nuvem e no local ambientes. Fornece informações em tempo real, utilizando a pesquisa integrada e dashboards personalizados para analisar prontamente milhões de registos em todas as suas cargas de trabalho e servidores, independentemente da respetiva localização física.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

No centro do Log Analytics é o repositório do OMS, que está alojado na nuvem do Azure. Os dados são recolhidos no repositório a partir de origens ligadas mediante a configuração das origens de dados e da adição de soluções à sua subscrição. As origens de dados e as soluções criarão, cada uma, diferentes tipos de registos que têm os seus próprios conjuntos de propriedades, mas que podem ser analisados em conjunto em consultas feitas no repositório. Desta forma, pode utilizar as mesmas ferramentas e métodos para trabalhar com diversos tipos de dados recolhidos por diferentes origens.

As origens ligadas são os computadores e outros recursos que geram os dados recolhidos pelo Log Analytics. Isto pode incluir agentes instalados na [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) computadores que estabelecem ligação diretamente ou agentes em [um grupo de gestão do System Center Operations Manager ligado.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) Análise de registos também pode recolher dados a partir de [storage do Azure.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)

As [origens de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) são os diferentes tipos de dados recolhidos a partir de cada origem ligada. Isto inclui eventos e [dados de desempenho](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) de [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) e agentes Linux, para além das origens, tal como [registos de IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs), e [registos de texto personalizado.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs) Pode configurar cada origem de dados que pretende recolher e a configuração é entregue automaticamente a cada origem ligada.

Existem quatro formas diferentes de [recolher registos e as métricas para os serviços do Azure:](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)
1.  Diagnóstico do Azure diretamente ao Log Analytics (diagnóstico na tabela seguinte)

2.  Diagnóstico do Azure para armazenamento do Azure ao Log Analytics (armazenamento na tabela seguinte)

3.  Conectores para serviços do Azure (conectores na tabela seguinte)

4.  Scripts para recolher e, em seguida, colocar dados no Log Analytics (espaços em branco na seguinte tabela e a serviços não listados)

| Serviço | Tipo de Recurso | Registos | Métricas | Solução |
| :------ | :------------ | :--- | :------ | :------- |
|Gateways de aplicação|  Microsoft.Network/<br>applicationGateways|  Diagnóstico|Diagnóstico|    [Aplicação Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [análise de Gateway](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Informações de aplicação||     conector|  conector|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [conector (pré-visualização)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Contas de Automatização|   Microsoft.Automation/<br>AutomationAccounts|    Diagnóstico||       [Obter mais informações](https://docs.microsoft.com/en-us/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Contas do batch|    Microsoft.Batch/<br>batchAccounts|  Diagnóstico|    Diagnóstico||
|Serviços cloud clássico||       Armazenamento||       [Obter mais informações](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Serviços cognitivos|    Microsoft.CognitiveServices/<br>accounts|       Diagnóstico|||
|Análise do Data Lake|   Microsoft.DataLakeAnalytics/<br>accounts|   Diagnóstico|||
|Arquivo data Lake|   Microsoft.DataLakeStore/<br>accounts|   Diagnóstico|||
|Espaço de nomes de Hub de eventos|   Microsoft.EventHub/<br>Espaços de nomes|  Diagnóstico|    Diagnóstico||
|Hubs IoT|  Microsoft.Devices/<br>IotHubs||     Diagnóstico||
|Cofre de Chaves| Microsoft.KeyVault/<br>cofres|  Diagnóstico  || [Análise de KeyVault](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-key-vault)|
|Balanceadores de carga|    Microsoft.Network/<br>loadBalancers|    Diagnóstico|||
|Aplicações Lógicas|    Microsoft.Logic/<br>Fluxos de trabalho|  Diagnóstico|    Diagnóstico||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupos de Segurança de Rede|   Microsoft.Network/<br>networksecuritygroups|Diagnóstico||   [Análise de grupo de segurança de rede do Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Cofres de recuperação|   Microsoft.RecoveryServices/<br>cofres|||[O Azure Recovery Services análise (pré-visualização)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Procurar serviços|   Microsoft.Search/<br>searchServices|    Diagnóstico|    Diagnóstico||
|Espaço de nomes do Service Bus| Microsoft.ServiceBus/<br>Espaços de nomes|    Diagnóstico|Diagnóstico|    [Análise de barramento de serviço (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Armazenamento||    [Análise de recursos de infraestrutura de serviço (pré-visualização)](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servidores /<br>bases de dados||       Diagnóstico||
||Microsoft.Sql/<br>servidores /<br>elasticPools||||
|Armazenamento|||         Script| [Análise de armazenamento do Azure (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Virtual Machines|  Microsoft.Compute/<br>virtualMachines|  Extensão|  Extensão||
||||Diagnóstico||
|Conjuntos de dimensionamento de máquinas virtuais|   Microsoft.Compute/<br>virtualMachines    ||Diagnóstico||
||Microsoft.Compute/<br>virtualMachineScaleSets /<br>virtualMachines||||
|Web farms de servidores|Microsoft.Web/<br>serverfarms||   Diagnóstico
|Web Sites| Microsoft.Web/<br>sites ||      Diagnóstico|    [Obter mais informações](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites /<br>ranhuras|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de registo com sistemas SIEM no local
[Integração de registos do Azure](https://www.microsoft.com/download/details.aspx?id=53324) permite-lhe integrar registos não processados dos seus recursos do Azure no seu no local **sistemas Security Information and Event Management (SIEM)**.

![Integração de registo](./media/azure-log-audit/azure-log-audit-fig9.png)

Integração de registos do Azure recolhe diagnósticos do Azure, das suas máquinas virtuais do Windows (WAD), registos de atividade do Azure, alertas do Centro de segurança do Azure, e regista o fornecedor de recursos do Azure. Esta integração proporciona um dashboard unificado para todos os seus recursos, no local ou na nuvem, para que possam agregar, correlacionar, analisar e alerta para eventos de segurança.



Integração de registos do Azure suporta atualmente a integração de registos de atividade do Azure, registo de eventos do Windows da máquina virtual do Windows na sua subscrição do Azure, registos de auditoria de alertas do Centro de segurança do Azure, os registos de diagnóstico do Azure e Azure Active Directory.

| Tipo de registo | Análise de registos que suportam JSON (Splunk, ArcSight, Qradar) |
| :------- | :-------------------------------------------------------- |
|Os registos de auditoria do AAD|    sim|
|Registos de Atividade| Sim|
|Alertas ASC |Sim|
|Registos de diagnóstico (registos de recursos)|  Sim|
|Registos VM|   Sim através de eventos de Forwarded e não através de JSON|


A tabela seguinte explica a categoria de registo e os detalhes de integração do SIEM.

[Introdução à integração de registos do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) - Tutorial explica como instalação de integração de registos do Azure e integrar os registos do storage do Azure WAD, registos de atividade do Azure, alertas do Centro de segurança do Azure e Azure Active Directory registos de auditoria.

Cenários de integração

-   [Passos de configuração do parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – este blogue mostra-lhe como configurar a integração de registos do Azure para trabalhar com soluções de parceiros Splunk, HP ArcSight e IBM QRadar.

-   [Registos do Azure integração perguntas mais frequentes (FAQ) do sobre](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) -FAQ este respondem a dúvidas sobre a integração de registos do Azure.

-   [Integrar o Centro de segurança de alertas com o Azure registo integração](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) – este documento mostra como sincronizar alertas do Centro de segurança, juntamente com eventos de segurança de máquina virtual recolhidos por diagnósticos do Azure e os registos de auditoria do Azure, com a solução do SIEM ou de análise de registos.

## <a name="next-steps"></a>Passos Seguintes

- [Auditoria e registo](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Proteger os dados, mantendo em simultâneo visibilidade e rapidamente a responder a alertas de segurança atempada

- [Registo de segurança e a recolha de registos de auditoria no Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Quais as definições que necessita para impor a certificar-se de que as instâncias do Azure estão a recolher a segurança correta e os registos de auditoria.

- [Configurar definições de auditoria para uma coleção de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

Como um administrador de coleção do site, um pode obter o histórico de ações executadas por um utilizador específico e também pode obter o histórico de ações executadas durante um intervalo de datas específico. 

- [Procurar o registo de auditoria de segurança do Office 365 & Centro de conformidade](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Um pode utilizar a segurança do Office 365 e o Centro de conformidade para procurar o registo de auditoria unificada para ver a atividade de utilizador e administrador na organização do Office 365.



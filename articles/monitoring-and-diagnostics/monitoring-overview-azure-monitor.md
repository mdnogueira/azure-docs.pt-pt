---
title: "Descrição geral do Monitor do Azure | Microsoft Docs"
description: "Monitor do Azure recolhe estatísticas para utilização em alertas, webhooks, dimensionamento automático e automatização. Artigo listam também outras opções de monitorização da Microsoft."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: robb
ms.openlocfilehash: 5003ede9a40848db6905ef182da68577bca342cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-monitor"></a>Descrição geral do Monitor do Azure
Este artigo fornece uma descrição geral do serviço Azure Monitor no Microsoft Azure. -Descreve o Monitor do Azure e fornece indicações para informações adicionais sobre como utilizar o Monitor do Azure.  Se preferir um vídeo de introdução, consulte seguintes passos ligações na parte inferior deste artigo. 

## <a name="azure-monitor-and-microsofts-other-monitoring-products"></a>Monitor do Azure e a Microsoft's outros produtos da monitorização
Monitor do Azure fornece métricas de infraestrutura de nível de base e os registos para a maioria dos serviços de no Microsoft Azure. Serviços do Azure que não ainda colocar os seus dados no Monitor do Azure irão colocar-não existe no futuro.

Microsoft é fornecido adicionais produtos e serviços que fornecem capacidades de monitorização adicionais para os programadores, DevOps ou operações de TI que também têm as instalações no local. Para uma descrição geral e a compreensão sobre como estes produtos e serviços diferentes funcionam em conjunto, consulte [monitorização no Microsoft Azure](monitoring-overview.md).

## <a name="portal-overview-page"></a>Página de descrição geral do portal

Monitor do Azure tem uma página de destino que ajuda os utilizadores: 
- Compreenda as capacidades de monitorização oferecidas pelo Azure.
- Detetar, configurar e uma Azure plataforma e premium capacidades de monitorização.

Enquanto o serviço de monitorização do Azure é lançado, a página de descrição geral de destino está em pré-visualização. 

A página é um ponto de partida para navegação, incluindo Dependency. Demonstra organizados problemas relevantes dos serviços de diferentes e permite ao utilizador navegar para-os no contexto.
 
![Modelo de monitorização e diagnóstico para recursos de computação não](./media/monitoring-overview-azure-monitor/monitor-overview-ux2.png)

Quando abrir a página, pode selecionar entre as subscrições que tem acesso de leitura. Para uma subscrição selecionada, consulte:

- **Acionado alertas e origens de alerta** - esta tabela mostra as contagens de resumidas, origens de alerta, e quantas vezes alertas desencadeadas para a duração de tempo selecionado. Aplica-se a métrica e atividade alertas de registo.
- **Erros de registo de atividade** -se qualquer um dos seus recursos do Azure registar eventos com uma gravidade de nível de erro, pode ver uma contagem de alto nível e clicar sucessivamente para a página de registo de atividade para investigar cada evento.
- **Estado de funcionamento de serviço do Azure** -pode ver uma contagem de problemas do serviço de estado de funcionamento do serviço, eventos de manutenção planeada e advisories de estado de funcionamento. Estado de funcionamento de serviço do Azure fornece informação personalizada quando problemas na infraestrutura do Azure afetam os serviços.  Consulte [estado de funcionamento de serviço de Azure](../service-health/service-health-overview.md) para obter mais informações.  
- **Application Insights** -Consulte KPIs para cada recurso AppInsights na subscrição atual. Os KPIs estão otimizados para monitorização, entre as aplicações web do ASP.NET, Java, nó e geral, tipos de aplicações da aplicação do lado do servidor. Os KPIs incluem as métricas de taxa de pedidos, duração de resposta, taxa de falhas e % de disponibilidade. 

Se a ter não integrada para análise de registos ou Application Insights, ou se não tiver configurado os alertas do Azure na subscrição atual, a página fornece ligações para iniciar o processo de Dependency.



## <a name="azure-monitor-sources---compute-subset"></a>Origens de Monitor do Azure - subconjunto de computação

![Modelo de monitorização e diagnóstico para recursos de computação não](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-compute_v6.png)

Os serviços de computação aqui incluem 
- Serviços Cloud 
- Virtual Machines 
- Conjuntos de dimensionamento de máquina virtual 
- Service Fabric

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicações - registos de diagnóstico, os registos de aplicações e métricas
As aplicações podem ser executadas sobre o SO convidado no modelo de computação. Se emitir o seu próprio conjunto de registos e as métricas. Monitor do Azure baseia-se a extensão de diagnóstico do Azure (Windows ou Linux) para recolher a maioria dos registos e as métricas de nível de aplicação. Os tipos incluir

* Contadores de desempenho
* Registos de aplicações
* Registos de eventos do Windows
* Origem de evento de .NET
* Registos de IIS
* O manifesto com base em ETW
* Informações de falhas
* Registos de erros do cliente

Sem a extensão de diagnóstico, estão disponíveis apenas alguns métricas, como a utilização da CPU. 

### <a name="host-and-guest-vm-metrics"></a>Métricas de anfitrião e convidado de VM
Os recursos de computação listadas anteriormente tem uma VM de anfitrião dedicada e interagem com SO convidado. O anfitrião VM e o SO convidado são equivalentes de VM de raiz e de convidado VM no modelo de Hipervisor de Hyper-V. Pode recolher a métrica em ambos. Também pode recolher registos de diagnóstico sobre o SO convidado.   

### <a name="activity-log"></a>Registo de Atividades
Pode pesquisar o registo de atividade (anteriormente denominada registos de auditoria ou operacional) para obter informações sobre o seu recurso visto pela infraestrutura do Azure. O registo contém informações como vezes quando os recursos são criados ou destruídos.  Para obter mais informações, consulte [descrição geral do registo de atividade](monitoring-overview-activity-logs.md). 

## <a name="azure-monitor-sources---everything-else"></a>Origens de Monitor do Azure - tudo o resto

![Modelo de monitorização e diagnóstico para recursos de computação](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-non-compute_v6.png)


### <a name="resource---metrics-and-diagnostics-logs"></a>Recursos - métricas e registos de diagnóstico
Registos de métricas e diagnóstico coletável variam com base no tipo de recurso. Por exemplo, aplicações Web fornece estatísticas sobre a percentagem de CPU e/s de disco. Não existem esses métricas para uma fila do Service Bus, em vez disso, fornece métricas, como o débito de tamanho e a mensagem da fila. Uma lista de coletável métricas para cada recurso está disponível em [suportado métricas](monitoring-supported-metrics.md). 

### <a name="host-and-guest-vm-metrics"></a>Métricas de anfitrião e convidado de VM
Não há necessariamente um mapeamento de 1:1 entre os recursos e um anfitrião específico ou VM do convidado para a métrica não está disponível.

### <a name="activity-log"></a>Registo de Atividades
O registo de atividade é igual aos recursos de computação.  

## <a name="uses-for-monitoring-data"></a>Utilizações de dados de monitorização
Depois de recolher os dados, pode fazer o seguinte com o mesmo no Monitor do Azure.

### <a name="route"></a>Encaminhar
Pode transmitir dados de monitorização para outras localizações. 

Os exemplos incluem:

- Envie para o Application Insights para poder utilizar o respetivas ferramentas mais rica de visualização e análise.
- Envie para os Event Hubs, para que pode encaminhar para as ferramentas de terceiros. 

### <a name="store-and-archive"></a>Arquivo e de arquivo
Alguns dados de monitorização já estão armazenada e está disponível no Monitor do Azure para um conjunto de período de tempo. 
- As métricas são armazenadas durante 30 dias. 
- Entradas de registo de atividade são armazenadas durante 90 dias. 
- Registos de diagnóstico não são armazenados em todas as. 

Se pretender armazenar dados de mais do que os períodos de tempo listados acima, pode utilizar um armazenamento do Azure. Dados de monitorização é mantido na sua conta de armazenamento com base na política de retenção que definir. É necessário pagar para o espaço de que dados demora a cópia de segurança no armazenamento do Azure. 

Algumas formas de utilizar estes dados:

- Depois de escrito, pode ter outras ferramentas dentro ou fora do Azure lê-lo e processá-la.
- Transferir os dados localmente para um arquivo local ou alterar a política de retenção na nuvem para manter os dados para períodos de tempo prolongados.  
- Deixe os dados no armazenamento do Azure indefinidamente para efeitos de arquivo. 

### <a name="query"></a>Consulta
Pode utilizar a API de REST de Monitor do Azure, entre comandos de Interface de linha de comandos (CLI) de plataforma, os cmdlets do PowerShell ou o SDK .NET para aceder aos dados no sistema ou armazenamento do Azure

Os exemplos incluem:

* Obter dados para uma aplicação de monitorização personalizada que ter escrito
* Criar consultas personalizadas e enviar esses dados para uma aplicação de terceiros.

### <a name="visualize"></a>Visualizar
Visualizar os dados nos gráficos e gráficos monitorização ajuda-o a localizar tendências mais rápida do que à procura de dados propriamente ditos.  

Alguns métodos de visualização incluem:

* Utilizar o portal do Azure
* Rota de dados para o Azure Application Insights
* Rota de dados para o Microsoft PowerBI
* Encaminhar os dados para uma ferramenta de visualização de terceiros utilizando a transmissão em direto ou fazendo com que a ferramenta de ler a partir do arquivo no armazenamento do Azure


### <a name="automate"></a>Automatizar
Pode utilizar dados de monitorização para os alertas de Acionador ou processos mesmo todo. Os exemplos incluem:

* Utilize dados para instâncias de computação de dimensionamento automático ou reduzir verticalmente com base na carga de aplicação.
* Envie mensagens de correio eletrónico quando uma métrica atravesse um limiar predeterminado.
* Chamar um URL de web (webhook) para executar uma ação num sistema fora do Azure
* Iniciar um runbook na automatização do Azure para efetuar quaisquer variedade de tarefas

## <a name="methods-of-accessing-azure-monitor"></a>Métodos para aceder ao Monitor do Azure
Em geral, pode manipular os dados, encaminhamento e o controlo de obtenção através de um dos seguintes métodos. Nem todos os métodos estão disponíveis para todas as ações ou tipos de dados.

* [Portal do Azure](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [Interface de linha de comandos de várias plataformas (CLI)](insights-cli-samples.md)
* [API REST](https://docs.microsoft.com/rest/api/monitor/)
* [SDK do .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre
- Um vídeo com instruções de Monitor de Azure apenas está disponível em  
[Introdução ao Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor). 
- Um vídeo explicar um cenário em que pode utilizar o Monitor do Azure está disponível em [explorar o Microsoft Azure de monitorização e diagnóstico](https://channel9.msdn.com/events/Ignite/2016/BRK2234) e [Monitor Azure um vídeo a partir de Ignite 2016](https://myignite.microsoft.com/videos/4977).
- Executar através da interface de Monitor do Azure no [introdução com a monitorização do Azure](monitoring-get-started.md)
- Configurar o [extensões de diagnóstico do Azure](../azure-diagnostics.md) se está a tentar diagnosticar problemas no seu serviço de nuvem, a Máquina Virtual, a Máquina Virtual Dimensionar conjuntos ou a aplicação de Service Fabric.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se estiver a tentar diagnóstico problemas na sua aplicação de serviço Web da aplicação.
- [Resolução de problemas de armazenamento do Azure](../storage/common/storage-e2e-troubleshooting.md) ao utilizar o armazenamento de Blobs, tabelas ou filas
- [Análise de registo](https://azure.microsoft.com/documentation/services/log-analytics/) e [Operations Management Suite](https://www.microsoft.com/oms/)

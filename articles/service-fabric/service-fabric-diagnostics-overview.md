---
title: "Monitorização de infraestrutura de serviço do Azure e a descrição geral de diagnóstico | Microsoft Docs"
description: "Saiba mais sobre a monitorização e diagnóstico para clusters, aplicações e serviços do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorização e diagnóstico para o Azure Service Fabric

Monitorização e diagnóstico é cruciais para desenvolver, testar e implementar aplicações e serviços em qualquer ambiente. Soluções de Service Fabric funcionam melhor quando planear e implementar a monitorização e certifique-se de diagnóstico que ajudam a aplicações e serviços estão a funcionar como esperado no ambiente de desenvolvimento local ou na produção.

Os objetivos principais da monitorização e diagnóstico são:
* Detetar e diagnosticar problemas de hardware e de infraestrutura
* Detetar problemas de aplicações e software, reduza o período de indisponibilidade do serviço
* Compreender o recurso consumo e ajuda unidade operações decisões
* Otimizar o desempenho de aplicações, serviço e infraestrutura
* Gerar informações empresariais e identifique as áreas de melhoramento da


O fluxo de trabalho geral de monitorização e diagnóstico consiste em três passos:

1. **Geração de eventos**: Isto inclui a eventos (registos de rastreios, eventos personalizados) de infraestrutura (cluster), a plataforma e o nível de serviço / aplicação
2. **Agregação de evento**: eventos gerados tem de ser recolhidos e agregados antes que podem ser apresentados
3. **Análise**: eventos tem de ser visualizadas e acessível algumas formato, para permitir a análise e apresentar conforme necessário

São vários produtos disponíveis que abrangem estes três áreas e estiver livre para escolher as diferentes tecnologias para cada. É importante certificar-se de que as peças vários funcionam em conjunto para fornecer uma solução de monitorização ponto a ponto para o cluster.

## <a name="event-generation"></a>Geração de eventos

O primeiro passo no fluxo de trabalho de monitorização e diagnóstico é a criação e a geração de eventos e registos. Estes eventos, os registos e os rastreios são gerados durante dois níveis: a camada de plataforma (incluindo o cluster, computadores ou ações de Service Fabric) ou a aplicação (qualquer instrumentação adicionado às aplicações e serviços implementados em cluster). Eventos em cada um destes níveis são personalizáveis, apesar de Service Fabric fornece algumas instrumentação por predefinição.

Leia mais sobre [eventos ao nível da plataforma](service-fabric-diagnostics-event-generation-infra.md) e [eventos ao nível da aplicação](service-fabric-diagnostics-event-generation-app.md) para compreender o que é fornecido e como adicionar mais instrumentação.

Depois de tomar uma decisão no fornecedor de registo que pretende utilizar, tem de certificar-se de que os registos estão a ser agregada e armazenados corretamente.

## <a name="event-aggregation"></a>Agregação de eventos

Para recolher os registos e eventos gerados pelas suas aplicações e o cluster, normalmente, recomendamos que utilize [diagnósticos do Azure](service-fabric-diagnostics-event-aggregation-wad.md) (mais semelhantes a recolha de registos com base no agente) ou [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (-processo de recolha de registos).

Recolher registos de aplicação utilizando a extensão de diagnóstico do Azure é uma boa opção para serviços do Service Fabric se o conjunto de origens de registo e destinos não são alterados frequentemente e não existe um mapeamento entre as origens e os respetivos destinos simples. O motivo para isto está a configurar o Azure Diagnostics acontece na camada do Gestor de recursos, para que efetuar alterações significativas a configuração requer a atualização/implementar novamente o cluster. Além disso, este é melhor utilizada de certificar-se de que os registos estão a ser armazenada em qualquer local, um pouco mais permanente, a partir de onde podem ser acedidos por várias plataformas de análise. Isto significa que mesmo termina a ser menos eficientes de um pipeline que vai com uma opção como EventFlow.

Utilizar [EventFlow](https://github.com/Azure/diagnostics-eventflow) permite-lhe ter serviços enviar registos diretamente a uma plataforma de análise e visualização de e/ou ao armazenamento. Outras bibliotecas (ILogger Serilog, etc.) podem ser utilizadas para a mesma finalidade, mas EventFlow tem a vantagem de ter sido concebidas especificamente para recolha de registos no processo e para suportar os serviços do Service Fabric. Isto tende a ter várias vantagens potenciais:

* Fácil configuração e implementação
    * A configuração da recolha de dados de diagnóstico é apenas parte da configuração do serviço. É fácil sempre manter "sincronizadas" com o resto da aplicação
    * Por aplicação ou serviço a configuração é facilmente alcançável
    * Configurar destinos de dados através de EventFlow é apenas um fim de adicionar o pacote NuGet adequado e alterar o *eventFlowConfig.json* ficheiro
* Flexibilidade
    * A aplicação pode enviar os dados sempre que necessita começar, desde que não há uma biblioteca de cliente que suporta o sistema de armazenamento de dados de destino. Novo destinos podem ser adicionados como pretendido
    * Regras de captura, filtragem e agregação de dados complexas podem ser implementadas
* Acesso a dados de aplicações internos e contexto
    * O subsistema de diagnóstico em execução dentro do processo de aplicações/serviços facilmente pode aumentar os rastreios com informações contextuais

É um aspeto a ter em atenção que estas duas opções não são mutuamente exclusivas e embora seja possível obter uma tarefa semelhante feita com a utilização de um ou outro, também foi fazer sentido para configurar ambos. Na maioria das situações, a combinação de um agente no processo de recolha pode levar para um fluxo de trabalho de monitorização mais fiável. A extensão de diagnóstico do Azure (agente) pode ser o caminho para os registos de nível de plataforma enquanto EventFlow (no processo de recolha) pode utilizar para os registos de nível de aplicação. Depois de ter figured saída que funciona melhor para si, está na altura de pensar sobre como pretende que os seus dados para ser apresentada e analisados.

## <a name="event-analysis"></a>Análise de eventos

Existem várias plataformas excelente que existem no mercado quando se trata de análise e visualização de dados de monitorização e diagnóstico. Os dois que recomendamos são [OMS](service-fabric-diagnostics-event-analysis-oms.md) e [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) devido ao respetivo melhor integração com o Service Fabric, mas deve também ter um aspeto para o [pilha elástico](https://www.elastic.co/products) (especialmente se estiver a considerar a executar um cluster num ambiente offline), [Splunk](https://www.splunk.com/), ou qualquer outra plataforma da sua preferência.

Os pontos de chaves para qualquer plataforma que escolher devem incluir como confortável são com a interface de utilizador e as opções de consultas, a capacidade de visualizar dados e criar dashboards facilmente legíveis e as ferramentas adicionais fornecem para melhorar a monitorização, tais como automatizada alertas.

Para além da plataforma que escolher, quando configurar um cluster do Service Fabric como um recurso do Azure, também ter acesso a do Azure out of box monitorização para máquinas, que podem ser útil para desempenho específicos e a monitorização de métrica.

### <a name="azure-monitor"></a>Azure Monitor

Pode utilizar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) monitorizar muitos dos recursos do Azure no qual baseia-se um cluster do Service Fabric. Um conjunto de métricas para o [conjunto de dimensionamento da máquina virtual](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) e individuais [máquinas virtuais](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) são recolhidos automaticamente e apresentado no portal do Azure. Para ver as informações recolhidas, no portal do Azure, selecione o grupo de recursos que contém o cluster do Service Fabric. Em seguida, selecione o conjunto de dimensionamento de máquina virtual que pretende visualizar. No **monitorização** secção, selecione **métricas** para ver um gráfico dos valores.

![Vista do portal do Azure das informações recolhidas métricas](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Para personalizar os gráficos, siga as instruções em [métricas no Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Também pode criar alertas com base nestas métricas, conforme descrito em [criar alertas no Monitor do Azure para serviços do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Pode enviar alertas para um serviço de notificação utilizando web hooks, conforme descrito em [configurar um web hook num alerta métrico Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Monitor do Azure suporta apenas uma subscrição. Se precisar de monitorizar várias subscrições, ou se precisar de funcionalidades adicionais, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte do Microsoft Operations Management Suite, fornece uma solução de gestão de IT holística para no local e a infraestrutura baseada na nuvem. Pode encaminhar dados a partir do Azure Monitor diretamente ao Log Analytics, para que possa ver as métricas e registos para todo o seu ambiente num único local.

## <a name="next-steps"></a>Passos seguintes

### <a name="watchdogs"></a>Watchdogs

Um watchdog do é um serviço separado que pode ver o estado de funcionamento e carregar em serviços e o estado de funcionamento do relatório para qualquer coisa na hierarquia do modelo de estado de funcionamento. Isto pode ajudar a evitar erros não seriam possível detetar com base na vista de um único serviço. Watchdogs também são um bom local para o código de anfitrião que executa toma ações sem interação do utilizador (por exemplo, da limpeza dos ficheiros de registo no armazenamento em determinados intervalos de tempo). Pode encontrar uma implementação de serviço do exemplo watchdog [aqui](https://github.com/Azure-Samples/service-fabric-watchdog-service).

Começar a compreender como registos de eventos e obterem gerados durante o [nível de plataforma](service-fabric-diagnostics-event-generation-infra.md) e o [nível de aplicação](service-fabric-diagnostics-event-generation-app.md).
---
title: "Plataforma de recursos de infraestrutura de serviço do Azure de nível de monitorização | Microsoft Docs"
description: "Saiba mais sobre eventos de nível de plataforma e registos utilizados para monitorizar e diagnosticar os clusters de Service Fabric do Azure."
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
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: c5857515ae8357b003f0999c4b11bd666c32bbf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="platform-level-event-and-log-generation"></a>Geração de registo e evento nível no plataforma

## <a name="monitoring-the-cluster"></a>O cluster de monitorização

É importante monitorizar a nível de plataforma para determinar se é ou não o hardware e o cluster se comportam conforme esperado. Apesar de Service Fabric pode manter as aplicações em execução durante uma falha de hardware, mas terá de diagnosticar se o erro está a ocorrer numa aplicação ou na infraestrutura subjacente. Também deverá monitorizar clusters para melhor planear a capacidade, ajudar a tomar decisões sobre a adição ou remoção de hardware.

O Service Fabric fornece cinco registo diferentes canais out-of-a-box que geram os seguintes eventos:

* Canal operacional: operações de alto nível efetuadas pelo serviço de recursos de infraestrutura e o cluster, incluindo eventos de um nó de futuras cópias de segurança, uma nova aplicação que está a ser implementada, ou um SF atualizar reversão, etc.
* Canal operacional - detalhada: relatórios de estado de funcionamento e decisões de balanceamento de carga
* Canal de mensagens & dados: críticos registos e eventos gerados no nosso serviço de mensagens (atualmente apenas o ReverseProxy) e o caminho de dados (modelos de reliable services)
* Canal de mensagens & dados - detalhada: verboso canal que contém todos os registos não críticos de dados e mensagens do cluster (este canal tem um volume muito elevado de eventos)   
* [Eventos de serviços fiáveis](service-fabric-reliable-services-diagnostics.md): eventos específicos do modelo de programação
* [Eventos de Atores fiáveis](service-fabric-reliable-actors-diagnostics.md): programação eventos específicos do modelo e os contadores de desempenho
* Suporta registos: gerados pelo serviço de recursos de infraestrutura apenas ser utilizado por ao fornecer suporte de registos de sistema

Estes canais vários abrangem a maioria do registo de nível de plataforma que é recomendada. Para melhorar o registo ao nível da plataforma, considere o investimento em melhor compreender o modelo de estado de funcionamento e a adicionar relatórios de estado de funcionamento personalizado e adicionar personalizado **contadores de desempenho** para criar uma compreensão em tempo real sobre o impacto da sua os serviços e aplicações no cluster.

### <a name="azure-service-fabric-health-and-load-reporting"></a>Estado de funcionamento de Service Fabric do Azure e relatórios de carga

Service Fabric tem o seu próprio modelo de estado de funcionamento, que é descrito detalhadamente nestes artigos:
- [Introdução à monitorização de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
- [Comunicar e verificar o estado de funcionamento dos serviços](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Adicionar relatórios de estado de funcionamento personalizados do Service Fabric](service-fabric-report-health.md)
- [Ver relatórios de estado de funcionamento do Service Fabric](service-fabric-view-entities-aggregated-health.md)

Monitorização de estado de funcionamento é essencial para vários aspetos de operação de um serviço. Monitorização de estado de funcionamento é especialmente importante quando o Service Fabric efetua uma atualização da aplicação com nome. Depois de cada domínio de atualização do serviço está atualizado e está disponível para os seus clientes, o domínio de atualização tem de passar verificações de estado de funcionamento antes da implementação de passa para o domínio de atualização seguinte. Se não é possível atingir o estado de bom estado de funcionamento, a implementação é revertida, para que a aplicação está num estado conhecido, bom. Embora alguns clientes poderão ser afetados antes dos serviços são revertidos, maioria dos clientes não irá ocorrer um problema. Além disso, uma resolução ocorre relativamente rapidamente e sem ter de aguardar a ação a partir de um operador humano. As verificações de estado de funcionamento mais que são incorporadas no seu código, o mais resiliente do que seu serviço é a problemas de implementação.

Outro aspeto do Estado de funcionamento do serviço está a comunicar as métricas do serviço. As métricas são importantes no Service Fabric porque são utilizados para equilibrar a utilização de recursos. Métricas também podem ser um indicador de estado de funcionamento do sistema. Por exemplo, poderá ter uma aplicação que tenha vários serviços, cada instância de relatórios e um pedidos por segundo (RPS) métrica. Se um serviço estiver a utilizar recursos mais do que outro serviço, o Service Fabric move instâncias de serviço em todo o cluster, para tentar manter a utilização de recursos do mesmo. Para obter uma explicação mais detalhada como funciona a utilização de recursos, consulte [gerir consumo de recursos e carregar no Service Fabric com métricas](service-fabric-cluster-resource-manager-metrics.md).

Métricas também podem ajudar a dar-lhe informações sobre como o serviço está a efetuar. Ao longo do tempo, pode utilizar as métricas para verificar que o serviço está a funcionar dentro de parâmetros esperados. Por exemplo, se as tendências mostram que em 09: 00 na segunda-feira de manhã a média RPS 1.000, em seguida, poderá configurou um relatório de estado de funcionamento que o alerta se o RPS for inferior a 500 ou superior 1,500. Tudo poderá ser perfeitamente adequado, mas poderá ser vale um aspeto de se certificar de que os seus clientes estão a ter uma excelente experiência. O serviço pode definir um conjunto de métricas que podem ser comunicadas para fins de verificação do Estado de funcionamento, mas que não afetam o balanceamento de recurso do cluster. Para tal, defina a ponderação da métrica para zero. Recomendamos que todas as métricas de começar com uma ponderação igual a zero e não aumente a ponderação até que tem a certeza de que compreende como weighting as métricas afeta para o cluster de balanceamento de recurso.

> [!TIP]
> Não utilize métricas ponderadas muitas. Pode ser difícil compreender por que razão as instâncias de serviço estão a ser movidas em torno para balanceamento. Métricas alguns podem aceder uma forma muito!

Todas as informações que podem indicar o estado de funcionamento e desempenho da aplicação são uma candidata para relatórios de estado de funcionamento e as métricas. Um contador de desempenho da CPU pode indicar-lhe como o nó for utilizado, mas não indica se um determinado serviço está em bom estado, porque poderão estar em execução vários serviços num único nó. Mas, métricas como RPS, itens processadas, e latência de pedidos de todos os pode indicar o estado de funcionamento de um serviço específico.

Para comunicar o estado de funcionamento, utilize o código semelhante a isto:

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Para comunicar uma métrica, utilize código semelhante a isto:

  ```csharp
    this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

### <a name="service-fabric-support-logs"></a>Registos de suporte do Service Fabric

Se precisar de contactar o suporte da Microsoft para obter ajuda com o cluster do Service Fabric do Azure, os registos de suporte quase sempre são necessários. Se o cluster está alojado no Azure, os registos de suporte são automaticamente configurados e recolhidos como parte da criação de um cluster. Os registos são armazenados numa conta de armazenamento dedicado no grupo de recursos do cluster. A conta de armazenamento não tem um nome fixo, mas a conta, é apresentado o contentores de BLOBs e tabelas com nomes que começam com *recursos de infraestrutura*. Para obter informações sobre como configurar coleções de registo para um cluster autónomo, consulte [criar e gerir um cluster do Azure Service Fabric autónomo](service-fabric-cluster-creation-for-windows-server.md) e [definições de configuração para uma autónoma Windows cluster](service-fabric-cluster-manifest.md). Para instâncias de Service Fabric autónomas, os registos devem ser enviados para uma partilha de ficheiros local. É **necessário** ter estes registos para suporte, mas não se destinam a ser utilizada por todas as pessoas fora da equipa de suporte ao cliente da Microsoft.

## <a name="enabling-diagnostics-for-a-cluster"></a>Ativar o diagnóstico para um cluster

Para tirar partido destes registos, recomenda-se vivamente que durante a criação do cluster, "diagnóstico" está ativado. Ao ativar o diagnóstico, quando o cluster é implementado, Windows Azure Diagnostics é capaz de reconhecer o operacional, Reliable Services e canais Reliable actors e armazenar os dados como mais explained [agregar eventos com o Azure Diagnóstico](service-fabric-diagnostics-event-aggregation-wad.md).

Como mostrado acima, há também um campo opcional para adicionar uma chave de instrumentação do Application Insights (AI). Se optar por utilizar AI para quaisquer análises de eventos (Leia mais informações sobre este assunto na [análise de eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)), incluir aqui instrumentationKey de recurso AppInsights (GUID).


Se pretender implementar contentores para o cluster, ative WAD recolher estatísticas de docker adicionando este à sua "WadCfg > DiagnosticMonitorConfiguration":

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},

```

## <a name="measuring-performance"></a>Medir o desempenho

Medida de desempenho do seu cluster irá ajudá-lo a compreender a forma como é capaz de lidar com as decisões de carga e a unidade em torno do seu cluster de dimensionamento (ver mais informações sobre dimensionamento de um cluster [no Azure](service-fabric-cluster-scale-up-down.md), ou [no local](service-fabric-cluster-windows-server-add-remove-nodes.md)). Dados de desempenho também são útil quando comparado com as ações a ou as suas aplicações e serviços podem ter demorado, ao analisar os registos no futuro. 

Para obter uma lista de contadores de desempenho para recolher ao utilizar o Service Fabric, consulte [contadores de desempenho no Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Seguem-se duas formas comuns na qual pode configurar a recolha de dados de desempenho para o cluster:

* Através de um agente: Esta é a forma de recolha de desempenho de uma máquina, uma vez que os agentes têm, normalmente, uma lista de possíveis métricas de desempenho que podem ser recolhidas preferencial e é um processo é relativamente fácil para escolher as métricas que pretende recolher ou alterá-los. Leia sobre [como configurar o OMS de Service Fabric](service-fabric-diagnostics-event-analysis-oms.md) e [como configurar o agente do OMS Windows](../log-analytics/log-analytics-windows-agents.md) artigos para saber mais sobre o agente do OMS, que é um desse agente de monitorização que consegue recolher desempenho dados para as VMs do cluster e contentores implementados.

* Configurar diagnósticos para escrever os contadores de desempenho na tabela: para os clusters no Azure, isto significa que a alteração da configuração de diagnósticos do Azure para recolher os contadores de desempenho adequadas de VMs do cluster e, permitindo-lhe recolher estatísticas de docker se for implementar quaisquer contentores. Leia sobre como configurar [os contadores de desempenho WAD](service-fabric-diagnostics-event-aggregation-wad.md) no Service Fabric para configurar a recolha do contador de desempenho.

## <a name="next-steps"></a>Passos seguintes

Os registos e eventos tem de ser agregada antes que podem ser enviadas para qualquer plataforma de análise. Leia sobre [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [WAD](service-fabric-diagnostics-event-aggregation-wad.md) para melhor compreender algumas das opções recomendadas.

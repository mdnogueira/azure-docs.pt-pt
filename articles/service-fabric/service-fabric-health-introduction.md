---
title: "Monitorização de estado de funcionamento no Service Fabric | Microsoft Docs"
description: "Uma introdução para o estado de funcionamento do Azure Service Fabric monitorização modelo, que fornece a monitorização do cluster e respetivas aplicações e serviços."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 1d979210-b1eb-4022-be24-799fd9d8e003
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: oanapl
ms.openlocfilehash: 330ef58d89ebabaa2af7fa8e98e693ddd64dcc4e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Introdução à monitorização do estado de funcionamento do Service Fabric
Azure Service Fabric introduz um modelo de estado de funcionamento que fornece a avaliação de estado de funcionamento extensíveis, avançado e flexível e de relatórios. O modelo permite quase em tempo real de monitorização do Estado do cluster e os serviços em execução no mesmo. Além disso, pode facilmente obter informações de estado de funcionamento e corrigir problemas de potencial antes de serem cascade e causam falhas em grande escala. No modelo de típico, os serviços de enviar relatórios com base na respetiva vistas locais e que informações são agregadas para fornecer um geral do cluster ao nível do Vista.

Componentes do Service Fabric utilizam este modelo de estado de funcionamento avançado para comunicar o respetivo estado atual. Pode utilizar o mesmo mecanismo para Estado de funcionamento do relatório das suas aplicações. Se investir no relatório de estado de funcionamento de alta qualidade que capture as condições personalizadas, pode detetar e corrigir problemas para a sua aplicação em execução muito mais facilmente.

O vídeo seguinte do Microsoft Virtual Academy também descreve o modelo de estado de funcionamento de recursos de infraestrutura de serviço e como são utilizadas:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-health-introduction/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

> [!NOTE]
> Iniciamos o subsistema de estado de funcionamento para resolver uma necessidade para atualizações monitorizadas. O Service Fabric fornece atualizações de aplicações e do cluster monitorizadas que garantir a disponibilidade completa, sem períodos de indisponibilidade e mínimo para sem intervenção do utilizador. Para alcançar estes objetivos, a atualização, verifica o estado de funcionamento com base nas políticas de atualização configuradas. Uma atualização possa prosseguir apenas quando o estado de funcionamento respeita os limiares pretendidos. Caso contrário, a atualização é automaticamente revertida ou colocada em pausa para dar aos administradores uma oportunidade para corrigir os problemas. Para saber mais sobre as atualizações de aplicações, consulte o artigo [neste artigo](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Arquivo de estado de funcionamento
O arquivo de estado de funcionamento mantém informações relacionadas com o estado de funcionamento sobre entidades do cluster para fácil obtenção e avaliação. Está implementado como um recurso de infraestrutura de serviço persistente serviço com monitorização de estado para garantir a elevada disponibilidade e escalabilidade. O arquivo de estado de funcionamento faz parte o **fabric: / sistema** aplicação e está disponível quando o cluster está operacional e em execução.

## <a name="health-entities-and-hierarchy"></a>Entidades de estado de funcionamento e a hierarquia
As entidades de estado de funcionamento são organizadas de uma hierarquia lógica que capture as interações e as dependências entre entidades diferentes. O arquivo de estado de funcionamento baseia-se automaticamente entidades de estado de funcionamento e a hierarquia baseadas em relatórios recebidos a partir de componentes do Service Fabric.

As entidades de estado de funcionamento espelhar as entidades de Service Fabric. (Por exemplo, **entidade de aplicação de estado de funcionamento** corresponde a uma instância de aplicação implementado num cluster, enquanto **entidade de nó de estado de funcionamento** corresponde a um nó de cluster do Service Fabric.) A hierarquia de estado de funcionamento captura as interações das entidades de sistema e é a base para a avaliação de estado de funcionamento avançadas. Pode saber sobre os conceitos fundamentais do Service Fabric do [descrição geral técnica do Service Fabric](service-fabric-technical-overview.md). Para mais informações sobre a aplicação, consulte [modelo de aplicação de Service Fabric](service-fabric-application-model.md).

Hierarquia de entidades de estado de funcionamento e permitir que o cluster e aplicações para ser efetivamente comunicadas, debugged e monitorizados. O modelo de estado de funcionamento fornece um exata *granulares* representação do Estado de funcionamento das peças mover muitos no cluster.

![Entidades de estado de funcionamento.][1]
As entidades de estado de funcionamento, organizados de uma hierarquia com base em relações principal-subordinado.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

As entidades de estado de funcionamento são:

* **Cluster**. Representa o estado de funcionamento de um cluster do Service Fabric. Relatórios de estado de funcionamento do cluster descrevem as condições que afetam o cluster completo. Estas condições afetam várias entidades no cluster ou o próprio cluster. Com base na condição, o relatório não é possível limitar o problema para baixo para um ou mais elementos subordinados mau estado de funcionamento. Os exemplos incluem o "-brain" do cluster dividir devido a problemas de criação de partições ou de comunicação de rede.
* **Nó**. Representa o estado de funcionamento de um nó de Service Fabric. Relatórios de estado de funcionamento do nó descrevem as condições que afetam a funcionalidade de nó. Normalmente, estes afetam todas as entidades implementadas em execução no mesmo. Os exemplos incluem nó sem espaço em disco (ou outras propriedades globais, como a memória, ligações) e quando um nó para baixo. A entidade do nó é identificada pelo nome do nó (cadeia).
* **Aplicação**. Representa o estado de funcionamento de uma instância de aplicação em execução no cluster. Relatórios de estado de funcionamento da aplicação descrevem as condições que afetam o estado de funcionamento geral da aplicação. Estes não podem ser narrowed para baixo a elementos subordinados individuais (serviços ou aplicações implementadas). Os exemplos incluem a interação de ponto a ponto entre os diferentes serviços na aplicação. A entidade de aplicação é identificada pelo nome da aplicação (URI).
* **Serviço**. Representa o estado de funcionamento de um serviço em execução no cluster. Relatórios de estado de funcionamento do serviço descrevem as condições que afetam o estado de funcionamento geral do serviço. O relatório não é possível restringir o problema para um mau estado de funcionamento partição ou a réplica. Os exemplos incluem uma configuração de serviço (por exemplo, a porta ou partilha de ficheiro externo) que está a causar problemas de todas as partições. A entidade de serviço é identificada pelo nome do serviço (URI).
* **Partição**. Representa o estado de funcionamento de uma partição de serviço. Relatórios de estado de funcionamento de partição descrevem as condições que afetam o conjunto completo de réplica. Os exemplos incluem o número de réplicas é inferior a contagem de destino e a uma partição é na perda de quórum. A entidade de partição é identificada por partição ID (GUID).
* **Réplica**. Representa o estado de funcionamento de uma réplica de monitorização de estado de serviço ou uma instância de serviço sem estado. A réplica é a unidade de menor que watchdogs e componentes de sistema podem reportar para uma aplicação. Para os serviços com monitorização de estado, os exemplos incluem uma réplica primária, que não é possível replicar operações secundárias e replicação lenta. Além disso, uma instância sem monitorização de estado pode reportar quando está a ficar sem recursos ou tem problemas de conectividade. A entidade de réplica é identificada pela ID (GUID) da partição e o ID de réplica ou de instância (longa).
* **DeployedApplication**. Representa o estado de funcionamento de um *aplicação em execução num nó*. Relatórios de estado de funcionamento da aplicação implementada descrevem condições específicas para a aplicação no nó que não pode ser narrowed para baixo para pacotes de serviço implementados no mesmo nó. Os exemplos incluem erros quando não é possível transferir o pacote de aplicação nesse nó e problemas de configuração de principais de segurança da aplicação no nó. A aplicação implementada é identificada pelo nome da aplicação (URI) e o nome do nó (cadeia).
* **DeployedServicePackage**. Representa o estado de funcionamento de um pacote de serviço em execução num nó do cluster. Descreve as condições específicas a um pacote de serviço que não afetam os outros pacotes de serviço no mesmo nó para a mesma aplicação. Os exemplos incluem um pacote do código no pacote de serviço que não pode ser iniciado e de um pacote de configuração que não é possível ler. O pacote de serviço implementado é identificado pelo nome da aplicação (URI), o nome de nó (cadeia), o nome do manifesto do serviço (cadeia) e o ID de ativação de pacote de serviço (cadeia).

A granularidade do modelo de estado de funcionamento torna mais fácil detetar e corrigir problemas. Por exemplo, se um serviço de mensagens em fila não está a responder, é exequível para comunicar que a instância da aplicação está danificada. Relatórios em que não é ideal, no entanto, porque o problema poderá não estar a afetar todos os serviços dentro dessa aplicação. O relatório deve ser aplicado para o serviço de estado de funcionamento incorreto ou a uma partição subordinada específico, se as informações mais apontar para esse partição. Os dados automaticamente analisa através da hierarquia e uma partição de mau estado de funcionamento ficam visíveis em níveis de serviços e de aplicações. Esta agregação ajuda a identificar e resolver a causa do problema mais rapidamente.

A hierarquia de estado de funcionamento é composta de relações principal-subordinado. Um cluster é composto por nós e aplicações. As aplicações tem os serviços e aplicações implementadas. Aplicações implementadas tem implementado os pacotes de serviços. Serviços tem partições e cada partição tem uma ou mais réplicas. Há uma relação especial entre nós e entidades implementadas. Um mau estado de funcionamento nó conforme comunicado pelo respectivo componente de sistema de autoridade, o serviço do Gestor de ativação pós-falha, afeta as aplicações implementadas, pacotes de serviços e as réplicas implementadas (s).

A hierarquia de estado de funcionamento representa o estado mais recente do sistema com base em relatórios de estado de funcionamento mais recentes, que é informações quase em tempo real.
As entidades mesmas com base no específicas da aplicação lógica ou condições monitorizadas personalizadas podem reportar watchdogs internos e externos. Relatórios de utilizador coexistem com os relatórios do sistema.

Pretende investir em como relatórios e responder para Estado de funcionamento durante a conceção de um serviço em nuvem grandes. Este investement prévio facilita o serviço de depuração, monitorizar e operar.

## <a name="health-states"></a>Estados de funcionamento
O Service Fabric utiliza três Estados de funcionamento para descrever se uma entidade está em bom estada ou não: OK, aviso e erro. Qualquer relatório enviado para o arquivo de estado de funcionamento tem de especificar um destes Estados. O resultado da avaliação do Estado de funcionamento é uma destes Estados.

Os possíveis [Estados de funcionamento](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate) são:

* **OK**. A entidade está em bom estada. Não existem não existem problemas conhecidos reportados- ou nos respectivos subordinados (quando aplicável).
* **Aviso**. A entidade tem alguns problemas, mas ainda pode funcionar corretamente. Por exemplo, existem atrasos, mas não causarem quaisquer problemas funcionais ainda. Em alguns casos, a condição de aviso pode corrigir próprio sem intervenção externa. Nestes casos, os relatórios de estado de funcionamento aumentar a deteção em fornecem visibilidade sobre o que se passa. Noutros casos, a condição de aviso pode degradar um problema grave sem intervenção do utilizador.
* **Erro**. A entidade está danificada. Deverão ser executadas para corrigir o estado da entidade, porque este não poderá funcionar corretamente.
* **Desconhecido**. A entidade não existe no arquivo de estado de funcionamento. Este resultado pode ser obtido a partir de consultas distribuídas que intercalar os resultados de vários componentes. Por exemplo, a consulta da lista get nó entra em **FailoverManager**, **ClusterManager**, e **HealthManager**; obter a aplicação vai de consulta da lista para  **ClusterManager** e **HealthManager**. Estas consultas intercalar os resultados de vários componentes do sistema. Se o outro componente do sistema devolver uma entidade que não está presente no arquivo de estado de funcionamento, o resultado intercalado tem desconhecido estado de funcionamento. Uma entidade não está no arquivo porque ainda não foram processados relatórios de estado de funcionamento ou a entidade foi limpa após a eliminação.

## <a name="health-policies"></a>Políticas de estado de funcionamento
O arquivo de estado de funcionamento se aplica a políticas de estado de funcionamento para determinar se uma entidade está em bom estado com base nos seus relatórios e os respectivos valores secundários.

> [!NOTE]
> Políticas de estado de funcionamento podem ser especificadas no manifesto do cluster (para avaliação de estado de funcionamento do cluster e o nó) ou no manifesto da aplicação (para a avaliação da aplicação e algum dos respetivos grupos subordinados). Também podem passar de pedidos de avaliação do Estado de funcionamento nas políticas de avaliação do Estado de funcionamento personalizado, que são utilizadas apenas para esse avaliação.
> 
> 

Por predefinição, o Service Fabric aplica regras strict (tudo tem de estar em bom estado) para a relação hierárquica principal-subordinado. Se ainda um do subordinado tem um evento mau estado de funcionamento, o elemento principal é considerado mau estado de funcionamento.

### <a name="cluster-health-policy"></a>Política de estado de funcionamento do cluster
O [política de estado de funcionamento do cluster](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy) é utilizado para avaliar o estado de funcionamento do cluster e Estados de funcionamento do nó. A política pode ser definida no manifesto do cluster. Se não estiver presente, a política predefinida (zero falhas tolerated) é utilizada.
Contém a política de estado de funcionamento do cluster:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Especifica se tratar de estado de funcionamento de aviso relatórios como erros durante a avaliação de estado de funcionamento. Predefinição: false.
* [MaxPercentUnhealthyApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Especifica a percentagem máxima de tolerated das aplicações que podem ser mau estado de funcionamento antes do cluster for considerado no erro.
* [MaxPercentUnhealthyNodes](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Especifica a percentagem máxima de tolerated de nós que podem ser mau estado de funcionamento antes do cluster for considerado no erro. Em grandes clusters, alguns nós são sempre baixo ou horizontalmente para reparações, pelo que esta percentagem deve ser configurada para tolerar que.
* [ApplicationTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). O mapa de política de estado de funcionamento de tipo de aplicação pode ser utilizado durante a avaliação de estado de funcionamento do cluster para descrever os tipos de aplicações especiais. Por predefinição, todas as aplicações são colocadas um conjunto e avaliadas com MaxPercentUnhealthyApplications. Se alguns tipos de aplicações devem ser tratados de forma diferente, pode ser retirados do conjunto global. Em vez disso, são avaliados em relação as percentagens associadas com os respetivos nomes de tipo de aplicação no mapa. Por exemplo, num cluster existem milhares de aplicações de diferentes tipos e algumas instâncias de aplicações de controlo de um tipo especial de aplicação. As aplicações de controlo nunca devem estar no erro. Pode especificar MaxPercentUnhealthyApplications global para 20% para tolerar algumas falhas, mas para o tipo de aplicação "ControlApplicationType" definir o MaxPercentUnhealthyApplications como 0. Desta forma, se algumas das muitas aplicações estão danificadas, mas abaixo da percentagem de mau estado de funcionamento global, o cluster será avaliado para aviso. Um Estado de funcionamento de aviso não afeta a atualização do cluster ou outros monitorização é acionada por Estado de funcionamento de erro. Mas, até um controlo de aplicação no registo de erros iria tornar o cluster mau estado de funcionamento, que aciona a reversão ou interrompe a atualização do cluster, dependendo da configuração de atualização.
  Para os tipos de aplicações definidos no mapa, todas as instâncias da aplicação são retiradas o conjunto global de aplicações. Estes são avaliados com base no número total de aplicações do tipo de aplicação, utilizando o MaxPercentUnhealthyApplications específico do mapa. Todos os o resto das aplicações permanecer no conjunto global e são avaliadas com MaxPercentUnhealthyApplications.

O exemplo seguinte é um excerpt do manifesto do cluster. Para definir as entradas no mapa de tipo de aplicação, preceder o nome de parâmetro com "ApplicationTypeMaxPercentUnhealthyApplications-", seguido do nome de tipo de aplicação.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Política de estado de funcionamento da aplicação
O [política de estado de funcionamento da aplicação](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy) descreve como a avaliação de agregação de eventos e Estados de subordinado é efetuada para aplicações e os respetivos subordinados. Pode ser definido no manifesto da aplicação, **ApplicationManifest.xml**, no pacote de aplicação. Se não existem políticas forem especificadas, Service Fabric parte do princípio de que a entidade é mau estado de funcionamento se tiver um relatório de estado de funcionamento ou um subordinado, o estado de funcionamento de aviso ou erro.
As políticas configuráveis são:

* [ConsiderWarningAsError](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Especifica se tratar de estado de funcionamento de aviso relatórios como erros durante a avaliação de estado de funcionamento. Predefinição: false.
* [MaxPercentUnhealthyDeployedApplications](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Especifica a percentagem máxima de tolerated de aplicações que podem ser mau estado de funcionamento antes da aplicação for considerada no erro. Esta percentagem é calculada dividindo o número de mau estado de funcionamento aplicações implementadas através do número de nós que as aplicações atualmente são implementadas no cluster. O cálculo Arredonda por excesso para tolerar uma falha num pequeno número de nós. Percentagem de predefinido: zero.
* [DefaultServiceTypeHealthPolicy](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Especifica a serviço tipo estado de funcionamento política predefinida, que substitui a política de estado de funcionamento predefinida para todos os tipos de serviço na aplicação.
* [ServiceTypeHealthPolicyMap](https://docs.microsoft.com/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Fornece um mapa de políticas de estado de funcionamento do serviço por tipo de serviço. Estas políticas substituem as políticas de estado de funcionamento do tipo de serviço predefinido para cada tipo de serviço especificado. Por exemplo, se uma aplicação tem um tipo de serviço de gateway sem monitorização de estado e um tipo de serviço do motor com monitorização de estado, pode configurar as políticas de estado de funcionamento para a respetiva avaliação de forma diferente. Quando especificar política de cada tipo de serviço, pode obter um controlo mais granular do Estado de funcionamento do serviço.

### <a name="service-type-health-policy"></a>Política de estado de funcionamento do tipo de serviço
O [política de estado de funcionamento do tipo de serviço](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy) Especifica como avaliar e agregar os serviços e os subordinados de serviços. A política contém:

* [MaxPercentUnhealthyPartitionsPerService](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Especifica a percentagem máxima de tolerated de partições mau estado de funcionamento antes de um serviço é considerado em mau estado de funcionamento. Percentagem de predefinido: zero.
* [MaxPercentUnhealthyReplicasPerPartition](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Especifica a percentagem máxima de tolerated das réplicas mau estado de funcionamento antes de uma partição é considerada em mau estado de funcionamento. Percentagem de predefinido: zero.
* [MaxPercentUnhealthyServices](https://docs.microsoft.com/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Especifica a percentagem máxima de tolerated dos serviços de mau estado de funcionamento antes da aplicação é considerada em mau estado de funcionamento. Percentagem de predefinido: zero.

O exemplo seguinte é um excerpt de um manifesto de aplicação:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Avaliação de estado de funcionamento
Utilizadores e serviços automatizados podem avaliar o estado de funcionamento de qualquer entidade em qualquer altura. Para avaliar o estado de funcionamento de uma entidade, os Estado de funcionamento arquivo agregados estado de funcionamento de todos os relatórios na entidade e avalia todos os respetivos elementos subordinados (quando aplicável). O algoritmo de agregação do Estado de funcionamento utiliza políticas de estado de funcionamento avaliar os relatórios de estado de funcionamento e como agregar Estados de funcionamento de subordinados (quando aplicável).

### <a name="health-report-aggregation"></a>Agregação de relatório de estado de funcionamento
Uma entidade pode ter vários relatórios de estado de funcionamento enviados pelo Informadores diferentes (componentes de sistema ou watchdogs) nas propriedades diferentes. A agregação utiliza políticas de estado de funcionamento, em particular o membro de ConsiderWarningAsError de política de estado de funcionamento da aplicação ou o cluster. ConsiderWarningAsError Especifica como avaliar avisos.

O estado de funcionamento agregados é acionado pelo *pior* relatórios de estado de funcionamento na entidade. Se houver um relatório de estado de funcionamento de pelo menos um erro, o estado de funcionamento agregados é um erro.

![Agregação de relatório de estado de funcionamento com o relatório de erros.][2]

Uma entidade de estado de funcionamento que tem um ou mais relatórios de estado de funcionamento de erro é avaliada como erro. O mesmo se aplica-se um relatório de estado de funcionamento expirada, independentemente do Estado de funcionamento.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se existirem sem relatórios de erros e um ou mais avisos, o estado de funcionamento agregados é aviso ou erro, consoante o sinalizador de política de ConsiderWarningAsError.

![Agregação de relatório de estado de funcionamento com o relatório de aviso e ConsiderWarningAsError false.][3]

Agregação de relatório de estado de funcionamento com o relatório de aviso e ConsiderWarningAsError definido como FALSO (predefinição).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregação do Estado de funcionamento de subordinados
O estado de funcionamento de agregados de uma entidade reflete os Estados de funcionamento de subordinados (quando aplicável). O algoritmo para agregar Estados de funcionamento do subordinado utiliza as políticas de estado de funcionamento aplicáveis com base no tipo de entidade.

![Agregação do Estado de funcionamento de entidades de subordinados.][4]

Agregação de subordinados com base nas políticas de estado de funcionamento.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Depois do arquivo de estado de funcionamento foi avaliada todos os elementos subordinados, agrega os Estados de funcionamento com base na percentagem de máxima configurada de subordinados mau estado de funcionamento. Esta percentagem é retirada da política consoante o tipo de entidade e subordinados.

* Se todos os elementos subordinados têm Estados OK, o estado de funcionamento do subordinado agregado é OK.
* Se tiverem subordinados OK e Estados de aviso, o estado de funcionamento do subordinado agregado indica um aviso.
* Se existirem subordinados com os Estados de erro não respeitem o máximo permitido de percentagem de subordinados mau estado de funcionamento, o estado de funcionamento agregados é um erro.
* Se os subordinados com os Estados de erro respeitem a percentagem máxima permitida de subordinados mau estado de funcionamento, o estado de funcionamento agregados indica um aviso.

## <a name="health-reporting"></a>Relatórios de estado de funcionamento
Componentes do sistema, aplicações de recursos de infraestrutura de sistema e interna/externa watchdogs podem reportar contra entidades do Service Fabric. Tornar os Informadores *local* determinations do Estado de funcionamento das entidades monitorizados, com base nas condições que estão a monitorizar. Não precisam de observar qualquer Estado global ou os dados agregados. O comportamento pretendido é ter Informadores simples e complexos não vivos que tem de observar inúmeros aspetos para inferir as informações para enviar.

Para enviar dados de estado de funcionamento para o arquivo de estado de funcionamento, um relatório tem de identificar a entidade afetada e criar um relatório de estado de funcionamento. Para enviar o relatório, utilize o [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API, o estado de funcionamento de relatório APIs expostas no `Partition` ou `CodePackageActivationContext` objetos, os cmdlets do PowerShell ou REST.

### <a name="health-reports"></a>Relatórios de estado de funcionamento
O [relatórios de estado de funcionamento](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthreport) para cada uma das entidades no cluster de conter as seguintes informações:

* **SourceId**. Uma cadeia que identifica exclusivamente o relatório do evento de estado de funcionamento.
* **Identificador da entidade**. Identifica a entidade em que o relatório é aplicado. Difere consoante o [tipo de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Cluster. nenhum.
  * Nó. Nome do nó (cadeia).
  * aplicação. Nome da aplicação (URI). Representa o nome da instância de aplicação implementado no cluster.
  * Serviço. Nome do serviço (URI). Representa o nome da instância do serviço implementado no cluster.
  * Partição. ID de partição (GUID). Representa o identificador exclusivo da partição.
  * Réplica. O ID de réplica de monitorização de estado do serviço ou o ID de instância de serviço sem estado (INT64).
  * DeployedApplication. Nome da aplicação (URI) e o nome do nó (cadeia).
  * DeployedServicePackage. Nome da aplicação (URI), o nome de nó (cadeia) e o service manifestam nome (cadeia).
* **Propriedade**. A *cadeia* (não uma enumeração fixa) que permite que o relatório para categorizar o evento de estado de funcionamento para uma propriedade específica da entidade. Por exemplo, o relatório A pode comunicar o estado de funcionamento a Node01 propriedade de "Storage" e o relatório B podem comunicar o estado de funcionamento a Node01 propriedade de "Conectividade". No arquivo de estado de funcionamento, estes relatórios são tratados como eventos de estado de funcionamento separado para a entidade de Node01.
* **Descrição**. Uma cadeia que permite que um relatório fornecer informações detalhadas sobre o evento de estado de funcionamento. **SourceId**, **propriedade**, e **HealthState** totalmente deve descrever o relatório. A descrição adiciona legível por humanos informações sobre o relatório. O texto torna mais fácil para os administradores e utilizadores a compreender o relatório de estado de funcionamento.
* **HealthState**. Um [enumeração](service-fabric-health-introduction.md#health-states) que descreve o estado de funcionamento do relatório. Os valores aceites são OK, aviso e erro.
* **TimeToLive**. Timespan que indica quanto o relatório de estado de funcionamento do é válido. Conjugados com **RemoveWhenExpired**, permite que o arquivo de estado de funcionamento sabe como avaliar eventos expirados. Por predefinição, o valor é infinito e o relatório é válido para sempre.
* **RemoveWhenExpired**. Um booleano. Se definido como true, o relatório de estado de funcionamento expirada é removido automaticamente do arquivo de estado de funcionamento e o relatório não tem impacto avaliação de estado de funcionamento da entidade. Utilizada quando o relatório é válido durante um período de tempo só especificado e o relatório não tem explicitamente limpá-lo a. Também é utilizado para eliminar relatórios a partir do arquivo de estado de funcionamento (por exemplo, um watchdog do é alterado e deixa de enviar relatórios com origem de anterior e propriedade). Pode enviar um relatório com um breve TimeToLive juntamente com RemoveWhenExpired para limpar quaisquer estado anterior do arquivo de estado de funcionamento. Se o valor estiver definido como false, o relatório expirado é tratado como um erro na avaliação de estado de funcionamento. O valor falso indica ao arquivo de estado de funcionamento que a origem de deve reportar periodicamente nesta propriedade. Se não, em seguida, tem de existir um problema com o watchdog do. Estado de funcionamento do watchdog é capturado por considerar o evento como um erro.
* **SequenceNumber**. Um número inteiro positivo que tem de ser alguma vez aumentar, representa a ordem dos relatórios. É utilizado pelo arquivo de estado de funcionamento para detetar obsoletos relatórios que são recebidos tarde devido a atrasos de rede ou outros problemas. Um relatório é rejeitado se o número de sequência é que menor ou igual a mais recentemente aplicadas número para a mesma entidade, a origem e a propriedade. Se não for especificado, o número de sequência é gerado automaticamente. É necessário colocar o número de sequência apenas quando os relatórios em transições de Estados. Nesta situação, a origem tem Lembre-se de que relatórios, enviados e manter as informações de recuperação na ativação pós-falha.

Estas quatro duas das informações – SourceId, identificador da entidade, propriedade e HealthState - são necessários para cada relatório de estado de funcionamento. A cadeia de SourceId não tem permissão para começar a utilizar o prefixo "**System.**", que está reservado para relatórios do sistema. Para a mesma entidade, há apenas um relatório para a mesma origem e a propriedade. Vários relatórios para a mesma origem e propriedade substituam entre si, no lado do cliente de estado de funcionamento (se estiver em lotes) ou no estado de funcionamento armazenar lado. A substituição baseia-se em números de sequência; relatórios mais recentes (com números de sequência superiores) substituem relatórios mais antigos.

### <a name="health-events"></a>Eventos de estado de funcionamento
Internamente, o arquivo de estado de funcionamento mantém [eventos de estado de funcionamento](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthevent), que contém todas as informações dos relatórios e metadados adicionais. Os metadados incluem a hora que do relatório foi atribuído ao cliente do Estado de funcionamento e o tempo que tenha sido modificado no lado do servidor. Os eventos de estado de funcionamento são devolvidos pelo [consultas de estado de funcionamento](service-fabric-view-entities-aggregated-health.md#health-queries).

Contém os metadados adicionado:

* **SourceUtcTimestamp**. O tempo o relatório foi atribuído ao cliente do Estado de funcionamento (hora de Universal Coordenada).
* **LastModifiedUtcTimestamp**. A hora em que o relatório foi modificado pela última vez no lado do servidor (hora de Universal Coordenada).
* **IsExpired**. Um sinalizador que indica se o relatório expirou quando a consulta foi executada pelo arquivo de estado de funcionamento. Um evento pode ter expirado apenas se RemoveWhenExpired está definido como false. Caso contrário, o evento não será devolvido pela consulta e é removido a partir da loja.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Última hora de transições OK/aviso/erro. Estes campos dê o histórico do Estado de funcionamento transições de estado para o evento.

Os campos de transição de estado podem ser utilizados para obter informações de evento de estado de funcionamento "históricos" ou alertas mais inteligentes. Se ativar cenários tais como:

* Alerta quando uma propriedade tiver sido em aviso/erro para mais de X minutos. A verificar a condição de um período de tempo evita alertas em condições temporárias. Por exemplo, pode ser convertido um alerta se o estado de funcionamento tem sido aviso durante mais de cinco minutos (HealthState = = aviso e agora - LastWarningTransitionTime > 5 minutos).
* Alerta apenas em condições que foram alterados nos últimos X minutos. Se um relatório já estava no erro anterior à hora especificada, pode ser ignorado porque já foi indicado anteriormente.
* Determinar se uma propriedade é ativando ou desativando entre avisos e erros, quanto foi mau estado de funcionamento (OK ou seja, não). Por exemplo, pode ser convertido um alerta se a propriedade não foi bom durante mais de cinco minutos (HealthState! = Ok e agora - LastOkTransitionTime > 5 minutos).

## <a name="example-report-and-evaluate-application-health"></a>Exemplo: Relatório e avaliar o estado de funcionamento da aplicação
O exemplo seguinte envia um relatório de estado de funcionamento através do PowerShell na aplicação **fabric: / WordCount** da origem **MyWatchdog**. O relatório de estado de funcionamento contém informações sobre a propriedade de estado de funcionamento "availability" em estado de funcionamento de erro, com TimeToLive infinito. Em seguida, consulta o estado de funcionamento de aplicações, que devolve agregado erros de estado de funcionamento e os eventos de estado de funcionamento comunicado na lista de eventos de estado de funcionamento.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Utilização do modelo de estado de funcionamento
O modelo de estado de funcionamento permite serviços em nuvem e a plataforma subjacente do Service Fabric para dimensionar, porque a monitorização e estado de funcionamento determinations são distribuídos entre os monitores diferentes dentro do cluster.
Outros sistemas tem um serviço único e centralizado ao nível do cluster que analisa todos os *potencialmente* informações úteis emitidas pelo serviços. Esta abordagem hinders os respetivos escalabilidade. -Também não permite-lhe recolher informações específicas para ajudar a identificar problemas e de potenciais problemas como próximo a causa raiz quanto possível.

O modelo de estado de funcionamento é utilizado descontos elevados para monitorização e diagnóstico, para avaliar o estado de funcionamento do cluster e da aplicação e para atualizações monitorizadas. Outros serviços utilizam dados de estado de funcionamento para efetuar reparações automáticas, criar o histórico de estado de funcionamento do cluster e emitir alertas em determinadas condições.

## <a name="next-steps"></a>Passos seguintes
[Ver relatórios de estado de funcionamento do Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Utilizar relatórios de estado de funcionamento do sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Como comunicar e verifique o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Adicionar relatórios de estado de funcionamento personalizados do Service Fabric](service-fabric-report-health.md)

[Monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md)


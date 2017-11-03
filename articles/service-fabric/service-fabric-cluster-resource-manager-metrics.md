---
title: "Gerir a carga de microsserviço do Azure através de métricas | Microsoft Docs"
description: "Saiba mais sobre como configurar e utilizar as métricas no Service Fabric para gerir o consumo de recursos do serviço."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5c291ef864518b2366c61c9e5c11fac9e8468a00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Gestão consumo de recursos e a carga no Service Fabric com a métrica
*Métricas* são os recursos que o cuidado de serviços sobre e que são fornecidos por nós do cluster. Uma métrica é tudo o que pretende gerir para melhorar ou monitorizar o desempenho dos seus serviços. Por exemplo, poderá ver o consumo de memória para saber se o serviço está sobrecarregado. Utilize outro consiste em descobrir se o serviço foi possível mover a noutro local onde a memória é que inferior restrita para obter um melhor desempenho.

Coisas como a utilização de memória, disco e CPU são exemplos de métricas. Estas métricas são métricas físicas, recursos que correspondem aos recursos físicos no nó que precisam de ser geridas. Métricas também podem ser (e são geralmente) métricas lógicas. A métrica de lógica está coisas como "MyWorkQueueDepth" ou "MessagesToProcess" ou "TotalRecords". Métricas lógicas são definidas pela aplicação e indiretamente correspondem a algumas consumo de recursos físicos. Métricas lógicas são comuns porque pode ser difícil de medidas e relatório consumo de recursos físicos numa base por serviço. A complexidade de medição e elaboração de relatórios de métricas físicas é também por que motivo o Service Fabric fornece algumas nas métricas predefinidas.

## <a name="default-metrics"></a>Nas métricas predefinidas
Digamos que pretende começar a escrever e implementar o seu serviço. Neste momento, não sabe que recursos físicos ou lógicos que consome. Que é adequado! O Gestor de recursos de Cluster do serviço de recursos de infraestrutura utiliza algumas nas métricas predefinidas quando não existem outras métricas são especificadas. São:

  - PrimaryCount - contagem de réplicas primárias no nó 
  - ReplicaCount - contagem de totais réplicas com monitorização de estado no nó
  - Contagem - contagem de todos os objetos do serviço (sem monitorização de estado e com monitorização de estado) no nó

| Métrica | Carga de instância sem monitorização de estado | Carga secundária com monitorização de estado | Com monitorização de estado carga primária |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Contagem |1 |1 |1 |

Para cargas de trabalho básicas, nas métricas predefinidas de fornecem uma distribuição decent de trabalho no cluster. No exemplo seguinte, vamos ver o que acontece quando iremos criar dois serviços e baseiam-se nas métricas predefinidas para o balanceamento de. O primeiro serviço é um serviço com monitorização de estado com partições de três e definir o tamanho de três uma réplica de destino. O serviço do segundo é um serviço sem monitorização de estado com uma partição e uma contagem de instâncias de três.

Eis o que obtém:

<center>
![Esquema de cluster com nas métricas predefinidas][Image1]
</center>

Algumas coisas a salientar:
  - Réplicas primárias para o serviço de monitorização de estado são distribuídas por vários nós
  - Réplicas para a mesma partição estão em nós diferentes
  - O número total de primaries e bases de dados secundárias é distribuído no cluster
  - O número total de objetos do serviço uniformemente é alocado em cada nó

Boa!

Nas métricas predefinidas de great funcionam como um início. No entanto, nas métricas predefinidas de irão apenas transportar, até ao momento. Por exemplo: o que é a probabilidade de que a criação de partições de esquema selecionado resultados no mesmo perfeitamente utilização por todas as partições? O que é a probabilidade de que a carga para um determinado serviço for constante ao longo do tempo, ou mesmo apenas mesmas entre várias partições agora?

É possível executar com apenas nas métricas predefinidas. No entanto, se o fizer, normalmente, significa que a utilização do cluster inferior e mais desigual que gostaria de. Isto acontece porque nas métricas predefinidas não são adaptável e presumem que tudo é equivalente. Por exemplo, um site primário que está ocupado e outro que não é ambas contribuem para a métrica PrimaryCount "1". Na pior das hipóteses, a utilizar apenas nas métricas predefinidas pode também resultar em nós overscheduled, resultando em problemas de desempenho. Se estiver interessado em obter o máximo partido do cluster e evitar problemas de desempenho, terá de utilizar métricas personalizadas e relatórios de carga dinâmico.

## <a name="custom-metrics"></a>Métricas personalizadas
As métricas são configuradas numa base por denominado-service-instância quando estiver a criar o serviço.

Qualquer métrica tem algumas propriedades que descrevem este: um nome, uma ponderação e uma carga predefinida.

* O nome da métrica: O nome da métrica. O nome da métrica é um identificador exclusivo para a métrica do cluster, o Gestor de recursos perspetiva.
* Peso: Ponderação métrica define como importante esta métrica é relativo as outras métricas para este serviço.
* Carga de predefinição: A carga predefinido é representada forma diferente dependendo se o serviço é sem monitorização de estado ou com monitorização de estado.
  * Para os serviços sem monitorização de estado, cada métrica tem uma propriedade de único com o nome DefaultLoad
  * Para definir os serviços com monitorização de estado:
    * PrimaryDefaultLoad: O período desta métrica predefinido este serviço consome quando for um site primário
    * SecondaryDefaultLoad: O período desta métrica predefinido este serviço consome quando for uma secundária

> [!NOTE]
> Se definir métricas personalizadas e pretender _também_ utilizar nas métricas predefinidas, terá de _explicitamente_ adicionar nas métricas predefinidas de criar e definem ponderações e valores para os mesmos. Isto acontece porque tem de definir a relação entre nas métricas predefinidas e as métricas personalizadas. Por exemplo, talvez que mais lhe interessam ConnectionCount ou WorkQueueDepth mais do que a distribuição primária. Por predefinição, a ponderação da métrica PrimaryCount é alta, pretende reduzir a média ao adicionar as outras métricas para se certificar de que têm precedência.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definir métricas para o seu serviço - um exemplo
Vamos supor que pretende que a configuração seguinte:

  - O serviço de relatórios uma métrica com o nome "ConnectionCount"
  - Também querer utilizar nas métricas predefinidas 
  - Tiver terminado algumas medidas e saber o que normalmente uma réplica primária do que o serviço demora 20 unidades de "ConnectionCount"
  - Bases de dados secundárias utilizam 5 unidades de "ConnectionCount"
  - Sabe que "ConnectionCount" é a métrica mais importante em termos de gerir o desempenho deste serviço específico
  - Pretender continuar com balanceamento de réplicas primárias. Balanceamento de réplicas primárias geralmente é uma boa ideia, independentemente da que. Isto ajuda a impedir a perda de algumas domínio nó ou falhas afetar a maioria das réplicas primárias juntamente com o mesmo. 
  - Caso contrário, nas métricas predefinidas de estejam funcionais

Eis o código que poderia escrever para criar um serviço com que a configuração da métrica:

Código:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Os exemplos acima e o resto deste documento descrevem as métricas de gestão numa base por-nome-service. Também é possível definir métricas para os serviços do serviço _tipo_ nível. Isto é conseguido através da especificação-los no seu manifestos de serviço. Definir as métricas de nível de tipo não é recomendada por vários motivos. O motivo principal é que os nomes de métricos são frequentemente específico do ambiente. A menos que exista um contrato sólido no local, não é possível não se esqueça de que a métrica "Núcleos" no um ambiente não se encontra "MiliCores" ou "Núcleos" num outras pessoas. Se as métricas são definidas no seu manifesto que tem de criar novos manifestos por ambiente. Normalmente, isto leva a uma proliferação de manifestos diferentes com apenas diferenças menores, que podem levar a dificuldades de gestão.  
>
> Métricas cargas normalmente são atribuídas numa base por-nome--instância de serviço. Por exemplo, vamos supor que criar uma instância do serviço para CustomerA que planeia apenas ligeiramente a utilizá-lo. Também Digamos que criar outro para CustomerB que tenha uma maior carga de trabalho. Neste caso, seriam provavelmente pretende otimizar cargas a predefinição para esses serviços. Se tiver métricas e carrega definida através de manifestos e pretende suportar este cenário, requer diferentes aplicações e tipos de serviço para cada cliente. Os valores definidos no momento de criação do serviço substituem as definido no manifesto, pelo que utilizar que definir as predefinições específicas. No entanto, se o fizer que faz com que os valores declarados nos manifestos para não corresponderem às, na verdade, o serviço seja executado com. Isto pode levar a confusão. 
>

Como um lembrete: Se pretender utilizar nas métricas predefinidas, não precisa de touch a coleção de métricas de todo ou fazer nada especiais ao criar o seu serviço. Nas métricas predefinidas de obterem utilizadas automaticamente quando não existem outros são definidos. 

Agora, vamos através de cada uma destas definições em mais detalhe e falar sobre o comportamento que afeta.

## <a name="load"></a>Carregar
O ponto de todo de definir métricas é representam alguns carga. *Carga* é a quantidade de uma determinada métrica é consumido por algumas instâncias de serviço ou a réplica de um determinado nó. Carga pode ser configurada em praticamente qualquer momento. Por exemplo:

  - Carga pode ser definida quando é criado um serviço. Esta opção é denominada _carga predefinido_.
  - As informações da métricas, incluindo cargas predefinido, para um serviço pode atualizado depois do serviço é criado. Esta opção é denominada _atualizar um serviço_. 
  - As cargas de uma determinada partição podem ser repostas para os valores predefinidos para esse serviço. Esta opção é denominada _repor a carga da partição_.
  - Pode ser comunicada carga num por base do objeto de serviço dinamicamente durante o tempo de execução. Esta opção é denominada _reporting carga_. 
  
Todas estas estratégias podem ser utilizadas no mesmo serviço longo da respetiva duração. 

## <a name="default-load"></a>Carga predefinido
*Predefinição carga* é a quantidade da métrica consome de cada objeto de serviço (instância sem monitorização de estado ou réplica com monitorização de estado) deste serviço. O Gestor de recursos de Cluster utiliza este número para a carga do objeto de serviço até receber outras informações, tais como um relatório de carga dinâmico. Para os serviços mais simples, a carga predefinido é uma definição de estática. A carga de predefinição nunca é atualizada e é utilizada para a duração do serviço. Predefinição carrega ótimo funciona para cenários onde determinadas quantidades de recursos estão dedicadas para cargas de trabalho diferentes e não alterar o planeamento de capacidade simple.

> [!NOTE]
> Para obter mais informações sobre a gestão de capacidade e definir as capacidades para os nós do cluster, consulte [neste artigo](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

O Gestor de recursos de Cluster permite que os serviços com monitorização de estado especificar um carregamento predefinido diferente para os respetivos Primaries e secundárias. Serviços sem monitorização de estado só podem especificar um valor que se aplica a todas as instâncias. Para serviços com monitorização de estado, a carga predefinido para o site primário e secundário réplicas são normalmente diferentes, uma vez que as réplicas fazer diferentes tipos de trabalho em cada função. Por exemplo, Primaries normalmente servir leituras e escritas e processar a carga computacional, a maioria enquanto não secundárias. Normalmente, a carga predefinida para uma réplica primária é superior da carga de predefinido para réplicas secundárias. Os números de real deve dependem os seus próprios valores.

## <a name="dynamic-load"></a>Carga dinâmico
Imaginemos que tiver sido a executar o seu serviço de tempo. Com alguns monitorização, ter reparado que:

1. Algumas partições ou instâncias de um determinado serviço consumam mais recursos do que outras pessoas
2. Alguns serviços tem carga varia ao longo do tempo.

Há muitos coisas que pode fazer com que estes tipos de flutuações de carga. Por exemplo, diferentes serviços ou partições estão associadas a diferentes clientes com requisitos diferentes. Também pode alterar a carga porque a quantidade de trabalho, que o serviço não varia ao longo do dia. Independentemente do motivo, normalmente, não há nenhum número único que pode utilizar para a predefinição. Isto é particularmente verdadeiro se quiser obter a maioria dos utilização fora do cluster. Qualquer valor que escolha para a carga de predefinição está errada algumas do tempo. Predefinição incorreta carrega o resultado no Cluster de Gestor de recursos através de ou em alocar recursos. Como resultado, tiver nós que são através de ou em utilizados, apesar do Gestor de recursos de Cluster pensa que balanceamento de cluster. Predefinição são ainda boa, uma vez que fornecem algumas informações para colocação inicial, mas não ainda é um bloco completo para cargas de trabalho reais. Para capturar com precisão os requisitos de recursos a alteração, o Gestor de recursos de Cluster permite cada objeto de serviço atualizar o seu próprio carga durante o tempo de execução. Esta opção é denominada relatórios de carga dinâmico.

Relatórios de carga dinâmico permitir que as réplicas ou instâncias para ajustar a respetiva carga comunicadas/alocação de métricas ao longo da respetiva duração. Uma réplica de serviço ou a instância que foi frio e não efetuar qualquer trabalho normalmente reportarão que estava a utilizar baixas quantidades de uma métrica indicada. Uma réplica ocupada ou instância reportarão que estão a utilizar mais.

Relatórios de carga por instâncias ou de réplicas permite que o Gestor de recursos de Cluster para reorganizar os objetos do serviço individual no cluster. Reorganizar os serviços de ajuda a garantir a que recebem os recursos que necessitam. Serviços ocupados eficazmente obter "recuperar" recursos a partir de outros as réplicas ou instâncias que estão atualmente frio ou fazer menos trabalho.

Dentro do Reliable Services, o código para comunicar carga dinamicamente este aspeto:

Código:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Um serviço pode comunicar em qualquer uma das métricas definidas para o mesmo no momento de criação. Se uma carga de relatórios do serviço para uma métrica de que não está configurado para utilizar, o Service Fabric ignora desse relatório. Se existirem outras métricas comunicadas ao mesmo tempo que são válidos, esses relatórios são aceites. Código do serviço pode medir e comunicar todas as métricas que confie como, e os operadores podem especificar a configuração da métrica para utilizar sem ter de alterar o código do serviço. 

### <a name="updating-a-services-metric-configuration"></a>Atualizar a configuração de métrica de um serviço
A lista de métricas associados ao serviço e as propriedades desses métricas podem ser atualizadas dinamicamente, enquanto o serviço está em direto. Isto permite a experimentação e flexibilidade. Alguns exemplos de quando isto é útil são:

  - desativar uma métrica com um relatório buggy para um serviço específico
  - reconfigurar as ponderações de métricas com base no comportamento pretendido
  - Ativar uma métrica de novo, apenas depois do código já foi implementado e validar através de outros mecanismos
  - alterar a carga predefinido para um serviço com base no comportamento observado e consumo

As principais APIs para alterar a configuração da métrica são `FabricClient.ServiceManagementClient.UpdateServiceAsync` em c# e `Update-ServiceFabricService` no PowerShell. Qualquer informação especificar com estas APIs substitui as informações de métricas existentes para o serviço imediatamente. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>A combinação de valores de carga predefinido e os relatórios de carga dinâmico
Carga predefinido e carrega dinâmica pode ser utilizadas para o mesmo serviço. Quando utiliza um serviço de carregamento predefinido e relatórios de carga dinâmico, carga predefinido funciona como uma estimativa até apareçam relatórios dinâmicos. Carregamento predefinido é boa porque proporciona o Gestor de recursos de Cluster algo para trabalhar com. A carga de predefinição permite que o Gestor de recursos de Cluster para colocar os objetos do serviço em localizações boas quando forem criados. Se não existem informações de carga predefinido, colocação dos serviços é efetivamente aleatória. Quando a carga relatórios chegam mais tarde a colocação aleatória inicial é frequentemente errada e o Gestor de recursos do Cluster tem de mover os serviços.

Vamos colocar o nosso exemplo anterior e ver o que acontece quando adicionamos alguns métricas personalizadas e, em seguida, relatórios de carga dinâmico. Neste exemplo, utilizamos "MemoryInMb" como uma métrica de exemplo.

> [!NOTE]
> Memória é uma das métricas de sistema que pode do Service Fabric [recursos governar](service-fabric-resource-governance.md), e relatórios por si é normalmente difícil. Não, na verdade, esperamos que seja um relatório sobre o consumo de memória Memória é utilizado aqui como uma ajuda para saber mais sobre as capacidades do Gestor de recursos do Cluster.
>

Vamos presumem que criámos inicialmente o serviço de monitorização de estado com o seguinte comando:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Como um lembrete, esta sintaxe é ("MetricName MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Vamos ver que um esquema de cluster possíveis pode ter o seguinte aspeto:

<center>
![Cluster equilibrado com métricas predefinidos e personalizados][Image2]
</center>

Algumas coisas que são vale a pena realçar:

* Cada réplicas secundárias dentro de uma partição podem ter as seus próprios carga
* Em geral as métricas de ter um aspeto equilibradas. Para memória, o rácio entre a carga máxima e mínima é 1.75 (o nó com o maior carga é N3, o menor é N2 e 28/16 = 1.75).

Existem algumas coisas que precisamos de explicam:

* O que determinou que o se um rácio de 1.75 foi razoável, ou não? Como o Gestor de recursos de Cluster saber se isto for suficientemente boa ou se houver mais trabalho fazer?
* Quando balanceamento acontecer?
* O que, significa que memória foi ponderada "Alto"?

## <a name="metric-weights"></a>Métricas ponderações
É importante controlar as mesmas métricas em serviços diferentes. Nessa vista global é o que permite que o Gestor de recursos de Cluster para controlar o consumo do cluster, balancear consumo em nós e certifique-se de que nós não passam capacidade. No entanto, os serviços podem ter vistas diferentes relativamente a importância da métrica mesma. Além disso, num cluster com várias métricas e muitos dos serviços, perfeitamente com balanceamento de soluções podem não existir para todas as métricas. Como é que o Gestor de recursos de Cluster deve processar estas situações?

Métricas ponderações permitir que o Gestor de recursos de Cluster para decidir como equilibrar o cluster quando não existe nenhuma resposta perfeita. Métricas ponderações também permitem o Gestor de recursos de Cluster para balancear serviços específicos de forma diferente. Métricas podem ter quatro níveis diferente ponderação: Zero, baixa, média e alta. Uma métrica com uma ponderação igual a zero contribui nada quando ponderar se as coisas são equilibradas ou não. No entanto, a sua carga ainda contribuir para a gestão de capacidade. Métricas com Zero ponderação são ainda úteis e são frequentemente utilizadas como parte do comportamento de serviço e a monitorização do desempenho. [Este artigo](service-fabric-diagnostics-event-generation-infra.md) fornece mais informações sobre a utilização de métricas para monitorização e diagnóstico dos seus serviços. 

O impacto real de peso de métrico diferentes do cluster é que o Gestor de recursos de Cluster gera diferentes soluções. Métricas ponderações indicam o Gestor de recursos de Cluster que determinadas métricas são mais importantes do que outras pessoas. Quando não há nenhuma solução perfeita do Gestor de recursos do Cluster pode preferir soluções que equilibrar as métricas ponderadas superiores melhor. Se um serviço pensa uma métrica específica é indiferente, que pode considerar a utilização dos que métrica imbalanced. Isto permite que outro serviço obter uma distribuição, mesmo que alguns métrica que é importante para a mesma.

Vamos ver um exemplo de alguns relatórios de carga e a métrica de forma diferente ponderações resulta em alocações diferentes no cluster. Neste exemplo, Vemos que mudar o peso relativo das métricas faz com que o Gestor de recursos de Cluster para criar disposições diferentes de serviços.

<center>
![Exemplo de métrica de ponderação e o seu impacto em soluções de balanceamento][Image3]
</center>

Neste exemplo, existem quatro diferentes serviços, todos os valores de diferentes relatórios dois diferentes com base nas métricas, MetricA e MetricB. Um cenário, todos os serviços definem MetricA é o mais importante (peso = alta) e MetricB como indiferente (peso = baixa). Como resultado, Vemos que o Gestor de recursos de Cluster coloca os serviços de modo a que MetricA é melhor balanceamento que MetricB. "Melhor equilibrada" significa que MetricA tem um inferior tem um desvio-padrão inferior que MetricB. No segundo caso, iremos inverter as ponderações de métricas. Como resultado, o Gestor de recursos de Cluster trocas serviços A e B, para obter uma alocação onde MetricB é melhor com balanceamento de MetricA.

> [!NOTE]
> Métricas ponderações determinam como o Gestor de recursos de Cluster deve equilibrar, mas não quando balanceamento deverá ocorrer. Para obter mais informações sobre balanceamento, veja [neste artigo](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Ponderações métricas global
Digamos ServiceA define MetricA como importância alta e ServiceB define a ponderação para MetricA baixa ou Zero. O que é o peso real que acaba por ficar obter utilizado?

Existem vários ponderações que são controladas por cada métrica. O peso da primeira é o definida para a métrica quando o serviço é criado. O peso da é uma ponderação global, que é calculada automaticamente. O Gestor de recursos de Cluster utiliza ambas estas ponderações quando a classificação de soluções. Tendo ambas as ponderações em consideração é importante. Isto permite que o Gestor de recursos de Cluster para equilibrar a cada serviço, de acordo com as suas próprias prioridades e certifique-se também que o cluster como um todo atribuído corretamente.

O que acontece se o Gestor de recursos de Cluster não se preocupar com saldo global e local? Bem, é fácil construir soluções que são equilibrados globalmente, mas que resultar no equilíbrio de recursos fraca para serviços individuais. No exemplo seguinte, vamos observar um serviço configurado com apenas nas métricas predefinidas e ver o que acontece quando apenas saldo global é considerado:

<center>
![O impacto de uma solução apenas Global][Image4]
</center>

No exemplo superior em apenas saldo global, o cluster como um todo, de facto, é balanceado. Todos os nós têm a mesma contagem de primaries e as mesmas número totais as réplicas. No entanto, se observar o impacto real da alocação deste não é por isso, boa: a perda de qualquer nó afeta uma carga de trabalho específica disproportionately, porque demora enviados todos os respetivos primaries. Por exemplo, se o primeiro nó falhar as três primaries para as três partições diferentes do serviço círculo todos os será perdido. Por outro lado, os serviços triângulo e Hexagon têm as respetivas partições perder uma réplica. Isto faz com que sem interrupção, além de ter que recuperar a réplica baixo.

No exemplo na parte inferior, o Gestor de recursos do Cluster tem distribuídas as réplicas com base no equilíbrio global e por serviço. Quando se calcular a classificação da solução proporciona maior parte da ponderação para a solução global e uma parte (configurável) para serviços individuais. Saldo global para uma métrica é calculado com base em média das ponderações métricas de cada serviço. Cada serviço é com balanceamento de acordo com as suas próprias ponderações métricas definidas. Isto garante que os serviços são equilibrados dentro de si próprios, de acordo com as suas próprias necessidades. Como resultado, se o mesmo primeiro nó falhar a falha é distribuído por todas as partições de todos os serviços. O impacto para cada um é o mesmo.

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre como configurar serviços](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definir métricas de desfragmentação é uma forma para consolidar a carga em nós em vez de propagando-se este. Para saber como configurar a desfragmentação, consulte [neste artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Para obter informações sobre a forma como o Gestor de recursos de Cluster gere e equilibra a carga no cluster, consulte o artigo em [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- Iniciar a partir do início e [obtenha uma introdução para o serviço de recursos de infraestrutura Cluster Gestor de recursos](service-fabric-cluster-resource-manager-introduction.md)
- O custo de movimento é uma forma de sinalização o Gestor de recursos do Cluster que são mais dispendiosos de mover que outros determinados serviços. Para saber mais sobre o custo de movimento, consulte [neste artigo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

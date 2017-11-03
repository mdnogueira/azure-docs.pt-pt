---
title: "Gestor de recursos de Cluster de recursos de infraestrutura de serviço - grupos de aplicações | Microsoft Docs"
description: "Descrição geral da funcionalidade de grupo de aplicações no serviço de recursos de infraestrutura Cluster Gestor de recursos"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3212631ede7125bd849c2d9ba86ba2a0747d69ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-application-groups"></a>Introdução aos grupos de aplicações
Gestor de recursos de Cluster do Service Fabric gere, normalmente, os recursos do cluster, propagando-se a carga (representado através de [métricas](service-fabric-cluster-resource-manager-metrics.md)) uniformemente em todo o cluster. O Service Fabric gere a capacidade de nós no cluster e o cluster como um todo através de [capacidade](service-fabric-cluster-resource-manager-cluster-description.md). Métricas e a capacidade de trabalham ótimo para muitas cargas de trabalho, mas os padrões que utilizam de instâncias da aplicação do serviço de recursos de infraestrutura diferentes, por vezes, colocar em requisitos adicionais. Por exemplo, poderá pretender:

- Alguns capacidade em nós no cluster para os serviços dentro de alguns instância da aplicação com o nome de reserva
- Limitar o número total de nós com os serviços de dentro de uma instância da aplicação com o nome de no (em vez de propagando-se-los ao longo de todo o cluster)
- Definir as capacidades da instância da aplicação com o nome próprio para limitar o número de serviços ou consumo total de recursos dos serviços de no interior-lo

Para cumprir estes requisitos, o Gestor de recursos de Cluster do serviço de recursos de infraestrutura suporta uma funcionalidade denominada grupos de aplicações.

## <a name="limiting-the-maximum-number-of-nodes"></a>Limitar o número máximo de nós
O caso de utilização mais simples para a capacidade de aplicação é quando uma instância de aplicação tem de estar limitado a um determinado número máximo de nós. Isto consolida todos os serviços dentro dessa instância da aplicação para um número de conjunto de máquinas. Consolidação é útil quando está a tentar prever ou extremidade recurso físico utilização pelos serviços dentro dessa instância com nome de aplicação. 

A imagem seguinte mostra uma instância de aplicação com e sem um número máximo de nós definida:

<center>
![Instância da aplicação de definir o número máximo de nós][Image1]
</center>

No exemplo à esquerda, a aplicação não tem um número máximo de nós definido e tem três serviços. O Gestor de recursos do Cluster tem distribuir todas as réplicas em seis nós disponíveis para alcançar o equilíbrio melhor no cluster (o comportamento predefinido). No exemplo correto, vemos a mesma aplicação limitada a três nós.

O parâmetro que controla este comportamento é denominado MaximumNodes. Este parâmetro pode ser definido durante a criação da aplicação ou atualizado para uma instância de aplicação que já estava a ser executado.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

Dentro do conjunto de nós, o Gestor de recursos de Cluster não garantir que os objetos de serviço são colocados em conjunto ou quais os nós obterem utilizados.

## <a name="application-metrics-load-and-capacity"></a>Métricas de aplicação, a carga e a capacidade
Grupos de aplicações também permitem-lhe definir métricas associadas uma instância de aplicação com o nome e a capacidade dessa instância de aplicação para as métricas. Métricas de aplicações permitem-lhe controlar, reservar e limitar o consumo de recursos dos serviços dentro dessa instância da aplicação.

Para cada métrica da aplicação, existem dois valores que podem ser definidos:

- **Total de capacidade de aplicação** – esta definição representa a capacidade total da aplicação para uma métrica específica. O Gestor de recursos de Cluster não permite a criação de quaisquer novos serviços dentro desta instância de aplicação que faria com que a carga total exceder este valor. Por exemplo, vamos supor que a instância da aplicação tivesse uma capacidade de 10 e já tinha carga cinco. Será permitida a criação de um serviço com uma carga total predefinido de 10.
- **Capacidade máxima do nó** – esta definição especifica a carga total máxima para a aplicação num único nó. Se a carga passa por esta capacidade, o Gestor de recursos de Cluster move réplicas para outros nós, para que a carga diminui.


PowerShell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Reservar capacidade
É outra utilização comum para grupos de aplicações para se certificar de que os recursos do cluster estão reservados para uma instância de aplicação específica. O espaço é sempre reservado quando é criada a instância da aplicação.

Reservar espaço no cluster para a aplicação imediatamente ocorre mesmo quando:
- a instância da aplicação é criada, mas ainda não tem quaisquer serviços dentro do mesmo
- o número de serviços a instância da aplicação sempre que é alterado 
- os serviços existem mas não estão a consumir os recursos 

Reservar os recursos para uma instância de aplicação requer a especificação de dois parâmetros adicionais: *MinimumNodes* e *NodeReservationCapacity*

- **MinimumNodes** -define o número mínimo de nós que deve executar a instância da aplicação no.  
- **NodeReservationCapacity** -esta definição é por métrica para a aplicação. O valor é a quantidade desse métrica reservada para a aplicação em qualquer nó onde que executam os serviços nessa aplicação.

Combinar **MinimumNodes** e **NodeReservationCapacity** garante uma reserva de carga mínima para a aplicação no cluster. Se existir tem menos capacidade restante do cluster que a reserva total necessário, a criação da aplicação falha. 

Vamos ver um exemplo de reserva de capacidade:

<center>
![Definir a capacidade reservada de instâncias de aplicações][Image2]
</center>

No exemplo à esquerda, aplicações não tem qualquer capacidade de aplicações definidos. O Gestor de recursos de Cluster equilibra tudo, de acordo com as regras de normais.

No exemplo à direita, vamos supor que o se Application1 foi criado com as seguintes definições:

- MinimumNodes definido como dois
- Uma aplicação métrica definida com
  - NodeReservationCapacity de 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric reserva capacidade em dois nós para Application1 e não permitir que os serviços de Application2 consumir essa capacidade, mesmo se não existirem que nenhuma carga consumida pelos serviços de no interior Application1 no momento. Esta capacidade de aplicação reservado é considerada consumido e é contabilizada na capacidade restantes nesse nó e no cluster.  A reserva é deducted partir da capacidade de cluster restante imediatamente, no entanto, o consumo de reservado é deducted a partir da capacidade de um nó específico apenas quando o objeto, pelo menos, um serviço é colocado na mesma. Esta reserva posterior permite flexibilidade e uma melhor utilização de recursos, uma vez que os recursos estão reservados apenas em nós quando necessário.

## <a name="obtaining-the-application-load-information"></a>Obter as informações de carga da aplicação
Para cada aplicação que tem uma capacidade de aplicações definidos para uma ou mais métricas, que pode obter informações sobre a carga de agregação comunicada pelo réplicas dos respetivos serviços.

PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

A consulta de ApplicationLoad devolve as informações básicas sobre a capacidade de aplicação que foi especificada para a aplicação. Estas informações incluem as informações de nós de mínimo e máximo de nós e o número de que a aplicação está atualmente occupying. Também inclui informações sobre cada métrica de carga da aplicação, incluindo:

* O nome da métrica: Nome da métrica.
* Capacidade de reserva: Capacidade de Cluster que está reservada no cluster para esta aplicação.
* Carga de aplicação: A carga Total de réplicas de subordinados desta aplicação.
* Capacidade de aplicação: Máximo permitido de valor de carga da aplicação.

## <a name="removing-application-capacity"></a>Remover a capacidade de aplicação
Assim que os parâmetros de capacidade de aplicação estão definidos para uma aplicação, podem ser removidos utilizando os cmdlets do PowerShell ou APIs de aplicação de atualização. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando remove todos os parâmetros de gestão de capacidade de aplicação a instância da aplicação. Isto inclui MinimumNodes, MaximumNodes e métricas da aplicação, se aplicável. O efeito do comando é imediato. Depois de concluída este comando, o Gestor de recursos de Cluster utiliza o comportamento predefinido para a gestão de aplicações. Parâmetros de capacidade de aplicação podem ser especificados novamente através de `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Restrições na capacidade de aplicação
Existem várias restrições nos parâmetros de capacidade de aplicação que tem de ser respeitados. Se não existirem erros de validação sem alterações ocorrer.

- Todos os parâmetros de número inteiro tem de ser não negativo números.
- MinimumNodes nunca deve ser superior a MaximumNodes.
- Se estiverem definidas as capacidades para uma métrica de carga, em seguida, têm de seguir estas regras:
  - Capacidade de reserva de nós não deve ser superior a capacidade máxima do nó. Por exemplo, não é possível limitar a capacidade para a métrica "CPU" no nó para duas unidades e tentar reservar três unidades em cada nó.
  - Se não for especificado MaximumNodes, em seguida, o produto de MaximumNodes e a capacidade máxima do nó não pode conter superior à capacidade Total de aplicação. Por exemplo, digamos a capacidade máxima do nó para a métrica de carga "CPU" está definido para oito. Também Digamos que definiu o máximo de nós para 10. Neste caso, a capacidade Total do aplicação tem de ser superior a 80 para esta métrica de carga.

As restrições são impostas ambos durante a criação de aplicações e atualizações.

## <a name="how-not-to-use-application-capacity"></a>Como não utilizar a capacidade de aplicação
- Não tente utilizar as funcionalidades de grupo de aplicações para restringir a aplicação para um _específico_ subconjunto de nós. Por outras palavras, pode especificar que a aplicação é executada no máximo, cinco nós, mas não os específicos cinco nós do cluster. Constraining uma aplicação específicas nós pode ser conseguido com restrições de posicionamento para os serviços.
- Não tente utilizar a capacidade de aplicação para se certificar de que os dois serviços da aplicação do mesmo são colocados em nós do mesmos. Em vez disso, utilize [afinidade](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) ou [restrições de posicionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Para obter informações sobre a forma como o Gestor de recursos de Cluster gere e equilibra a carga no cluster, consulte o artigo em [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- Iniciar a partir do início e [obtenha uma introdução para o serviço de recursos de infraestrutura Cluster Gestor de recursos](service-fabric-cluster-resource-manager-introduction.md)
- Para mais informações sobre como métricas funcionam normalmente, ler sobre [métricas de carga do Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para saber mais sobre os mesmos, consulte este artigo no [que descrevem um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

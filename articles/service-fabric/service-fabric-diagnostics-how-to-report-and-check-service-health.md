---
title: "Relatório e verificar o estado de funcionamento com o Azure Service Fabric | Microsoft Docs"
description: "Saiba como enviar relatórios de estado de funcionamento de código do serviço e como verificar o estado de funcionamento do seu serviço utilizando as ferramentas de monitorização de estado de funcionamento do Azure Service Fabric fornece."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: 
ms.assetid: 7c712c22-d333-44bc-b837-d0b3603d9da8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/2/2017
ms.author: dekapur
ms.openlocfilehash: a8c1ac57d38ae504e677c44c6fec08164b9b74ce
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="report-and-check-service-health"></a>Comunicar e verificar o estado de funcionamento dos serviços
Quando os serviços de encontrarem problemas, a capacidade de responder a e corrigir incidentes e falhas depende da sua capacidade para detetar os problemas rapidamente. Se comunicar problemas e falhas para o Gestor de estado de funcionamento do Service Fabric do Azure a partir do seu código de serviço, pode utilizar o estado de funcionamento padrão ferramentas que o Service Fabric fornece para verificar o estado de funcionamento de monitorização.

Existem três formas que pode comunicar o estado de funcionamento do serviço:

* Utilize [partição](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) ou [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objetos.  
  Pode utilizar o `Partition` e `CodePackageActivationContext` objetos para comunicar o estado de funcionamento dos elementos que fazem parte do contexto atual. Por exemplo, o código que é executado como parte de uma réplica pode comunicar o estado de funcionamento apenas essa réplica, a partição que pertence a e a aplicação que é uma parte.
* Utilize `FabricClient`.   
  Pode utilizar `FabricClient` para Estado de funcionamento do relatório do código do serviço se o cluster não for [segura](service-fabric-cluster-security.md) ou se o serviço está em execução com privilégios de administrador. A maioria dos cenários no mundo real não utilizar clusters protegidas, ou forneça privilégios de administrador. Com `FabricClient`, pode comunicar o estado de funcionamento em qualquer entidade que faz parte do cluster. Idealmente, no entanto, código do serviço deve apenas enviar relatórios que estão relacionados com a sua própria estado de funcionamento.
* Utilize as APIs REST no cluster, aplicação, aplicação implementada, serviço, o pacote de serviço, partição, réplica ou níveis de nó. Isto pode ser utilizado para comunicar o estado de funcionamento num contentor.

Este artigo explica como um exemplo que comunica o estado de funcionamento do código do serviço. O exemplo mostra também como as ferramentas fornecidas pelo serviço de recursos de infraestrutura podem ser utilizadas para verificar o estado de funcionamento. Este artigo destina-se para uma introdução rápida para o estado de funcionamento capacidades dos recursos de infraestrutura do serviço de monitorização. Para obter informações mais detalhadas, pode ler a série de artigos aprofundadas sobre o estado de funcionamento que começam com a ligação no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte instalado:

* Visual Studio 2015 ou Visual Studio 2017
* SDK do Service Fabric

## <a name="to-create-a-local-secure-dev-cluster"></a>Para criar um cluster de desenvolvimento seguro local
* Abra o PowerShell com privilégios de administrador e execute os seguintes comandos:

![Comandos que mostram como criar um cluster de desenvolvimento seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Para implementar uma aplicação e verificar o estado de funcionamento
1. Abra o Visual Studio como administrador.
2. Criar um projeto com o **serviço de monitorização de estado** modelo.
   
    ![Criar uma aplicação de Service Fabric com o serviço de monitorização de estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
3. Prima **F5** para executar a aplicação no modo de depuração. A aplicação for implementada para o cluster local.
4. Depois da aplicação é executada, faça duplo clique no ícone na área de notificação de Gestor de clusters locais e selecione **gerir Cluster Local** no menu de atalho para abrir o Explorador de recursos de infraestrutura de serviço.
   
    ![Abra o Explorador de recursos de infraestrutura de serviço da área de notificação](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
5. O estado de funcionamento da aplicação deverá ser apresentado como esta imagem. Neste momento, a aplicação deve estar em bom estado sem erros.
   
    ![Aplicação bom estado de funcionamento no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
6. Também pode verificar o estado de funcionamento com o PowerShell. Pode utilizar ```Get-ServiceFabricApplicationHealth``` para verificar o estado de funcionamento de uma aplicação e pode utilizar ```Get-ServiceFabricServiceHealth``` para verificar o estado de funcionamento de um serviço. O relatório de estado de funcionamento para a mesma aplicação no PowerShell está nesta imagem.
   
    ![Aplicação bom estado de funcionamento no PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Para adicionar os eventos de estado de funcionamento personalizado ao código do serviço
Os modelos de projeto de Service Fabric no Visual Studio contém o código de exemplo. Os passos seguintes mostram como pode comunicar eventos de estado de funcionamento personalizado a partir do seu código de serviço. Esses relatórios apareçam automaticamente nas ferramentas do padrão para monitorização de estado de funcionamento que o Service Fabric fornece, como o Service Fabric Explorer, vista de estado de funcionamento de portal do Azure e PowerShell.

1. Reabra a aplicação que criou anteriormente no Visual Studio ou criar uma nova aplicação utilizando o **serviço de monitorização de estado** modelo do Visual Studio.
2. Abra o ficheiro Stateful1.cs e localize o `myDictionary.TryGetValueAsync` chamada o `RunAsync` método. Pode ver que este método devolve um `result` que contém o valor atual do contador de porque é a chave lógica desta aplicação para manter uma contagem em execução. Se isto tiverem uma aplicação real, e se a falta de resultado representado uma falha, iria querer sinalizador esse evento.
3. Para reportar um evento de estado de funcionamento quando a falta de resultado representa uma falha, adicione os seguintes passos.
   
    a. Adicionar o `System.Fabric.Health` espaço de nomes para o ficheiro Stateful1.cs.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Adicione o seguinte código após o `myDictionary.TryGetValueAsync` chamar
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Elaboramos relatórios de estado de funcionamento de réplica porque está a ser reportado num serviço de monitorização de estado. O `HealthInformation` parâmetro armazena informações sobre o problema de estado de funcionamento está a ser reportado.
   
    Se criou um serviço sem monitorização de estado, utilize o seguinte código
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
4. Se o serviço está em execução com privilégios de administrador ou se o cluster não for [segura](service-fabric-cluster-security.md), também pode utilizar `FabricClient` para Estado de funcionamento do relatório conforme mostrado nos passos seguintes.  
   
    a. Criar o `FabricClient` instância após o `var myDictionary` declaração.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Adicione o seguinte código após o `myDictionary.TryGetValueAsync` chamada.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
5. Vamos simular desta falha e veja-apareçam nas ferramentas de monitorização do Estado de funcionamento. Para simular a falha, comente a primeira linha com o código de relatórios do Estado de funcionamento que adicionou anteriormente. Depois de comente a primeira linha, o código terá um aspeto semelhante ao seguinte exemplo.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Este código desencadeado o relatório de estado de funcionamento de cada vez `RunAsync` executa. Depois de efetuar a alteração, prima **F5** para executar a aplicação.
6. Depois da aplicação é executada, abra o Explorador de recursos de infraestrutura de serviço para verificar o estado de funcionamento da aplicação. Neste momento, Service Fabric Explorer mostra que a aplicação está danificada. Trata-se devido ao erro que foi comunicado do código que adicionamos anteriormente.
   
    ![Aplicação mau estado de funcionamento no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
7. Se selecionar a réplica primária na vista de árvore do Service Fabric Explorer, verá que **estado de funcionamento** indica um erro, demasiado. Service Fabric Explorer também apresenta os detalhes do relatório de estado de funcionamento que foram adicionados para o `HealthInformation` parâmetro no código. Pode ver os relatórios de estado de funcionamento mesmo no PowerShell e o portal do Azure.
   
    ![Estado de funcionamento de réplica no Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Este relatório permanece no Gestor de estado de funcionamento até é substituído por outro relatório ou até que esta réplica foi eliminada. Porque não foi possível definir não `TimeToLive` para este relatório de estado de funcionamento no `HealthInformation` objeto, o relatório nunca expira.

Recomendamos que o estado de funcionamento deve ser comunicado no nível mais granular, que neste caso, é a réplica. Também pode comunicar o estado de funcionamento no `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Para o estado de funcionamento de relatório em `Application`, `DeployedApplication`, e `DeployedServicePackage`, utilize `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Passos seguintes
* [Descrição profunda sobre o estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
* [API REST para os relatórios do Estado de funcionamento do serviço](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [API REST para os relatórios do Estado de funcionamento da aplicação](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)


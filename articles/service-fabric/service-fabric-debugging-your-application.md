---
title: "Depurar a aplicação no Visual Studio | Microsoft Docs"
description: "Melhorar a fiabilidade e desempenho dos seus serviços, desenvolver e a depurá-los no Visual Studio num cluster de desenvolvimento local."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: vturecek;mikhegn
ms.openlocfilehash: 8c29b54415a42ec435f2b4c3ce6b407b2155f70c
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar a aplicação de Service Fabric através do Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Depurar uma aplicação de Service Fabric local
Pode poupar tempo e dinheiro ao implementar e depurar a aplicação de Service Fabric do Azure num cluster de desenvolvimento do computador local. Visual Studio 2017 ou Visual Studio 2015, pode implementar a aplicação ao local cluster e ligar automaticamente o depurador para todas as instâncias da aplicação.

1. Iniciar um cluster de desenvolvimento local, seguindo os passos no [como configurar o ambiente de desenvolvimento do Service Fabric](service-fabric-get-started.md).
2. Prima **F5** ou clique em **depurar** > **iniciar depuração**.
   
    ![Iniciar a depuração de uma aplicação][startdebugging]
3. Configurar pontos de interrupção no seu código e passo através da aplicação, clicando em comandos no **depurar** menu.
   
   > [!NOTE]
   > Visual Studio anexa a todas as instâncias da aplicação. Enquanto estiver a avance através de código, os pontos de interrupção podem obter atingir por vários processos, resultando em sessões simultâneas. Experimente desativar os pontos de interrupção depois que está a acessos, ao tornar cada ponto de interrupção condicional o ID de thread ou utilizando os eventos de diagnóstico.
   > 
   > 
4. O **eventos de diagnóstico** janela será aberta automaticamente para que possa vê eventos de diagnóstico em tempo real.
   
    ![Ver eventos de diagnóstico em tempo real][diagnosticevents]
5. Pode também abrir o **eventos de diagnóstico** janela no Cloud Explorer.  Em **Service Fabric**, faça duplo clique de qualquer nó e escolha **rastreios de transmissão em fluxo vista**.
   
    ![Abra a janela de eventos de diagnóstico][viewdiagnosticevents]
   
    Se pretender filtrar os rastreios para um serviço específico ou aplicação, basta ative rastreios de transmissão em fluxo nesse serviço específico ou aplicação.
6. Os eventos de diagnóstico que podem ser vistos na gerado automaticamente **ServiceEventSource.cs** do ficheiro e são chamados a partir do código da aplicação.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. O **eventos de diagnóstico** janela suporta filtragem, colocar em pausa e inspecionar eventos em tempo real.  O filtro é uma pesquisa de cadeia simples da mensagem de evento, incluindo o respetivo conteúdo.
   
    ![Filtrar, colocar em pausa e retomar ou Inspecione os eventos em tempo real][diagnosticeventsactions]
8. A depuração de serviços é como qualquer outra aplicação de depuração. Normalmente, irá configurar pontos de interrupção através do Visual Studio para depuração fácil. Apesar de coleções fiável replicar em vários nós, ainda implementar IEnumerable. Isto significa que pode utilizar a vista de resultados no Visual Studio durante a depuração para ver que tenha armazenado no interior. Basta defina um ponto de interrupção em qualquer lugar no seu código.
   
    ![Iniciar a depuração de uma aplicação][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar uma aplicação de Service Fabric remota
Se estiver a executar as aplicações de Service Fabric num cluster de Service Fabric no Azure, conseguir remotamente depurar estes, diretamente a partir do Visual Studio.

> [!NOTE]
> A funcionalidade requer [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Depuração remota destina-para cenários de desenvolvimento/teste e não deve ser utilizada em ambientes de produção, devido ao impacto nas aplicações em execução.
> 
> 

1. Navegue para o cluster **Cloud Explorer**, clique com botão direito e escolha **ativar a depuração**
   
    ![Ativar depuração remota][enableremotedebugging]
   
    Isto irá iniciar o processo para ativar a extensão de depuração remota em nós de cluster, bem como as configurações de rede necessários.
2. Clique com o botão direito no nó de cluster na **Cloud Explorer**e escolha **anexar depurador**
   
    ![Anexar o depurador][attachdebugger]
3. No **anexar ao processo** caixa de diálogo, escolha o processo que pretende depurar e clique em **anexar**
   
    ![Escolha o processo][chooseprocess]
   
    O nome do processo que pretende anexar, igual ao nome do seu nome de assemblagem do projeto de serviço.
   
    O depurador irá anexar a todos os nós que executa o processo.
   
   * No caso em que está a depurar um serviço sem monitorização de estado, todas as instâncias do serviço em todos os nós fazem parte da sessão de depuração.
   * Se está a depurar um serviço com estado, a réplica primária de qualquer partição será Active Directory e, por conseguinte, atualizada pelo depurador. Se a réplica primária move durante a sessão de depuração, o processamento dessa réplica continuarão a estar parte da sessão de depuração.
   * Para apenas catch relevantes partições ou instâncias de um determinado serviço, pode utilizar pontos de interrupção condicionais a interrupção apenas uma partição específica ou instância.
     
     ![Ponto de interrupção condicional][conditionalbreakpoint]
     
     > [!NOTE]
     > Atualmente não suportamos a depuração de um cluster do Service Fabric com várias instâncias do mesmo nome executável do serviço.
     > 
     > 
4. Depois de concluir a depurar a aplicação, pode desativar a extensão de depuração remota clicando com o cluster no **Cloud Explorer** e escolha **desativar a depuração**
   
    ![Desativar a depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Transmissão em fluxo rastreios a partir de um nó de cluster remoto
Pode também conseguem rastreios de fluxo diretamente a partir de um nó de cluster remoto com o Visual Studio. Esta funcionalidade permite-lhe para eventos de rastreio ETW de fluxo, produzidos num nó de cluster do Service Fabric.

> [!NOTE]
> Esta funcionalidade requer [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).
> Esta funcionalidade só suporta clusters em execução no Azure.
> 
> 

<!-- -->
> [!WARNING]
> Os rastreios de transmissão em fluxo destina-para cenários de desenvolvimento/teste e não deve ser utilizada em ambientes de produção, devido ao impacto nas aplicações em execução.
> Num cenário de produção, deverá confiar no reencaminhamento de eventos utilizando o diagnóstico do Azure.
> 
> 

1. Navegue para o cluster **Cloud Explorer**, clique com botão direito e escolha **ativar rastreios de transmissão em fluxo**
   
    ![Ativar remotos rastreios de transmissão em fluxo][enablestreamingtraces]
   
    Isto irá iniciar o processo para ativar a extensão de rastreios de transmissão em fluxo em nós de cluster, bem como as configurações de rede necessários.
2. Expanda o **nós** elemento **Cloud Explorer**, faça duplo clique no nó que pretende rastreios a partir de fluxo e escolha **rastreios de transmissão em fluxo de vista**
   
    ![Vista remoto rastreios de transmissão em fluxo][viewremotestreamingtraces]
   
    Repita o passo 2 para nós tantos como pretende ver os rastreios de. Cada sequência de nós irá aparecer numa janela dedicada.
   
    Agora é capaz de ver os rastreios emitidos pelo Service Fabric e os serviços. Se pretender filtrar os eventos para mostrar apenas uma aplicação específica, basta escreva o nome da aplicação no filtro.
   
    ![Ver os rastreios de transmissão em fluxo][viewingstreamingtraces]
3. Quando tiver terminado rastreios de transmissão em fluxo do seu cluster, pode desativar remotos rastreios de transmissão em fluxo, clicando com o cluster no **Cloud Explorer** e escolha **desativar rastreios de transmissão em fluxo**
   
    ![Desativar remotos rastreios de transmissão em fluxo][disablestreamingtraces]

## <a name="next-steps"></a>Passos seguintes
* [Testar um serviço do Service Fabric](service-fabric-testability-overview.md).
* [Gerir as aplicações de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png

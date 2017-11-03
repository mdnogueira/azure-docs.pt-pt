---
title: "Depurar micro-serviços do Azure no Windows | Microsoft Docs"
description: "Saiba como monitorizar e diagnosticar os serviços escritos utilizando o Microsoft Azure Service Fabric numa máquina de desenvolvimento local."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 034bfe6fbab3da083d6c63f99f960467fd0a84e9
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorizar e diagnosticar os serviços de uma configuração de desenvolvimento do computador local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitorização, detetar, diagnosticar e resolver problemas permitem de serviços continuar com a mínima interrupção a experiência de utilizador. Enquanto a monitorização e diagnóstico é crítico num ambiente de produção implementado real, a eficiência irá depender de um modelo semelhante a adotar durante o desenvolvimento de serviços e certifique-se de que funcionam quando mover para um programa de configuração do mundo real. Service Fabric torna mais fácil para os programadores de serviço implementar o diagnóstico de forma totalmente integrada pode trabalhar em setups única máquina de desenvolvimento local e setups de cluster de produção do mundo real.

## <a name="event-tracing-for-windows"></a>Rastreio de eventos para o Windows
[O rastreio de eventos do Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) é a tecnologia recomendada para mensagens de rastreio no Service Fabric. Alguns benefícios da utilização ETW são:

* **ETW é rápida.** Foi criada como uma tecnologia de rastreio que tem um impacto mínimo no código os tempos de execução.
* **O rastreio ETW funciona na perfeição entre ambientes de desenvolvimento local e também setups de cluster do mundo real.** Isto significa que não tem de reescrever o código de rastreio quando estiver pronto para implementar o código para um cluster real.
* **Código de sistema do Service Fabric utiliza também ETW para o rastreio interno.** Isto permite-lhe ver os rastreios de aplicação interleaved com rastreios de sistema do Service Fabric. Também ajuda-o a compreender mais facilmente as interrelationships entre o seu código da aplicação e eventos e sequências de no sistema subjacente.
* **Não há suporte incorporado nas ferramentas do Service Fabric Visual Studio para ver eventos ETW.** Os eventos ETW são apresentados na vista de eventos de diagnóstico do Visual Studio, depois do Visual Studio está corretamente configurada com o Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Ver eventos de sistema do Service Fabric no Visual Studio
Service Fabric emite Eventos ETW para o ajudar a compreender o que acontece na plataforma de programadores de aplicações. Se ainda não o fez, avançar e seguir os passos [criar a sua primeira aplicação no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). Estas informações irão ajudá-lo uma aplicação em execução com o Visualizador de eventos de diagnóstico que mostra as mensagens de rastreio.

1. Se o diagnóstico janela eventos automaticamente não for apresentada, vá para o **vista** separador no Visual Studio, escolha **outras janelas** e, em seguida, **Visualizador de eventos de diagnóstico**.
2. Cada evento tem informações de metadados padrão que indica o nó, a aplicação e o serviço, que o evento é proveniente de. Também pode filtrar a lista de eventos utilizando o **filtrar eventos** caixa na parte superior da janela eventos. Por exemplo, pode filtrar por **nome de nó** ou **nome do serviço.** E quando está a observar os detalhes do evento, pode também colocar em pausa utilizando a **colocar em pausa** botão na parte superior da janela eventos e retomar mais tarde sem qualquer perda de eventos.
   
   ![Visualizador de eventos de diagnóstico do Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Adicionar os seus próprios rastreios personalizados ao código da aplicação
Os modelos de projeto do Visual Studio do serviço Fabric contém o código de exemplo. O código mostra como adicionar aplicação personalizada código ETW rastreios que apareçam no Visualizador de ETW do Visual Studio, juntamente com os rastreios de sistema do Service Fabric. A vantagem deste método é que metadados são adicionados automaticamente rastreios e o Visual Studio diagnóstico Visualizador de eventos já está configurado para as mostrar.

Para projetos criados a partir de **modelos de serviço** (sem monitorização de estado ou com monitorização de estado) apenas procura o `RunAsync` implementação:

1. A chamada para `ServiceEventSource.Current.ServiceMessage` no `RunAsync` método mostra um exemplo de um rastreio ETW personalizada do código da aplicação.
2. No **ServiceEventSource.cs** ficheiro, encontrará uma sobrecarga para o `ServiceEventSource.ServiceMessage` método que deve ser utilizado para eventos de alta frequência por motivos de desempenho.

Para projetos criados a partir de **modelos de ator** (sem monitorização de estado ou com monitorização de estado):

1. Abra o **"ProjectName" CS** ficheiro onde *ProjectName* é o nome que escolheu para o seu projeto de Visual Studio.  
2. Encontrar o código `ActorEventSource.Current.ActorMessage(this, "Doing Work");` no *DoWorkAsync* método.  Este é um exemplo de um rastreio ETW personalizada escrito a partir do código da aplicação.  
3. No ficheiro **ActorEventSource.cs**, encontrará uma sobrecarga para o `ActorEventSource.ActorMessage` método que deve ser utilizado para eventos de alta frequência por motivos de desempenho.

Depois de adicionar o rastreio ETW personalizado para o código do serviço, pode criar, implementar e executar a aplicação novamente para ver os eventos no Visualizador de eventos de diagnóstico. Se a depurar a aplicação com **F5**, o Visualizador de eventos de diagnóstico será aberta automaticamente.

## <a name="next-steps"></a>Passos seguintes
O mesmo código de rastreio que adicionou à sua aplicação acima para obter um diagnóstico local irá funcionar com as ferramentas que pode utilizar para ver estes eventos quando executar a aplicação num cluster do Azure. Consulte estes artigos que abordam as diferentes opções para as ferramentas e descrevem como pode configurá-los.

* [Como recolher registos de diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Agregação de eventos e coleção utilizando EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)


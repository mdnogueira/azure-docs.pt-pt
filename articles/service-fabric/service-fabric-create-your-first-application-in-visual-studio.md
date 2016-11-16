---
title: "Criação da sua primeira aplicação de Service Fabric no Visual Studio | Microsoft Docs"
description: "Criar, implementar e depurar uma aplicação de Service Fabric com o Visual Studio"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 037dc010a6dc60eb49ad4fdad2861e8653e36199


---
# <a name="create-your-first-azure-service-fabric-application"></a>Criar a sua primeira aplicação do Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

O SDK do Service Fabric inclui um suplemento para Visual Studio que fornece modelos e ferramentas para criar, implementar e depurar aplicações de Service Fabric. Este tópico guia-o no processo de criação da sua primeira aplicação no Visual Studio.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que [configurou o seu ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="video-walkthrough"></a>Instruções de vídeo
O vídeo a seguir guia-o pelos passos neste tutorial:

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio/player]
> 
> 

## <a name="create-the-application"></a>Criar a aplicação
Uma aplicação de Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. Com o assistente de Novo Projeto, pode criar um projeto de aplicação, juntamente com o seu primeiro projeto de serviço. Pode adicionar mais serviços posteriormente.

1. Inicie o Visual Studio como administrador.
2. Clique em **Ficheiro > Novo Projeto > Nuvem > Aplicação de Service Fabric**.
3. Atribua um nome à aplicação e clique em **OK**.
   
    ![Caixa de diálogo de novo projeto no Visual Studio][1]
4. Na página seguinte, selecione **Com Estado** como o primeiro tipo de serviço a incluir na sua aplicação. Atribua-lhe um nome e clique em **OK**.
   
    ![Caixa de diálogo novo serviço no Visual Studio][2]
   
   > [!NOTE]
   > Para obter mais informações sobre as opções, veja [Service Fabric programming model overview (Descrição geral do modelo de programação Service Fabric)](service-fabric-choose-framework.md).
   > 
   > 
   
    O Visual Studio cria o projeto de aplicação e o projeto de serviço com estado e apresenta-os no Explorador de Soluções.
   
    ![Explorador de Soluções a seguir a criação da aplicação com o serviço com estado][3]
   
    O projeto de aplicação não contém qualquer código diretamente. Em vez disso, referencia um conjunto de projetos de serviço. Além disso, contém outros três tipos de conteúdo:
   
   * **Perfis de publicação**: utiliza-se para gerir as preferências de ferramentas para os diferentes ambientes.
   * **Scripts**: inclui um script de PowerShell para implementar/atualizar a sua aplicação. O Visual Studio utiliza o script em segundo plano do Visual Studio. O script também pode ser invocado diretamente na linha de comandos.
   * **Definição da aplicação**: inclui o manifesto da aplicação em *ApplicationPackageRoot*. Os ficheiros de parâmetro de aplicação associados em *ApplicationParameters*, definem a aplicação e permitem configurá-la especificamente para um determinado ambiente.
     
     Para obter uma descrição geral do conteúdo do projeto de serviço, consulte o artigo [Introdução a Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Implemente a aplicação e depure-a
Agora que tem uma aplicação, tente executá-la.

1. Prima F5 no Visual Studio para implementar a aplicação, com o fim de depurá-la.
   
   > [!NOTE]
   > A primeira implementação demorará algum tempo, dado que o Visual Studio está a criar um cluster local para o desenvolvimento. Um cluster local executa o mesmo código de plataforma que se compila num cluster com várias máquinas, mas numa única máquina. O estado de criação do cluster aparece na janela de saída do Visual Studio.
   > 
   > 
   
    Quando o cluster estiver pronto, recebe uma notificação da aplicação do gestor de tabuleiro de sistema do cluster local que está incluído no SDK.
   
    ![Notificação do tabuleiro de sistema do cluster local][4]
2. Uma vez iniciada a aplicação, o Visual Studio apresentará automaticamente o Visualizador de Eventos de Diagnóstico, onde pode ver os resultados de rastreio do serviço.
   
    ![Visualizador de eventos de diagnóstico][5]
   
    No caso do modelo de serviço com estado, as mensagens mostram simplesmente o valor do contador que se incrementa no método `RunAsync` de MyStatefulService.cs.
3. Expanda um dos eventos para ver mais detalhes, incluindo o nó em que se executa o código. Neste caso, é _Node_2, embora possa ser diferente no seu computador.
   
    ![Detalhe do visualizador de eventos de diagnóstico][6]
   
    O cluster local contém cinco nós que estão alojados num único computador. Imita um cluster de cinco nós, no qual os nós estão em máquinas distintas. Vamos desativar um dos nós no cluster local para simular a perda de uma máquina enquanto executamos o depurador do Visual Studio ao mesmo tempo.
   
   > [!NOTE]
   > Os eventos de diagnóstico de aplicações que são emitidos pelo modelo de projeto utilizam a classe `ServiceEventSource` incluída. Para obter mais informações, consulte o artigo [Como monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
   > 
   > 
4. Procure a classe no seu serviço de projeto que deriva de StatefulService (por exemplo, MyStatefulService) e defina um ponto de interrupção na primeira linha do método `RunAsync`.
   
    ![Método RunAsync com ponto de interrupção do serviço com estado ][7]
5. Clique com o botão direito na aplicação de tabuleiro de sistema do Gestor de Clusters Locais e selecione **Gerir Cluster Local** para iniciar o Service Fabric Explorer.
   
    ![Iniciar o Service Fabric Explorer a partir do Gestor de Clusters Locais][systray-launch-sfx]
   
    O Service Fabric Explorer proporciona uma representação visual de um cluster – incluindo o conjunto de aplicações aí implementadas e o conjunto de nós físicos que o constituem. Para saber mais acerca do Service Fabric Explorer, consulte o artigo [Visualizar o seu cluster](service-fabric-visualizing-your-cluster.md).
6. No painel da esquerda, expanda **Cluster > Nós** e localizar o nó que está a executar o seu código.
7. Clique em **Ações > Desativar (Reiniciar)** para simular um reinício do computador. (Tenha em atenção que também pode desativar a partir do menu de contexto na vista de lista de nós no painel esquerdo.)
   
    ![Parar um nó no Service Fabric Explorer][sfx-stop-node]
   
    Momentaneamente, deverá ver o ponto de interrupção atingido no Visual Studio, dado que a computação que estava a fazer diretamente num nó falha no outro.
8. Regresse ao Visualizador de Eventos de Diagnóstico e observe as mensagens. O contador não deixa de aumentar, apesar de os eventos, na verdade, estarem a vir por um nó diferente.
   
    ![Visualizador de eventos de diagnóstico após falha][diagnostic-events-viewer-detail-post-failover]

## <a name="switch-cluster-mode"></a>Alternar o modo do cluster
Por predefinição, o cluster de desenvolvimento local está configurado para ser executado como um cluster de 5 nós, o que é útil na depuração de serviços implementados em vários nós. No entanto, a implementação de uma aplicação no cluster de desenvolvimento de cinco nós pode demorar algum tempo. Se pretender iterar rapidamente alterações de código sem executar a aplicação nos cinco nós, pode alternar o cluster de desenvolvimento para o modo de 1 Nó. Para executar o código num cluster com um nó, clique com o botão direito do rato no Gestor de Clusters Locais na bandeja do sistema e selecione **Alternar Modo do Cluster -> 1 Nó**.  

![Alternar o modo do cluster][switch-cluster-mode]

Quando altera o modo do cluster, o cluster de desenvolvimento é reinicializado e todas as aplicações aprovisionadas ou em execução no cluster são removidas.

## <a name="cleaning-up"></a>Limpeza
  Antes de concluir, é importante lembrar-se de que o cluster local é real. Parar o depurador remove a instância da aplicação e anula o registo do tipo de aplicação. Contudo, o cluster continua a ser executado em segundo plano. Tem várias opções para gerir o cluster:

1. Para encerrar o cluster, mas manter os dados de aplicação e o rastreio, clique em **Parar Cluster Local** na aplicação de tabuleiro do sistema.
2. Para eliminar o cluster totalmente, clique em **Remover Cluster Local** na aplicação de tabuleiro do sistema. Esta opção resultará noutra implementação lenta da próxima vez que premir F5 no Visual Studio. Elimine o cluster apenas se não pretender utilizar o cluster local durante algum tempo ou se precisar de recuperar recursos.

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autónomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Experimente criar um serviço com os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como pode expor os seus serviços à Internet com um [front-end do serviço Web](service-fabric-add-a-web-frontend.md).
* Percorra um [laboratório prático](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) e crie um serviço sem monitorização de estado, configure relatórios de monitorização e estado de funcionamento e faça uma atualização da aplicação.

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png



<!--HONumber=Nov16_HO2-->



---
title: Criar um Reliable Services do Azure Service Fabric com C#
description: "Crie, implemente e depure uma aplicação Reliable Service compilada no Azure Service Fabric com o Visual Studio."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2017
ms.author: ryanwi
ms.openlocfilehash: a4bada3c099e348e2aa188fe08a67ef170ad50ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Criar a sua primeira aplicação Reliable Services sem monitorização de estado do Service Fabric em C#

Saiba como implementar a sua primeira aplicação do Service Fabric para .NET no Windows em poucos minutos. Quando tiver terminado, terá um cluster local em execução com uma aplicação do Reliable Services.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que [configurou o seu ambiente de desenvolvimento](service-fabric-get-started.md). Esta configuração inclui instalar o Service Fabric SDK e o Visual Studio 2017 ou 2015.

## <a name="create-the-application"></a>Criar a aplicação

Inicie o Visual Studio como **administrador**.

Criar um projeto com `CTRL`+`SHIFT`+`N`

Na caixa de diálogo **Novo Projeto**, escolha **Cloud > Aplicação do Service Fabric**.

Dê o nome **MyApplication** à aplicação e prima **OK**.

   
![Caixa de diálogo de novo projeto no Visual Studio][1]

Pode criar qualquer tipo de aplicação do Service Fabric na caixa de diálogo seguinte. Neste Início Rápido, escolha **Serviço Com Estado** .

Dê o nome **MyStatefulService** ao serviço e prima **OK**.

![Caixa de diálogo novo serviço no Visual Studio][2]


O Visual Studio cria o projeto de aplicação e o projeto de serviço com estado e apresenta-os no Explorador de Soluções.

![Explorador de Soluções a seguir a criação da aplicação com o serviço com estado][3]

O projeto de aplicação (**MyApplication**) não contém nenhum código diretamente. Em vez disso, referencia um conjunto de projetos de serviço. Além disso, contém outros três tipos de conteúdo:

* **Perfis de publicação**  
Perfis para implementar em diferentes ambientes.

* **Scripts**  
Script do PowerShell para implementar/atualizar a sua aplicação.

* **Definição da aplicação**  
Inclui o ficheiro ApplicationManifest.xml em *ApplicationPackageRoot*, que descreve a composição da sua aplicação. Os ficheiros de parâmetros da aplicação associados estão localizados em *ApplicationParameters*, que podem ser utilizados para especificar parâmetros específicos para o ambiente. O Visual Studio seleciona um ficheiro de parâmetros de aplicação que é especificado no perfil de publicação associado durante a implementação num ambiente específico.
    
Para obter uma descrição geral do conteúdo do projeto de serviço, consulte o artigo [Introdução a Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Implemente a aplicação e depure-a

Agora que tem uma aplicação, execute-a.

No Visual Studio, prima `F5` para implementar a aplicação, com o fim de depurá-la.

>[!NOTE]
>Da primeira vez que executar e implementar a aplicação localmente, o Visual Studio cria um cluster local para depuração. Esta operação pode demorar algum tempo. O estado da criação do cluster aparece na janela de saída do Visual Studio.

Quando o cluster estiver pronto, recebe uma notificação da aplicação do gestor de tabuleiro de sistema do cluster local que está incluído no SDK.
   
![Notificação do tabuleiro de sistema do cluster local][4]

Uma vez iniciada a aplicação, o Visual Studio apresentará automaticamente o **Visualizador de Eventos de Diagnóstico**, onde pode ver os resultados de rastreio dos seus serviços.
   
![Visualizador de eventos de diagnóstico][5]

O modelo de serviço com estado que utilizámos mostra simplesmente um valor de contador que se incrementa no método `RunAsync` de **MyStatefulService.cs**.

Expanda um dos eventos para ver mais detalhes, incluindo o nó em que se executa o código. Neste caso, é \_Nó\_, embora possa ser diferente no seu computador.
   
![Detalhe do visualizador de eventos de diagnóstico][6]

O cluster local contém cinco nós que estão alojados num único computador. Num ambiente de produção, cada nó é alojada num computador físico ou virtual diferente. Para simular a perda de uma máquina enquanto executamos o depurador do Visual Studio ao mesmo tempo, vamos desativar um dos nós no cluster local.

Na janela **Explorador de Soluções**, abra **MyStatefulService.cs**. 

Localize o método `RunAsync` e defina um ponto de interrupção na primeira linha do método.

![Método RunAsync com ponto de interrupção do serviço com estado ][7]

Inicie a ferramenta **Service Fabric Explorer**, ao clicar com o botão direito do rato na aplicação de tabuleiro do sistema do **Gestor de Clusters Locais** e escolha **Gerir Cluster Local**.

![Iniciar o Service Fabric Explorer a partir do Gestor de Clusters Locais][systray-launch-sfx]

O [**Service Fabric Explorer**](service-fabric-visualizing-your-cluster.md) oferece uma representação visual de um cluster. Inclui o conjunto de aplicações implementadas no mesmo e o conjunto de nós físicos que o constituem.

No painel da esquerda, expanda **Cluster > Nós** e localizar o nó que está a executar o seu código.

Clique em **Ações > Desativar (Reiniciar)** para simular um reinício do computador.

![Parar um nó no Service Fabric Explorer][sfx-stop-node]

Momentaneamente, deverá ver o ponto de interrupção atingido no Visual Studio, dado que a computação que estava a fazer diretamente num nó falha no outro.


Em seguida, regresse ao Visualizador de Eventos de Diagnóstico e observe as mensagens. O contador não deixa de aumentar, apesar de os eventos, na verdade, estarem a vir por um nó diferente.

![Visualizador de eventos de diagnóstico após falha][diagnostic-events-viewer-detail-post-failover]

## <a name="cleaning-up-the-local-cluster-optional"></a>Limpar o cluster local (opcional)

Lembre-se de que este cluster local é real. Parar o depurador remove a instância da aplicação e anula o registo do tipo de aplicação. Contudo, o cluster continua a ser executado em segundo plano. Quando estiver pronto para parar o cluster local, tem duas opções à sua disposição.

### <a name="keep-application-and-trace-data"></a>Manter os dados da aplicação e de rastreio

Encerre o cluster, ao clicar com o botão direito do rato na aplicação de tabuleiro do sistema **Gestor de Clusters Locais** e escolha **Parar Cluster Local**.

### <a name="delete-the-cluster-and-all-data"></a>Eliminar o cluster e todos os dados

Remova o cluster, ao clicar com o botão direito do rato na aplicação de tabuleiro do sistema **Gestor de Clusters Locais** e escolha **Remover Cluster Local**. 

Se escolher esta opção, o Visual Studio irá reimplementar o cluster da próxima vez que a aplicação for executada. Escolha esta opção se não pretender utilizar o cluster local durante algum tempo ou se precisar de recuperar recursos.

## <a name="next-steps"></a>Passos seguintes
Leia mais sobre o [Reliable Services](service-fabric-reliable-services-introduction.md).
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
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png

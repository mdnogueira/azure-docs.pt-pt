<properties
   pageTitle="Criação da sua primeira aplicação de Service Fabric no Visual Studio | Microsoft Azure"
   description="Criar, implementar e depurar uma aplicação de Service Fabric com o Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/27/2016"
   ms.author="ryanwi"/>

# Criação da sua primeira aplicação de Service Fabric no Visual Studio

O SDK do Service Fabric inclui um suplemento para Visual Studio que fornece modelos e ferramentas para criar, implementar e depurar aplicações de Service Fabric. Este tópico guia-o no processo de criação da sua primeira aplicação no Visual Studio.

## Pré-requisitos

Antes de começar, certifique-se de que [configurou o seu ambiente de desenvolvimento](service-fabric-get-started.md).

## Instruções de vídeo

O vídeo a seguir guia-o pelos passos neste tutorial:

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Criar a aplicação

Uma aplicação de Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. Com o assistente de Novo Projeto, pode criar um projeto de aplicação, juntamente com o seu primeiro projeto de serviço. Pode adicionar mais serviços posteriormente.

1. Inicie o Visual Studio como administrador.

2. Clique em **Ficheiro > Novo Projeto > Nuvem > Aplicação de Service Fabric**.

3. Atribua um nome à aplicação e clique em **OK**.

    ![Caixa de diálogo de novo projeto no Visual Studio][1]

4. Na página seguinte, será pedido para escolher o primeiro tipo de serviço a incluir na sua aplicação. Para este tutorial, escolheremos **Com Estado**. Atribua-lhe um nome e clique em **OK**.

    ![Caixa de diálogo novo serviço no Visual Studio][2]

    >[AZURE.NOTE] Para obter mais informações sobre as opções, consulte o artigo [Escolher uma estrutura](service-fabric-choose-framework.md).

    O Visual Studio cria o projeto de aplicação e o projeto de serviço com estado e apresenta-os no Explorador de Soluções.

    ![Explorador de Soluções a seguir a criação da aplicação com o serviço com estado][3]

    O projeto de aplicação não contém qualquer código diretamente. Em vez disso, referencia um conjunto de projetos de serviço. Além disso, contém outros três tipos de conteúdo:

    - **Perfis de publicação**: utiliza-se para gerir as preferências de ferramentas para os diferentes ambientes.

    - **Scripts**: inclui um script de PowerShell para implementar/atualizar a sua aplicação. O Visual Studio utiliza este script em segundo plano e pode ser invocado diretamente na linha de comandos.

    - **Definição da aplicação**: inclui o manifesto da aplicação em *ApplicationPackageRoot* e os ficheiros de parâmetro de aplicação em *ApplicationParameters* que definir a aplicação e permitem-lhe configurá-la especificamente para um determinado ambiente.

    Para obter uma descrição geral do conteúdo do projeto de serviço, consulte o artigo [Introdução a Reliable Services](service-fabric-reliable-services-quick-start.md).

## Implemente a aplicação e depure-a

Agora que tem uma aplicação, pode tentar executá-la.

1. Prima F5 no Visual Studio para implementar a aplicação, com o fim de depurá-la.

    >[AZURE.NOTE] A primeira vez que o fizer demorará algum tempo, dado que o Visual Studio está a criar um cluster local para o desenvolvimento. Nos clusters locais executa-se o mesmo código de plataforma que se compilam num cluster com várias máquinas, mas numa única máquina. O estado de criação do cluster aparecerá na janela de saída do Visual Studio.

    Quando o cluster estiver pronto, receberá uma notificação da aplicação do gestor de tabuleiro de sistema do cluster local que está incluído com o SDK.

    ![Notificação do tabuleiro de sistema do cluster local][4]

2. Uma vez iniciada a aplicação, o Visual Studio apresentará automaticamente o Visualizador de Eventos de Diagnóstico, onde pode ver os resultados de rastreio do serviço.

    ![Visualizador de eventos de diagnóstico][5]

    No caso do modelo de serviço com estado, as mensagens mostram simplesmente o valor do contador que se incrementa no método `RunAsync` de MyStatefulService.cs.

3. Expanda um dos eventos para ver mais detalhes, incluindo o nó em que se executa o código. Neste caso, é _Node_2, embora possa ser diferente no seu computador.

    ![Detalhe do visualizador de eventos de diagnóstico][6]

    O cluster local contém cinco nós que estão alojados num único computador. Imita um cluster de cinco nós, no qual os nós estão em máquinas distintas. Desativemos um dos nós no cluster local para simular a perda de uma máquina e executemos o depurador do Visual Studio ao mesmo tempo.

    >[AZURE.NOTE] Os eventos de diagnóstico de aplicações que são emitidos pelo modelo de projeto utilizam a classe `ServiceEventSource` incluída. Para obter mais informações, consulte o artigo [Como monitorizar e diagnosticar os serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

4. Procure a classe no seu serviço de projeto que deriva de StatefulService (por exemplo, MyStatefulService) e defina um ponto de interrupção na primeira linha do método `RunAsync`.

    ![Método RunAsync com ponto de interrupção do serviço com estado ][7]

5. Clique com o botão direito na aplicação de tabuleiro de sistema do Gestor de Clusters Locais e selecione **Gerir Cluster Local** para iniciar o Service Fabric Explorer.

    ![Iniciar o Service Fabric Explorer a partir do Gestor de Clusters Locais][systray-launch-sfx]

    O Service Fabric Explorer proporciona uma representação visual de um cluster - incluindo o conjunto de aplicações aí implementadas e o conjunto de nós físicos que o constituem. Para saber mais acerca do Service Fabric Explorer, consulte o artigo [Visualizar o seu cluster](service-fabric-visualizing-your-cluster.md).

6. No painel da esquerda, expanda **Cluster > Nós** e localizar o nó que está a executar o seu código.

7. Clique em **Ações > Desativar (Reiniciar)** para simular um reinício do computador. (Tenha em atenção de que também pode fazê-lo num menu de contexto na vista de lista de nós no painel da esquerda, através da seleção das reticências.)

    ![Parar um nó no Service Fabric Explorer][sfx-stop-node]

    Momentaneamente, deverá ver o ponto de interrupção atingido no Visual Studio, dado que a computação que estava a fazer diretamente num nó falha no outro.

8. Regresse ao Visualizador de Eventos de Diagnóstico e observe as mensagens. Tenha em atenção que o contador não deixa de aumentar, apesar dos eventos, na verdade, estarem a vir por um nó diferente.

    ![Visualizador de eventos de diagnóstico após falha][diagnostic-events-viewer-detail-post-failover]

### Limpeza

  Antes de concluir, é importante lembrar-se de que o cluster local é real. Mesmo depois de parar o depurador e fechar o Visual Studio, as aplicações continuarão a ser executadas em segundo plano. Dependendo da natureza das suas aplicações, esta atividade em segundo plano pode consumir recursos significativos no seu computador. Tem várias opções para lidar com isto:

  1. Para remover uma aplicação individual e todos os seus dados, utilize a ação **Eliminar aplicação** no Service Fabric Explorer com o menu **AÇÕES** ou com o menu de contexto, na vista da lista de aplicações, no painel da esquerda.

    ![Eliminar uma aplicação no Service Fabric Explorer][sfe-delete-application]

  2. Depois de eliminar a aplicação do cluster, pode optar por **Tipo de Não Aprovisionamento** para a aplicação que remove o pacote da aplicação, incluindo o código e a configuração, a partir do armazém de imagem do cluster.
  3. Para encerrar o cluster, mas manter os dados de aplicação e o rastreio, clique em **Parar Cluster Local** na aplicação de tabuleiro do sistema.

  4. Para eliminar o cluster totalmente, clique em **Remover Cluster Local** na aplicação de tabuleiro do sistema. Tenha em atenção que esta opção resultará noutra implementação lenta da próxima vez que premir F5 no Visual Studio. Utilize esta opção apenas se não pretender utilizar o cluster local durante algum tempo ou se precisar de recuperar recursos.



## Passos seguintes

<!--
Temporarily removing this link because we have removed the ASP.NET template.

 - [See how you can expose your services to the Internet with a web service front end](service-fabric-add-a-web-frontend.md)
-->
- [Saiba como criar um cluster no Azure](service-fabric-cluster-creation-via-portal.md)
- [Obter mais informações sobre Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Experimente criar um serviço utilizando o modelo de programação Reliable Actors](service-fabric-reliable-actors-get-started.md)

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



<!--HONumber=Jun16_HO2-->



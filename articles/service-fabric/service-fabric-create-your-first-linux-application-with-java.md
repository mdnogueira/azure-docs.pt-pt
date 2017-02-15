---
title: "Criar a sua primeira aplicação do Service Fabric no Linux com Java | Microsoft Docs"
description: "Criar e implementar uma aplicação do Service Fabric com Java"
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 381d372b549fa0ed0900d97c03b9584b21624b25
ms.openlocfilehash: 4a8fb3499ec55e451b54a05d5642bdf9a924294f


---
# <a name="create-your-first-azure-service-fabric-application"></a>Criar a sua primeira aplicação do Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

O Service Fabric disponibiliza SDKs para criar serviços no Linux em .NET Core e Java. Neste tutorial, podemos criar uma aplicação para Linux e compilar um serviço com Java.  

> [!NOTE]
> Java como linguagem de programação incorporada de primeira categoria é suportada apenas para a pré-visualização do Linux (o suporte do Windows está planeado). No entanto, todas as aplicações que incluam aplicações Java podem ser executadas como executáveis convidados ou dentro de contentores em Windows ou Linux. Para obter mais informações, veja [Implementar um executável existente no Azure Service Fabric](service-fabric-deploy-existing-app.md) e [Implementar contentores no Service Fabric](service-fabric-deploy-container.md).
>

## <a name="video-tutorial"></a>Tutorial de vídeo

O seguinte vídeo da Academia Virtual da Microsoft orienta-o ao longo do processo de criação de uma aplicação Java no Linux:  
<center><a target="\_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que [configurou o seu ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md). Se estiver a utilizar o Mac OS X, pode [configurar um ambiente de uma caixa do Linux numa máquina virtual com Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Criar a aplicação
Uma aplicação de Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que permite criar facilmente o seu primeiro serviço e adicionar outros mais tarde. Vamos utilizar o Yeoman para criar uma aplicação com um único serviço.

1. Num terminal, escreva ``yo azuresfjava``.
2. Dê um nome à aplicação.
3. Escolha o tipo do seu primeiro serviço e dê-lhe um nome. Para os objetivos deste tutorial, escolhemos um Serviço Reliable Actor.

   ![Gerador Yeoman do Service Fabric para Java][sf-yeoman]

> [!NOTE]
> Para obter mais informações sobre as opções, veja [Service Fabric programming model overview (Descrição geral do modelo de programação Service Fabric)](service-fabric-choose-framework.md).
>

## <a name="build-the-application"></a>Criar a aplicação
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode utilizar para criar a aplicação a partir do terminal.

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Implementar a aplicação
Depois de criada a aplicação, pode implementá-la no cluster local com a CLI do Azure.

1. Ligue ao cluster do Service Fabric local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Utilize o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

    ```bash
    ./install.sh
    ```

3. Abra um browser e navegue para o Service Fabric Explorer, em http://localhost:19080/Explorer (substitua localhost pelo IP privado da VM se estiver a utilizar Vagrant em Mac OS X).

4. Expanda o nó Aplicações e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

## <a name="start-the-test-client-and-perform-a-failover"></a>Iniciar o cliente de teste e executar uma ativação pós-falha
Os projetos de ator não fazem nada só por si. Precisam de outro serviço ou cliente que lhes envie mensagens. O modelo de ator inclui um script de teste simples, que pode utilizar para interagir com o serviço de ator.

1. Execute o script com o utilitário watch para ver o resultado do serviço de ator.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Service Fabric Explorer, localize o nó que aloja a réplica primária do serviço de ator. Na captura de ecrã, é o nó 3.

    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]

3. Clique no nó que localizou no passo anterior e selecione **Desativar (reiniciar)**, no menu Ações. Esta ação reinicia um dos cinco nós no seu cluster local e força uma ativação pós-falha numa das réplicas secundárias em execução noutro nó. Quando realizar esta ação, tenha atenção à saída do cliente de teste e repare que o contador continua a aumentar, apesar da ativação pós-falha.

## <a name="build-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Criar e implementar uma aplicação com o plug-in do Eclipse Neon

Se tiver instalado o [Plug-in do Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#install-the-java-sdk-and-eclipse-neon-plugin-optional) para o Eclipse Neon, pode utilizá-lo para criar, compilar e implementar aplicações do Service Fabric criadas com Java.  Ao instalar o Eclipse, escolha o **Eclipse IDE for Java Developers**.

### <a name="create-the-application"></a>Criar a aplicação

O plug-in do Service Fabric está disponível através da extensibilidade do Eclipse.

1. No Eclipse, escolha **Ficheiro > Outro > Service Fabric**. Verá um conjunto de opções, incluindo Atores e Contentores.

    ![Modelos do Service Fabric no Eclipse][sf-eclipse-templates]

2. Neste caso, escolha Serviço Sem Estado.

3. É-lhe pedido para confirmar a utilização da perspetiva do Service Fabric, o que otimiza o Eclipse para uso com os projetos do Service Fabric. Escolha "Sim".

### <a name="deploy-the-application"></a>Implementar a aplicação
Os modelos do Service Fabric incluem um conjunto de tarefas Gradle para criar e implementar aplicações, que pode acionar através do Eclipse.

1. Escolha **Executar > Executar Configurações**.
2. Especifique **local** ou **cloud**. A configuração predefinida é **local**. Para implementar um cluster remoto, selecione **cloud**.
3. Certifique-se de que as informações adequadas são preenchidas nos perfis de publicação, ao editar o `local.json` ou `cloud.json`, conforme adequado.
4. Clique em **Executar**.

A aplicação é criada e implementada ao fim de alguns momentos. Pode monitorizar o respetivo estado no Service Fabric Explorer.

## <a name="adding-more-services-to-an-existing-application"></a>Adicionar mais serviços a uma aplicação existente

Para adicionar outro serviço a uma aplicação já criada com o `yo`, execute os seguintes passos:
1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Execute `yo azuresfjava:AddService`


## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre os Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagir com os clusters do Service Fabric com a CLI do Azure](service-fabric-azure-cli.md)
* [Resolução de problemas de implementação](service-fabric-azure-cli.md#troubleshooting)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Jan17_HO1-->



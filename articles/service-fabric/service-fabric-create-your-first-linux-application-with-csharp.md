---
title: "Criar a sua primeira aplicação do Service Fabric no Linux com C# | Microsoft Docs"
description: "Criar e implementar uma aplicação do Service Fabric com C#"
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9486fcb56b05b22120aef5a8373c6558b2d88d6c


---
# <a name="create-your-first-azure-service-fabric-application"></a>Criar a sua primeira aplicação do Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

O Service Fabric disponibiliza SDKs para criar serviços no Linux em .NET Core e Java. Neste tutorial, vamos ver como pode criar uma aplicação para Linux e compilar um serviço com C# (Núcleo de .NET).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que [configurou o seu ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md). Se estiver a utilizar o Mac OS X, pode [configurar um ambiente de uma caixa do Linux numa máquina virtual com Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Criar a aplicação
Uma aplicação de Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que permite criar facilmente o seu primeiro serviço e adicionar outros mais tarde. Vamos utilizar o Yeoman para criar uma aplicação com um único serviço.

1. Num terminal, escreva o seguinte comando para começar a criar a estrutura: `yo azuresfcsharp`
2. Dê um nome à aplicação.
3. Escolha o tipo do seu primeiro serviço e dê-lhe um nome. Para os objetivos deste tutorial, escolhemos um Serviço Reliable Actor.
   
   ![Gerador Yeoman do Service Fabric para C#][sf-yeoman]

> [!NOTE]
> Para obter mais informações sobre as opções, veja [Service Fabric programming model overview (Descrição geral do modelo de programação Service Fabric)](service-fabric-choose-framework.md).
> 
> 

## <a name="build-the-application"></a>Criar a aplicação
Os modelos Yeoman do Service Fabric incluem um script de compilação que pode utilizar para criar a aplicação a partir do terminal (depois de navegar para a pasta da aplicação).

  ```bash
 cd myapp 
 ./build.sh 
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
3. Clique no nó que localizou no passo anterior e selecione **Desativar (reiniciar)**, no menu Ações. Esta ação reinicia um dos cinco nós no seu cluster local e força uma ativação pós-falha num réplica secundária em execução noutro nó. Quando realizar esta ação, tenha atenção à saída do cliente de teste e repare que o contador continua a aumentar, apesar da ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre os Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagir com os clusters do Service Fabric com a CLI do Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png



<!--HONumber=Nov16_HO2-->



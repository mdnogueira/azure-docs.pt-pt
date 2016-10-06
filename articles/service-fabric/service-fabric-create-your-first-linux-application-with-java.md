<properties
   pageTitle="Criar a sua primeira aplicação do Service Fabric no Linux com Java | Microsoft Azure"
   description="Criar e implementar uma aplicação do Service Fabric com Java"
   services="service-fabric"
   documentationCenter="java"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="seanmck"/>


# Criar a sua primeira aplicação do Azure Service Fabric no Linux com Java

O Service Fabric disponibiliza SDKs para criar serviços no Linux em .NET Core e Java. Neste tutorial, vamos ver como pode criar uma aplicação para Linux e compilar um serviço com Java.

## Pré-requisitos

Antes de começar, certifique-se de que [configurou o seu ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md). Se estiver a utilizar o Mac OS X, pode [configurar um ambiente de uma caixa do Linux numa máquina virtual com Vagrant](service-fabric-get-started-mac.md).

## Criar a aplicação

Uma aplicação de Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que permite criar facilmente o seu primeiro serviço e adicionar outros mais tarde. Vamos utilizar o Yeoman para criar uma aplicação nova com um único serviço.

1. Num terminal, escreva **yo azuresfjava**.

2. Dê um nome à aplicação.

3. Escolha o tipo do seu primeiro serviço e dê-lhe um nome. Para os objetivos deste tutorial, vamos escolher um Serviço Reliable Actor.

  ![Gerador Yeoman do Service Fabric para Java][sf-yeoman]

>[AZURE.NOTE] Para obter mais informações sobre as opções, veja [Service Fabric programming model overview (Descrição geral do modelo de programação Service Fabric)](service-fabric-choose-framework.md).

## Criar a aplicação

Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode utilizar para criar a aplicação a partir do terminal.

  ```bash
  gradle
  ```

## Implementar a aplicação

Depois de criada a aplicação, pode implementá-la no cluster local com a CLI do Azure.

1. Ligue ao cluster do Service Fabric local.

    ```bash
    azuresfcli servicefabric cluster connect
    ```

2. Utilize o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

    ```bash
    cd myapp
    ./install.sh
    ```

3. Abra um browser e navegue para o Service Fabric Explorer, em http://localhost:19080/Explorer (substitua localhost pelo IP privado da VM se estiver a utilizar Vagrant em Mac OS X).

4. Expanda o nó Aplicações e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

## Iniciar o cliente de teste e executar uma ativação pós-falha

Os projetos de ator não fazem nada só por si. Precisam de outro serviço ou cliente que lhes envie mensagens. O modelo de ator inclui um script de teste simples, que pode utilizar para interagir com o serviço de ator.

1. Execute o script com o utilitário watch para ver o resultado do serviço de ator.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Service Fabric Explorer, localize o nó que aloja a réplica primária do serviço de ator. Na captura de ecrã, é o nó 3.

    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]

3. Clique no nó que localizou no passo anterior e selecione **Desativar (reiniciar)**, no menu Ações. É reiniciado um dos cinco nós no seu cluster local e forçada uma ativação pós-falha numa das réplicas secundárias em execução noutro nó. Quando o fizer, tenha atenção à saída do cliente de teste e repare que o contador continua a aumentar, apesar da ativação pós-falha.

## Criar e implementar uma aplicação com o plug-in do Eclipse Neon

Se tiver instalado o plug-in do Service Fabric para o Eclipse Neon, pode utilizá-lo para criar, compilar e implementar aplicações do Service Fabric criadas com Java.

### Criar a aplicação

O plug-in do Service Fabric está disponível através da extensibilidade do Eclipse.

1. No Eclipse, escolha **Ficheiro > Outro > Service Fabric**. Verá um conjunto de opções, incluindo Atores e Contentores.

    ![Modelos do Service Fabric no Eclipse][sf-eclipse-templates]

2. Neste caso, escolha Serviço Sem Estado.

3. É-lhe pedido para confirmar a utilização da perspetiva do Service Fabric, o que otimiza o Eclipse para uso com os projetos do Service Fabric. Escolha "Sim".

### Implementar a aplicação

Os modelos do Service Fabric incluem um conjunto de tarefas Gradle para criar e implementar aplicações, que pode acionar através do Eclipse.

1. Escolha **Executar > Executar Configurações**.

2. Expanda **Projeto do Gradle** e escolha **ServiceFabricDeployer**.

3. Clique em **Executar**.

A aplicação vai ser criada e implementada ao fim de alguns momentos. Pode monitorizar o respetivo estado no Service Fabric Explorer.

## Passos seguintes

- [Saiba mais sobre o Reliable Actors](service-fabric-reliable-actors-introduction.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Sep16_HO4-->



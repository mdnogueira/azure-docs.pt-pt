---
title: "Criar uma aplicação Java Reliable Actors do Azure Service Fabric no Linux | Microsoft Docs"
description: "Saiba como criar e implementar uma aplicação Java Reliable Actors do Service Fabric em cinco minutos."
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: ryanwi
ms.openlocfilehash: c7625a5670aca5d105601432fedfd0d7a78bb53c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Criar a sua primeira aplicação Java Reliable Actors do Service Fabric no Linux
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Este guia de introdução ajuda-o a criar a sua primeira aplicação Java do Azure Service Fabric num ambiente de desenvolvimento do Linux em apenas alguns minutos.  Quando tiver terminado, terá uma aplicação Java de serviço único simples em execução no cluster de desenvolvimento local.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, instale o SDK do Service Fabric, a CLI do Service Fabric e configure um cluster de desenvolvimento no seu [ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md). Se estiver a utilizar o Mac OS X, pode [configurar um ambiente de desenvolvimento do Linux numa máquina virtual com Vagrant](service-fabric-get-started-mac.md).

Além disso, instale a [CLI do Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Instalar e configurar os geradores para Java
O Service Fabric fornece ferramentas estruturais que irão ajudá-lo a criar uma aplicação Java do Service Fabric a partir do terminal, através do gerador de modelos Yeoman. Siga os passos abaixo para certificar-se de que tem o gerador de modelo yeoman do Service Fabric para Java operacional no seu computador.
1. Instalar nodejs e NPM no seu computador

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) no seu computador a partir do NPM

  ```bash
  sudo npm install -g yo
  ```
3. Instalar o gerador de aplicações Java Yeoman do Service Fabric a partir do NPM

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a utilizar o Reliable Actors, só precisa de compreender alguns conceitos básicos:

* **Serviço de ator**. O Reliable Actors é compactado no Reliable Services que pode ser implementado na infraestrutura do Service Fabric. As instâncias de ator são ativadas numa instância de serviço com nome.
* **Registo de ator**. Tal como acontece com o Reliable Services, um serviço Reliable Actor tem de ser registado com o runtime do Service Fabric. Além disso, o tipo de ator tem de ser registado com o runtime de Ator.
* **Interface de ator**. A interface de ator serve para definir uma interface pública com tipos de dados inflexíveis de um ator. Na terminologia modelo do Reliable Actor, a interface de ator define os tipos de mensagens que o ator pode compreender e processar. A interface de ator é utilizada por outros atores e aplicações cliente para "enviar" (de forma assíncrona) mensagens para o ator. O Reliable Actors pode implementar várias interfaces.
* **Classe ActorProxy**. A classe ActorProxy é utilizada por aplicações cliente para invocar os métodos expostos através da interface de ator. A classe ActorProxy fornece duas funcionalidades importantes:
  
  * Resolução de nomes: é capaz de localizar o ator no cluster (localizar o nó do cluster onde está alojado).
  * Processamento de falhas: pode repetir invocações de método e voltar a resolver a localização do ator após, por exemplo, uma falha que exija que o ator seja relocalizado para outro nó no cluster.

As seguintes regras que pertencem a interfaces de ator merecem especial referência:

* Os métodos de interfaces de ator não podem ser sobrecarregados.
* Os métodos de interfaces de ator não podem ter parâmetros out, ref ou opcionais.
* As interfaces genéricas não são suportadas.

## <a name="create-the-application"></a>Criar a aplicação
Uma aplicação de Service Fabric contém um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. O gerador instalado na última secção facilita a criação do primeiro serviço e a adição posterior de outros.  Também pode criar, construir e implementar aplicações Java do Service Fabric com um plug-in do Eclipse. Veja [Criar e implementar a sua primeira aplicação Java com o Eclipse](service-fabric-get-started-eclipse.md). Neste guia de introdução, utilize Yeoman para criar uma aplicação com um único serviço, que armazena e obtém um valor do contador.

1. Num terminal, escreva ``yo azuresfjava``.
2. Dê um nome à aplicação.
3. Escolha o tipo do seu primeiro serviço e dê-lhe um nome. Para este tutorial, escolha um Serviço Reliable Actor. Para obter mais informações sobre os outros tipos de serviço, veja [Service Fabric programming model overview (Descrição geral do modelo de programação do Service Fabric)](service-fabric-choose-framework.md).
   ![Gerador Yeoman do Service Fabric para Java][sf-yeoman]

Se der à aplicação o nome "HelloWorldActorApplication" e ao ator o nome "HelloWorldActor", é criado o andaime seguinte:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Blocos modulares básicos do Reliable Actors
Os conceitos básicos descritos anteriormente traduzem-se nos blocos modulares básicos de um serviço Reliable Actor.

### <a name="actor-interface"></a>Interface de ator
Contém a definição de interface para o ator. Esta interface define o contrato de ator que é partilhado pela implementação de ator e pelos clientes que chamam o ator, pelo que, normalmente, faz sentido defini-lo num local que esteja separado da implementação de ator e que possa ser partilhado por vários outros serviços ou aplicações cliente.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Serviço de ator
Contém a implementação de ator e o código de registo de ator. A classe de ator implementa a interface de ator. É aqui que seu ator faz o seu trabalho.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registo de ator
O serviço de ator tem de ser registado com um tipo de serviço no runtime do Service Fabric. Para que o Serviço de Ator execute as suas instâncias de ator, o tipo de ator também tem de estar registado no Serviço de Ator. O método de registo `ActorRuntime` realiza este trabalho para atores.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Criar a aplicação
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode utilizar para criar a aplicação a partir do terminal.
As dependências Java do Service Fabric são obtidas do Maven. Para criar e trabalhar nas aplicações Java do Service Fabric, tem de garantir que o JDK e Gradle estão instalados. Se ainda não estiverem instalados, execute o seguinte para instalar o JDK(openjdk-8-jdk) e Gradle -

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Para criar e empacotar a aplicação, execute o seguinte:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Implementar a aplicação
Depois de criada a aplicação, pode implementá-la no cluster local.

1. Ligue ao cluster do Service Fabric local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Execute o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

    ```bash
    ./install.sh
    ```

A implementação da aplicação criada é igual à de qualquer outra aplicação do Service Fabric. Veja a documentação sobre como [gerir uma aplicação do Service Fabric com a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md) para obter instruções detalhadas.

Os parâmetros desses comandos encontram-se nos manifestos gerados dentro do pacote de aplicação.

Após a implementação da aplicação, abra um browser e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Em seguida, expanda o nó **Aplicações** e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

## <a name="start-the-test-client-and-perform-a-failover"></a>Iniciar o cliente de teste e executar uma ativação pós-falha
Os atores não fazem nada sozinhos, precisam de outro serviço ou cliente que lhes envie mensagens. O modelo de ator inclui um script de teste simples, que pode utilizar para interagir com o serviço de ator.

1. Execute o script com o utilitário watch para ver o resultado do serviço de ator.  O script de teste chama o método `setCountAsync()` no ator para incrementar um contador, chama o método `getCountAsync()` no ator para obter o valor do novo contador e apresenta esse valor à consola.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Service Fabric Explorer, localize o nó que aloja a réplica primária do serviço de ator. Na captura de ecrã, é o nó 3. A réplica do principal de serviço gere as operações de escrita e leitura.  As alterações ao estado do serviço são então replicadas para as réplicas secundárias, em execução nos nós 0 e 1 da captura de ecrã abaixo.

    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]

3. Em **Nós**, clique no nó que localizou no passo anterior e selecione **Desativar (reiniciar)**, no menu Ações. Esta ação reinicia o nó em execução na réplica do principal de serviço e força uma ativação pós-falha numa das réplicas secundárias em execução noutro nó.  Essa réplica secundária é promovida a principal, outra réplica secundária é criada num nó diferente e a réplica primária começa a executar operações de leitura/escrita. Á medida que o nó reinicia, observe a saída do cliente de teste e repare que o contador continua a aumentar, apesar da ativação pós-falha.

## <a name="remove-the-application"></a>Remover a aplicação
Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação, anular o registo do pacote de aplicação e remover o pacote de aplicação do arquivo de imagens do cluster.

```bash
./uninstall.sh
```

No explorador do Service Fabric, pode constatar que a aplicação e o tipo da aplicação já não são apresentados no nó **Aplicações**.

## <a name="service-fabric-java-libraries-on-maven"></a>Bibliotecas Java do Service Fabric no Maven
As bibliotecas Java do Service Fabric foram alojadas no Maven. Pode adicionar as dependências no ``pom.xml`` ou ``build.gradle`` dos seus projetos para utilizar bibliotecas Java do Service Fabric a partir de **mavenCentral**. 

### <a name="actors"></a>Atores

Suporte de Reliable Actor do Service Fabric para a sua aplicação.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Serviços

Suporte de Reliable Services do Service Fabric para a sua aplicação.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Outros
#### <a name="transport"></a>Transporte

Suporte da camada de transporte para a aplicação Java do Service Fabric. Não é necessário adicionar esta dependência explicitamente às suas aplicações Reliable Actor ou Serviço, a não ser que o programe na camada de transporte.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Suporte para o Fabric

Suporte ao nível do sistema para o Service Fabric, que comunica com o runtime do Service Fabric nativo. Não é necessário adicionar esta dependência explicitamente às suas aplicações Reliable Actor ou Serviço. Isto é obtido automaticamente do Maven, quando incluir as outras dependências acima.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrar aplicações Java antigas do Service Fabric para utilizar com o Maven
Recentemente, movemos as bibliotecas Java do Service Fabric do SDK Java do Service Fabric para o repositório Maven. Enquanto as aplicações novas que gerar através do Yeoman ou Eclipse irão gerar projetos com as atualizações mais recentes (que conseguirão trabalhar com o Maven), pode atualizar as aplicações Java sem estado ou de Actor do Service Fabric, que estavam a utilizar anteriormente o SDK Java do Service Fabric, para utilizar as dependências Java do Service Fabric a partir do Maven. Siga os passos mencionados [aqui](service-fabric-migrate-old-javaapp-to-use-maven.md) para garantir que a aplicação mais antiga funciona com o Maven.

## <a name="next-steps"></a>Passos seguintes

* [Create your first Service Fabric Java application on Linux using Eclipse (Criar a sua primeira aplicação Java do Service Fabric no Linux com o Eclipse)](service-fabric-get-started-eclipse.md)
* [Saiba mais sobre os Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagir com os clusters do Service Fabric através da CLI do Service Fabric](service-fabric-cli.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

---
title: "Criar a sua primeira microsserviço fiável de Azure em Java | Microsoft Docs"
description: "Introdução à criação de uma aplicação do Microsoft Azure Service Fabric com serviços sem monitorização de estado e com monitorização de estado."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 59b58e9d9bdb044c81261fd19338c3f95bd409b3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="get-started-with-reliable-services"></a>Introdução ao Reliable Services
> [!div class="op_single_selector"]
> * [C# no Windows](service-fabric-reliable-services-quick-start.md)
> * [Java em Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Este artigo explica as noções básicas do Azure Service Fabric Reliable Services e explica-lhe como criar e implementar uma aplicação de serviço fiável simple escrita em Java. Este vídeo do Microsoft Virtual Academy também mostra-lhe como criar um serviço fiável sem monitorização de estado:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Instalação e configuração
Antes de começar, certifique-se de que tem o ambiente de desenvolvimento do Service Fabric configurar no seu computador.
Se precisar de configurá-lo, aceda a [introdução no Mac](service-fabric-get-started-mac.md) ou [introdução no Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceitos básicos
Para começar a Reliable Services, só tem de compreender alguns conceitos básicos:

* **Tipo de serviço**: Esta é a implementação de serviço. Está definido pela classe de escrita que expande `StatelessService` e quaisquer outras código ou dependências utilizadas nas mesmas, juntamente com um nome e um número de versão.
* **Com o nome de instância de serviço**: ao executar o serviço, criar instâncias nomeadas do seu tipo de serviço, muito como criar instâncias do objecto de um tipo de classe. Instâncias de serviço são de facto instâncias de objeto da sua classe de serviço escreve.
* **Anfitrião do serviço**: as instâncias de serviço com nome que cria tem de executar no interior de um anfitrião. O anfitrião do serviço é apenas um processo em que podem executar instâncias do seu serviço.
* **Serviço de registo**: registo reúne tudo. O tipo de serviço tem de ser registado com o runtime Service Fabric num anfitrião de serviço para permitir que o Service Fabric criar instâncias do mesmo para ser executada.  

## <a name="create-a-stateless-service"></a>Criar um serviço sem monitorização de estado
Comece por criar uma aplicação de Service Fabric. O SDK de Service Fabric para Linux inclui um Yeoman gerador para fornecer andaime para uma aplicação de Service Fabric com um serviço sem estado. Comece por executar o Yeoman seguinte comando:

```bash
$ yo azuresfjava
```

Siga as instruções para criar um **sem monitorização de estado de serviço fiável**. Para este tutorial, atribua um nome de aplicação "HelloWorldApplication" e o serviço "Olámundo". O resultado inclui diretórios para o `HelloWorldApplication` e `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementar o serviço
Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Esta classe define o tipo de serviço e pode executar qualquer código. A API de serviço fornece dois pontos de entrada para o seu código:

* Um método de ponto de entrada open-ended, denominado `runAsync()`, onde pode começar a executar quaisquer cargas de trabalho, incluindo as cargas de trabalho de computação de execução longa.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Um ponto de entrada de comunicação em que pode plug-in a pilha de comunicação de eleição. Este é onde pode começar a receber pedidos de utilizadores e outros serviços.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Neste tutorial, iremos focar-se no `runAsync()` o método de ponto de entrada. Este é onde imediatamente pode começar a executar o seu código.

### <a name="runasync"></a>Runasync com
A plataforma chama este método quando uma instância de um serviço está pronto para executar e colocá-la. Para um serviço sem monitorização de estado, basta que significa quando a instância de serviço é aberta. Um token de cancelamento é fornecido para coordenar quando a instância de serviço tem de ser fechado. No Service Fabric, este ciclo de abertura/fecho de uma instância de serviço pode ocorrer demasiadas vezes ao longo da duração do serviço como um todo. Esta situação pode ocorrer por vários motivos, incluindo:

* O sistema move as instâncias de serviço para balanceamento de recurso.
* Falhas ocorrerem no seu código.
* A aplicação ou o sistema está atualizado.
* O hardware subjacente sofre uma falha.

Esta orquestração é gerida pelo serviço de recursos de infraestrutura para manter o seu serviço altamente disponível e corretamente equilibrada.

`runAsync()`não deverá bloquear forma síncrona. A implementação de runasync com deverá devolver um CompletableFuture para permitir que o tempo de execução continuar. Se a carga de trabalho tem de implementar uma tarefa de execução longa que deve ser efetuada dentro de CompletableFuture.

#### <a name="cancellation"></a>Cancelamento
O cancelamento da sua carga de trabalho é um esforço cooperative orquestrado pelo token de cancelamento fornecido. O sistema aguarda que a tarefa terminar (por conclusão com êxito, cancelamento ou falhas) antes de se move. É importante honrar o token de cancelamento, concluir qualquer trabalho e sair `runAsync()` mais rapidamente possível quando o sistema pede cancelamento. O exemplo seguinte demonstra como processar um evento de cancelamento:

```java
    @Override
    protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

        // TODO: Replace the following sample code with your own logic
        // or remove this runAsync override if it's not needed in your service.

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
    }
```

### <a name="service-registration"></a>Registo do serviço
Tipos de serviço tem de ser registados com o tempo de execução do Service Fabric. O tipo de serviço está definido no `ServiceManifest.xml` e a classe de serviço que implementa `StatelessService`. Registo do serviço é executado no ponto de entrada principal do processo. Neste exemplo, o ponto de entrada principal do processo é `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Executar a aplicação

O Yeoman andaime inclui um script de gradle para compilar a aplicação e a implementar e remover a aplicação de scripts de bash. Para executar a aplicação, primeiro crie a aplicação com o gradle:

```bash
$ gradle
```

Isto produz um pacote de aplicação de Service Fabric pode ser implementado utilizando a CLI de recursos de infraestrutura de serviço.

### <a name="deploy-with-service-fabric-cli"></a>Implementar com a CLI do Service Fabric

O script de install.sh contém os comandos da CLI de recursos de infraestrutura de serviço necessários para implementar o pacote de aplicação. Execute o script de install.sh para implementar a aplicação.

```bash
$ ./install.sh
```

## <a name="next-steps"></a>Passos seguintes

* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

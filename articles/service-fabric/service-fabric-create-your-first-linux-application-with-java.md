---
title: "Criar a sua primeira aplicação de microsserviços do Azure no Linux com Java | Microsoft Docs"
description: "Criar e implementar uma aplicação do Service Fabric com Java"
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
ms.date: 06/02/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: e229602b4bfa72977c9b15e854d796ed09fa55d2
ms.contentlocale: pt-pt
ms.lasthandoff: 07/01/2017


---
<a id="create-your-first-service-fabric-java-application-on-linux" class="xliff"></a>

# Criar a sua primeira aplicação Java do Service Fabric no Linux
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Este guia de introdução ajuda-o a criar a sua primeira aplicação Java do Azure Service Fabric num ambiente de desenvolvimento do Linux em apenas alguns minutos.  Quando tiver terminado, terá uma aplicação Java de serviço único em execução no cluster de desenvolvimento local.  

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos
Antes de começar, instale o SDK do Service Fabric, a CLI do Azure e configure um cluster de desenvolvimento no seu [ambiente de desenvolvimento do Linux](service-fabric-get-started-linux.md). Se estiver a utilizar o Mac OS X, pode [configurar um ambiente de desenvolvimento do Linux numa máquina virtual com Vagrant](service-fabric-get-started-mac.md).

Também deve configurar a [CLI 2.0 do Azure](service-fabric-azure-cli-2-0.md) (recomendado) ou a [CLI XPlat](service-fabric-azure-cli.md) para implementar a aplicação.

<a id="create-the-application" class="xliff"></a>

## Criar a aplicação
Uma aplicação de Service Fabric contém um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade da aplicação. O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que permite criar facilmente o seu primeiro serviço e adicionar outros mais tarde.  Também pode criar, construir e implementar aplicações Java do Service Fabric com um plug-in do Eclipse. Veja [Criar e implementar a sua primeira aplicação Java com o Eclipse](service-fabric-get-started-eclipse.md). Neste guia de introdução, utilize Yeoman para criar uma aplicação com um único serviço, que armazena e obtém um valor do contador.

1. Num terminal, escreva ``yo azuresfjava``.
2. Dê um nome à aplicação. 
3. Escolha o tipo do seu primeiro serviço e dê-lhe um nome. Para este tutorial, escolha um Serviço Reliable Actor. Para obter mais informações sobre os outros tipos de serviço, veja [Service Fabric programming model overview (Descrição geral do modelo de programação do Service Fabric)](service-fabric-choose-framework.md).
   ![Gerador Yeoman do Service Fabric para Java][sf-yeoman]

<a id="build-the-application" class="xliff"></a>

## Criar a aplicação
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode utilizar para criar a aplicação a partir do terminal. Para criar e empacotar a aplicação, execute o seguinte:

  ```bash
  cd myapp
  gradle
  ```

<a id="deploy-the-application" class="xliff"></a>

## Implementar a aplicação
Depois de criada a aplicação, pode implementá-la no cluster local.

<a id="using-xplat-cli" class="xliff"></a>

### Utilizar a CLI XPlat

1. Ligue ao cluster do Service Fabric local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Execute o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

    ```bash
    ./install.sh
    ```

<a id="using-azure-cli-20" class="xliff"></a>

### Utilizar a CLI 2.0 do Azure

A implementação da aplicação criada é igual à de qualquer outra aplicação do Service Fabric. Veja a documentação sobre como [gerir uma aplicação do Service Fabric com a CLI do Azure](service-fabric-application-lifecycle-azure-cli-2-0.md) para obter instruções detalhadas.

Os parâmetros desses comandos encontram-se nos manifestos gerados dentro do pacote de aplicação.

Após a implementação da aplicação, abra um navegador e navegue até [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) em [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Em seguida, expanda o nó **Aplicações** e repare que há, agora, uma entrada para o tipo de aplicação e outra para a primeira instância desse tipo.

<a id="start-the-test-client-and-perform-a-failover" class="xliff"></a>

## Iniciar o cliente de teste e executar uma ativação pós-falha
Os atores não fazem nada sozinhos, precisam de outro serviço ou cliente que lhes envie mensagens. O modelo de ator inclui um script de teste simples, que pode utilizar para interagir com o serviço de ator.

1. Execute o script com o utilitário watch para ver o resultado do serviço de ator.  O script de teste chama o método `setCountAsync()` no ator para incrementar um contador, chama o método `getCountAsync()` no ator para obter o valor do novo contador e apresenta esse valor à consola.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Service Fabric Explorer, localize o nó que aloja a réplica primária do serviço de ator. Na captura de ecrã, é o nó 3. A réplica do principal de serviço gere as operações de escrita e leitura.  As alterações ao estado do serviço são então replicadas para as réplicas secundárias, em execução nos nós 0 e 1 da captura de ecrã abaixo.

    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]

3. Em **Nós**, clique no nó que localizou no passo anterior e selecione **Desativar (reiniciar)**, no menu Ações. Esta ação reinicia o nó em execução na réplica do principal de serviço e força uma ativação pós-falha numa das réplicas secundárias em execução noutro nó.  Essa réplica secundária é promovida a principal, outra réplica secundária é criada num nó diferente e a réplica primária começa a executar operações de leitura/escrita. Á medida que o nó reinicia, observe a saída do cliente de teste e repare que o contador continua a aumentar, apesar da ativação pós-falha.

<a id="add-another-service-to-the-application" class="xliff"></a>

## Adicionar outro serviço à aplicação
Para adicionar outro serviço a uma aplicação existente com o `yo`, execute os seguintes passos:
1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Execute `yo azuresfjava:AddService`
3. Crie e implemente a aplicação, como nas etapas anteriores.

<a id="remove-the-application" class="xliff"></a>

## Remover a aplicação
Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação, anular o registo do pacote de aplicação e remover o pacote de aplicação do arquivo de imagens do cluster.

```bash
./uninstall.sh
```

No explorador do Service Fabric, pode constatar que a aplicação e o tipo da aplicação já não são apresentados no nó **Aplicações**.

<a id="next-steps" class="xliff"></a>

## Passos seguintes
* [Create your first Service Fabric Java application on Linux using Eclipse (Criar a sua primeira aplicação Java do Service Fabric no Linux com o Eclipse)](service-fabric-get-started-eclipse.md)
* [Saiba mais sobre os Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagir com os clusters do Service Fabric com a CLI do Azure](service-fabric-azure-cli.md)
* [Resolução de problemas de implementação](service-fabric-azure-cli.md#troubleshooting)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)

<a id="related-articles" class="xliff"></a>

## Artigos relacionados

* [Introdução ao Service Fabric com a CLI 2.0 do Azure](service-fabric-azure-cli-2-0.md)
* [Introdução ao XPlat CLI do Service Fabric](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png


---
title: Configurar o ambiente de desenvolvimento no Mac OS X para trabalhar com o Azure Service Fabric | Microsoft Docs
description: "Instale o runtime, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir esta configuração, estará pronto para criar aplicações no Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: saysa
ms.openlocfilehash: cf67c377cd5c17f7b540fb23af48a333a9cddf69
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar o ambiente de desenvolvimento no Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Pode criar aplicações do Service Fabric para serem executadas em clusters do Linux através do Mac OS X. Este artigo descreve como configurar o seu Mac para desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos
O Service Fabric não é executado de forma nativa no OS X. Para executar um cluster do Service Fabric local, fornecemos uma imagem de contentor do Docker pré-configurada. Antes de começar, vai precisar:

* De, pelo menos, 4 GB de RAM
* Da versão mais recente do [Docker](https://www.docker.com/)
* De acesso à [imagem](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) de contentor do Service Fabric One-box do Docker

>[!TIP]
> * Pode seguir os passos mencionados na [documentação](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) oficial do Docker para instalá-lo no seu Mac. 
> * Depois de concluída a instalação, execute os passos mencionados [aqui](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine) para confirmar que foi instalada corretamente.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contentor local e configurar o Service Fabric
Para configurar um contentor do Docker local e executar um cluster do Service Fabric no mesmo, realize os passos seguintes:

1. Extrair a imagem do repositório de hubs do Docker:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Iniciar uma instância de contentor do Service Fabric One-box com a imagem:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >Especificar um nome para a instância do contentor permite-lhe processá-la de forma mais legível. 

3. Iniciar sessão no contentor do Docker no modo ssh interativo:

    ```bash
    docker exec -it sfonebox bash
    ```

4. Execute o script de configuração, o qual vai obter as dependências necessárias e, depois, iniciar o cluster no contentor.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

5. Quando o passo 4 estiver devidamente concluído, pode ir a ``http://localhoist:19080`` a partir do seu Mac, onde pode ver o explorador do Service Fabric.


## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurar a CLI do Service Fabric (sfctl) no seu Mac

Siga as instruções em [Service Fabric CLI](service-fabric-cli.md#cli-mac) para instalar a CLI do Service Fabric (`sfctl`) no seu Mac.
Os comandos da CLI para interagir com as entidades do Service Fabric, incluindo clusters, aplicações e serviços.

## <a name="create-application-on-your-mac-using-yeoman"></a>Criar uma aplicação no seu Mac com o Yeoman

O Service Fabric disponibiliza ferramentas estruturais que irão ajudá-lo a criar uma aplicação do Service Fabric a partir do terminal mediante a utilização do gerador de modelos Yeoman. Siga os passos abaixo para confirmar que tem o gerador de modelos Yeoman do Service Fabric a funcionar no seu computador.

1. Tem de ter o Node.js e o NPM instalados no Mac. Caso contrário, pode utilizar o passo seguinte para instalá-los com o Homebrew. Para verificar as versões do Node.js e NPM instaladas no Mac, utilize a opção ``-v``.

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) no seu computador a partir do NPM

    ```bash
    npm install -g yo
    ```
3. Instale o gerador Yeoman que pretende utilizar, seguindo os passos na [documentação](service-fabric-get-started-linux.md) da introdução. Para criar Aplicações do Service Fabric com o Yeoman, siga os passos:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Para criar uma aplicação Java do Service Fabric no Mac, terá de ter - JDK 1.8 e Gradle instalados no computador anfitrião. Se ainda não estiverem instalados, utilize o [HomeBrew](https://brew.sh/) para instalá-los. 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>Implementar a aplicação no seu Mac a partir do terminal

Depois de criar e compilar a aplicação do Service Fabric, pode implementá-la com a [CLI do Service Fabric](service-fabric-cli.md#cli-mac) através dos passos abaixo:

1. Ligue ao cluster do Service Fabric que está em execução dentro da instância de contentor no seu Mac.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Aceda ao diretório do seu projeto e execute o script de instalação.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurar o desenvolvimento em .NET Core 2.0

Instale o [.NET Core 2.0 SDK for Mac](https://www.microsoft.com/net/core#macos) para começar a [criar aplicações do Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes para aplicações do Service Fabric do .NET Core 2.0 são alojados no NuGet.org, atualmente em pré-visualização.

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Instalar o plug-in do Service Fabric para Eclipse Neon no seu Mac

O Service Fabric fornece um plug-in para o **IDE Eclipse Neon para Java** que pode simplificar o processo de criação e implementação de serviços Java. Pode seguir os passos de instalação mencionados nesta [documentação](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) geral sobre a instalação ou atualização do plug-in do Eclipse para o Service Fabric para a versão mais recente.

Todos os outros passos mencionados na [documentação do Eclipse para o Service Fabric](service-fabric-get-started-eclipse.md) para criar uma aplicação, adicionar o serviço mesma, instalá-la/desinstalá-la, etc., será aplicável também aqui.

Para além dos passos anteriores, de modo a que o plug-in do Eclipse para o Service Fabric funcione com o contentor do Docker no seu Mac, deve instanciar o contentor com um caminho partilhado com o seu anfitrião, da seguinte forma:
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
em que ``/Users/sayantan/work/workspaces/mySFWorkspace`` é o caminho completamente qualificado da área de trabalho no Mac e ``/tmp/mySFWorkspace`` é o caminho dentro do contentor, para onde seria mapeado.

> [!NOTE]
>1. Se o nome/caminho da sua área de trabalho for diferente, atualize-o em conformidade no comando ``docker run``, acima.
>2. Se iniciar o contentor com um nome que não ``sfonebox``, atualize-o no ficheiro ``testclient.sh`` na sua aplicação Java de atores do Service Fabric.

## <a name="next-steps"></a>Passos seguintes
<!-- Links -->
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Create a Service Fabric cluster in the Azure portal (Criar um cluster do Service Fabric no portal do Azure)](service-fabric-cluster-creation-via-portal.md)
* [Create a Service Fabric cluster using the Azure Resource Manager (Criar um cluster do Service Fabric com o Azure Resource Manager)](service-fabric-cluster-creation-via-arm.md)
* [Understand the Service Fabric application model (Compreender o modelo de aplicações do Service Fabric)](service-fabric-application-model.md)
* [Utilizar a CLI do Service Fabric para gerir as aplicações](service-fabric-application-lifecycle-sfctl.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

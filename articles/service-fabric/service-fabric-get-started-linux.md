---
title: Configurar o ambiente de desenvolvimento no Linux | Microsoft Docs
description: "Instale o runtime e o SDK e crie um cluster de desenvolvimento local no Linux. Depois de concluir esta configuração, estará pronto a criar aplicações."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 5840932d79ef9fd99a94eb6ae4e587b0e616065e
ms.contentlocale: pt-pt
ms.lasthandoff: 07/01/2017


---
<a id="prepare-your-development-environment-on-linux" class="xliff"></a>

# Preparar o ambiente de desenvolvimento no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Para implementar e executar [aplicações do Azure Service Fabric](service-fabric-application-model.md) no seu computador de desenvolvimento Linux, instale o runtime e o SDK comum. Também pode instalar SDKs opcionais para Java e .NET Core.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos

<a id="supported-operating-system-versions" class="xliff"></a>

### Versões de sistema operativo com suporte
As seguintes versões do sistema operativo são suportadas para desenvolvimento:

* Ubuntu 16.04 (`Xenial Xerus`)

<a id="update-your-apt-sources" class="xliff"></a>

## Atualizar as origens de apt
Para instalar o SDK e o pacote de runtime associado através de apt-get, primeiro tem de atualizar as origens de apt.

1. Abra um terminal.
2. Adicione o repositório do Service Fabric à lista de origens.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Adicione o repositório `dotnet` à lista de origens.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Adicione a chave de GPG nova ao porta-chaves de apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Adicione a chave de GPG oficial do Docker ao porta-chaves de apt.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Configure o repositório do Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Atualize as listas de pacotes com base nos repositórios adicionados recentemente.

    ```bash
    sudo apt-get update
    ```

<a id="install-and-set-up-the-sdk-for-containers-and-guest-executables" class="xliff"></a>

## Instalar e configurar o SDK para contentores e executáveis do convidado

Assim que as origens forem atualizadas, pode instalar o SDK.

1. Instale o pacote do SDK do Service Fabric. É-lhe pedido para confirmar a instalação e aceitar um contrato de licença.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   Os comandos seguintes automatizam a aceitação da licença para pacotes do Service Fabric:
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    >   ```
    
2. Execute o script de configuração do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Depois de executar os passos para instalar o pacote de SDK Comum, pode criar aplicações com serviços de convidados executáveis ou de contentor com o `yo azuresfguest` ou o `yo azuresfcontainer`. Poderá ter de definir a variável do ambiente **$NODE_PATH** para onde se encontram os módulos do nó. 


```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
```

Se estiver a utilizar o ambiente como raiz, poderá ter de definir a variável com o seguinte comando:

```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
```


> [!TIP]
> Poderá adicionar estes comandos ao seu ficheiro ~/.bashrc, para que não tenha de definir a variável de ambiente em cada início de sessão.
>

<a id="setup-the-xplat-service-fabric-cli" class="xliff"></a>

## Configurar o XPlat CLI do Service Fabric
O [XPlat CLI][azure-xplat-cli-github] inclui comandos para interagir com entidades do Service Fabric, incluindo clusters e aplicações. Baseia-se em Node.js, pelo que deve [confirmar que tem o Node instalado][install-node] antes de avançar para as instruções seguintes:

1. Clone o repositório do github para o computador de desenvolvimento.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Mude para o repositório clonado e instale as dependências da CLI com o Node Package Manager (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Crie um symlink a partir da pasta `bin/azure` do repositório clonado para `/usr/bin/azure`.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Por fim, ative os comandos de preenchimento automático do Service Fabric.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

<a id="setup-azure-cli-20" class="xliff"></a>

### Configurar a CLI 2.0 do Azure

Como alternativa ao XPlat CLI, existe agora um módulo de comandos do Service Fabric incluído na CLI do Azure.

Para obter mais informações sobre como instalar a CLI 2.0 do Azure e utilizar os comandos do Service Fabric, veja a [documentação de introdução](service-fabric-azure-cli-2-0.md).

<a id="set-up-a-local-cluster" class="xliff"></a>

## Configurar um cluster local
Se tudo tiver sido instalado corretamente, deverá conseguir iniciar um cluster local.

1. Execute o script de configuração do cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Abra um browser e navegue para http://localhost:19080/Explorer. Se o cluster tiver sido iniciado, deverá ver o dashboard do Service Fabric Explorer.

    ![Service Fabric Explorer no Linux][sfx-linux]

Nesta fase, pode implementar pacotes de aplicações do Service Fabric pré-configurados ou novos com base em contentores convidados ou em executáveis convidados. Para criar novos serviços com os SDKs Java ou .NET Core, siga os passos de configuração opcionais apresentados nas secções seguintes.


> [!NOTE]
> Não são suportados clusters autónomos em Linux - na pré-visualização, apenas são suportados clusters de uma caixa e clusters de vários computadores Azure Linux.
>

<a id="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models" class="xliff"></a>

## Instalar o SDK do Java (opcional, se pretender utilizar os modelos de programação do Java)
O Java SDK fornece as bibliotecas e modelos necessários para criar serviços do Service Fabric com Java.

1. Instale o pacote do Java SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Execute o script de configuração do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

<a id="install-the-eclipse-neon-plugin-optional" class="xliff"></a>

## Instalar o plug-in Eclipse Neon (opcional)

Pode instalar o plug-in do Eclipse para o Service Fabric a partir do **IDE do Eclipse para Programadores de Java**. Pode utilizar o Eclipse para criar aplicações executáveis de convidado do Service Fabric e aplicações de contentor, além de aplicações Java do Service Fabric.

> [!NOTE]
> O SDK do Java é um pré-requisito para utilizar o plug-in do Eclipse, mesmo que o utilize apenas para aplicações de contentor e executáveis de convidado.
>

1. No Eclipse, certifique-se de que tem as versões mais recentes do Eclipse **Neon** e do Buildship (versão 1.0.17 ou posterior) instaladas. Para verificar as versões dos componentes instalados, escolha **Ajuda > Detalhes da Instalação**. Pode atualizar o Buildship ao seguir as instruções fornecidas [aqui][buildship-update].
2. Para instalar o plug-in do Service Fabric, selecione **Ajuda > Instalar Novo Software...**
3. Na caixa de texto "Trabalhar com", introduza http://dl.microsoft.com/eclipse
4. Clique em Adicionar.

    ![Plug-in do Eclipse][sf-eclipse-plugin]

5. Escolha o plug-in do Service Fabric e clique em **Seguinte**.
6. Continue a instalação e aceite o contrato de licença de utilizador final.

Se já tiver o plug-in do Eclipse para o Service Fabric instalado, confirme que tem a versão mais recente. Pode verificar ao selecionar ``Help => Installation Details`` e procurar o Service Fabric na lista dos plug-ins instalados. Selecione a atualização se estiver disponível uma versão mais recente.

Para obter mais informações, veja [Service fabric getting started with Eclipse ](service-fabric-get-started-eclipse.md) (Introdução ao Service Fabric com o Eclipse).


<a id="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models" class="xliff"></a>

## Instalar o SDK do .NET Core (opcional, se pretender utilizar os modelos de programação do .NET Core)
O .NET Core SDK fornece as bibliotecas e os modelos necessários para criar serviços do Service Fabric com .NET Core.

1. Instale o pacote do NET Core SDK.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Execute o script de configuração do SDK.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

<a id="updating-the-sdk-and-runtime" class="xliff"></a>

## Atualizar o SDK e o Runtime

Para atualizar para a versão mais recente do SDK e do runtime, execute os seguintes comandos (desmarque os SDKs que não quer):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
```


> [!NOTE]
> Atualizar os pacotes poderá resultar na paragem do cluster de desenvolvimento local. Reinicie o cluster local após uma atualização ao seguir as instruções nesta página.

<a id="next-steps" class="xliff"></a>

## Passos seguintes
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux (Criar a sua primeira aplicação CSharp no Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX (Preparar o ambiente de desenvolvimento no OSX)](service-fabric-get-started-mac.md)
* [Use the XPlat CLI to manage your Service Fabric applications](service-fabric-azure-cli.md) (Utilizar o XPlat CLI para gerir as aplicações do Service Fabric)
* [Diferenças do Service Fabric no Windows/Linux](service-fabric-linux-windows-differences.md)

<a id="related-articles" class="xliff"></a>

## Artigos relacionados

* [Introdução ao Service Fabric com a CLI 2.0 do Azure](service-fabric-azure-cli-2-0.md)
* [Introdução ao XPlat CLI do Service Fabric](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png


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
ms.date: 03/23/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d01e141ec8ee8da18d38a216f3b13c88f3632801
ms.contentlocale: pt-pt
ms.lasthandoff: 04/27/2017


---
# <a name="prepare-your-development-environment-on-linux"></a>Preparar o ambiente de desenvolvimento no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Para implementar e executar [aplicações do Azure Service Fabric](service-fabric-application-model.md) no seu computador de desenvolvimento Linux, instale o runtime e o SDK comum. Também pode instalar SDKs opcionais para Java e .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-operating-system-versions"></a>Versões de sistema operativo com suporte
As seguintes versões do sistema operativo são suportadas para desenvolvimento:

* Ubuntu 16.04 (i**"Xenial Xerus"**)

## <a name="update-your-apt-sources"></a>Atualizar as origens de apt
Para instalar o SDK e o pacote de runtime associado através de apt-get, primeiro tem de atualizar as origens de apt.

1. Abra um terminal.
2. Adicione o repositório do Service Fabric à lista de origens.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Adicione o repositório **dotnet** à lista de origens.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```
4. Adicione a chave de GPG nova ao porta-chaves de apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Atualize as listas de pacotes com base nos repositórios adicionados recentemente.

    ```bash
    sudo apt-get update
    ```
## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Instalar e configurar o SDK para contentores e executáveis do convidado
Assim que as origens forem atualizadas, pode instalar o SDK.

1. Instale o pacote do SDK do Service Fabric. É-lhe pedido para confirmar a instalação e aceitar um contrato de licença.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
    Para automatizar a instalação pode ignorar o contrato de licença apresentado por definição as seleções de debconf para os pacotes do Service Fabric. Os seguintes dois comandos podem ser executados
    
    ```bash
    echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    ```

2. Execute o script de configuração do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Depois de executar os passos para instalar o pacote de SDK Comum, pode criar aplicações com serviços de convidados executáveis ou de contentor com o `yo azuresfguest`. Poderá ter de definir a variável do ambiente **$NODE_PATH** para onde se encontram os módulos do nó. 

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

## <a name="set-up-the-azure-cross-platform-cli"></a>Configurar a CLI do Azure de várias plataformas
A [CLI do Azure de várias plataformas][azure-xplat-cli-github] inclui comandos para interagir com entidades do Service Fabric, incluindo clusters e aplicações. Baseia-se em Node.js, pelo que deve [confirmar que tem o Node instalado][install-node] antes de avançar para as instruções seguintes:

1. Clone o repositório do github para o computador de desenvolvimento.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. Mude para o repositório clonado e instale as dependências da CLI com o Node Package Manager (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. Crie um symlink a partir da pasta bin/azure do repositório clonado para /usr/bin/azure, para que seja adicionado ao seu caminho e que os comandos estejam disponíveis em qualquer diretório.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. Por fim, ative os comandos de preenchimento automático do Service Fabric.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> Os comandos do Service Fabric ainda não estão disponíveis na CLI 2.0 do Azure.


## <a name="set-up-a-local-cluster"></a>Configurar um cluster local
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

## <a name="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models"></a>Instalar o SDK do Java (opcional, se pretender utilizar os modelos de programação do Java)
O Java SDK fornece as bibliotecas e modelos necessários para criar serviços do Service Fabric com Java.

1. Instale o pacote do Java SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Execute o script de configuração do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```
## <a name="install-the-eclipse-neon-plugin-optional"></a>Instalar o plug-in Eclipse Neon (opcional)

Pode instalar o plug-in do Eclipse para o Service Fabric a partir do **IDE do Eclipse para Programadores de Java**. Pode utilizar o Eclipse para criar aplicações executáveis de convidado do Service Fabric e aplicações de contentor, além de aplicações Java do Service Fabric.

> [!NOTE]
> Instalar o SDK do Java é um pré-requisito para utilizar o plug-in do Eclipse, mesmo que o utilize apenas para criar e implementar aplicações de contentor e executáveis de convidado.
>

1. No Eclipse, certifique-se de que tem as versões mais recentes do Eclipse **Neon** e do Buildship (versão 1.0.17 ou posterior) instaladas. Para verificar as versões dos componentes instalados, escolha **Ajuda > Detalhes da Instalação**. Pode atualizar o Buildship ao seguir as instruções fornecidas [aqui][buildship-update].
2. Para instalar o plug-in do Service Fabric, selecione **Ajuda > Instalar Novo Software...**
3. Na caixa de texto "Trabalhar com", introduza http://dl.windowsazure.com/eclipse/servicefabric
4. Clique em Adicionar.
    ![Plug-in do Eclipse][sf-eclipse-plugin]
5. Escolha o plug-in do Service Fabric e clique em “Seguinte”.
6. Continue a instalação e aceite o contrato de licença de utilizador final.

Se já tiver o plug-in do Eclipse para o Service Fabric instalado, confirme que tem a versão mais recente. Pode verificar ao selecionar ``Help => Installation Details`` e procurar o Service Fabric na lista dos plug-ins instalados. Selecione a atualização se estiver disponível uma versão mais recente. 

Para mais informações, consulte [Service fabric getting started with eclipse (Introdução ao Service Fabric com o eclipse)](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models"></a>Instalar o SDK do .NET Core (opcional, se pretender utilizar os modelos de programação do .NET Core)
O NET Core SDK fornece as bibliotecas e modelos necessários para criar serviços do Service Fabric com NET Core de várias plataformas.

1. Instale o pacote do NET Core SDK.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Execute o script de configuração do SDK.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>Atualizar o SDK e o Runtime

Para atualizar para a versão mais recente do SDK e do runtime, execute os seguintes passos (remova os SDKs da lista que não pretende atualizar ou instalar):

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
   ```
   
> [!NOTE]
> Atualizar os pacotes acima poderá resultar na paragem do desenvolvimento do cluster local. Reinicie o cluster local após uma atualização seguindo as instruções nesta página
>
>

Para atualizar a CLI, navegue para o diretório onde clonou a CLI e execute `git pull` para atualizar.  Se forem necessários passos adicionais para atualizar, as notas de versão irão especificar esses passos. 


## <a name="next-steps"></a>Passos seguintes
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux (Criar a sua primeira aplicação CSharp no Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX (Preparar o ambiente de desenvolvimento no OSX)](service-fabric-get-started-mac.md)
* [Use the Azure CLI to manage your Service Fabric applications (Utilizar a CLI do Azure para gerir as aplicações do Service Fabric)](service-fabric-azure-cli.md)
* [Diferenças do Service Fabric no Windows/Linux](service-fabric-linux-windows-differences.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png


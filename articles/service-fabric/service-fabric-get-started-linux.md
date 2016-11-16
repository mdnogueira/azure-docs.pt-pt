---
title: Configurar o ambiente de desenvolvimento no Linux | Microsoft Docs
description: "Instale o runtime e o SDK e crie um cluster de desenvolvimento local no Linux. Depois de concluir esta configuração, estará pronto a criar aplicações."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 567a998102558626df73878865b317b830ba1faa


---
# <a name="prepare-your-development-environment-on-linux"></a>Preparar o ambiente de desenvolvimento no Linux
> [!div class="op_single_selector"]
> -[ Windows](service-fabric-get-started.md)
> 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Para implementar e executar [aplicações do Azure Service Fabric](service-fabric-application-model.md) no seu computador de desenvolvimento Linux, instale o runtime e o SDK comum. Também pode instalar SDKs opcionais para Java e .NET Core.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistema operativo com suporte
As seguintes versões do sistema operativo são suportadas para desenvolvimento:

* Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>Atualizar as origens de apt
Para instalar o SDK e o pacote de runtime associado através de apt-get, primeiro tem de atualizar as origens de apt.

1. Abra um terminal.
2. Adicione o repositório do Service Fabric à lista de origens.
   
    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Adicione a chave de GPG nova ao porta-chaves de apt.
   
    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
4. Atualize as listas de pacotes com base nos repositórios adicionados recentemente.
   
    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Instalar e configurar o SDK
Assim que as origens forem atualizadas, pode instalar o SDK.

1. Instale o pacote do SDK do Service Fabric. Ser-lhe-á pedido para confirmar a instalação e aceitar um contrato de licença.
   
    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. Execute o script de configuração do SDK.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-crossplatform-cli"></a>Configurar a CLI do Azure de várias plataformas
A [CLI do Azure de várias plataformas][azure-xplat-cli-github] inclui comandos para interagir com entidades do Service Fabric, incluindo clusters e aplicações. Baseia-se em Node.js, pelo que deve [confirmar que tem o Node instalado][install-node] antes de avançar para as instruções abaixo.

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

## <a name="set-up-a-local-cluster"></a>Configurar um cluster local
Se tudo tiver sido instalado corretamente, deverá conseguir iniciar um cluster local.

1. Execute o script de configuração do cluster.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Abra um browser e navegue para http://localhost:19080/Explorer. Se o cluster tiver sido iniciado, deverá ver o dashboard do Service Fabric Explorer.
   
    ![Service Fabric Explorer no Linux][sfx-linux]

Nesta fase, pode implementar pacotes de aplicações do Service Fabric pré-configurados ou novos com base em contentores convidados ou em executáveis convidados. Para criar novos serviços através de SDKs Java ou .NET Core, siga os passos de configuração opcionais abaixo.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Instalar o Java SDK e o plug-in Eclipse Neon (opcional)
O Java SDK fornece as bibliotecas e modelos necessários para criar serviços do Service Fabric com Java.

1. Instale o pacote do Java SDK.
   
    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Execute o script de configuração do SDK.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Pode instalar o plug-in do Eclipse para o Service Fabric a partir do IDE do Eclipse Neon.

1. No Eclipse, confirme que tem a versão 1.0.17 ou posterior do Buildship instalada. Para verificar as versões dos componentes instalados, escolha **Ajuda > Detalhes da Instalação**. Pode atualizar o Buildship ao seguir as instruções fornecidas [aqui][buildship-update].
2. Para instalar o plug-in do Service Fabric, selecione **Ajuda > Instalar Novo Software...**
3. Na caixa de texto "Trabalhar com", introduza http://dl.windowsazure.com/eclipse/servicefabric
4. Clique em Adicionar.
   
    ![Plug-in do Eclipse][sf-eclipse-plugin]
5. Escolha o plug-in do Service Fabric e clique em “Seguinte”.
6. Continue a instalação e aceite o contrato de licença de utilizador final.

## <a name="install-the-net-core-sdk-optional"></a>Instalar o .NET Core SDK (opcional)
O NET Core SDK fornece as bibliotecas e modelos necessários para criar serviços do Service Fabric com NET Core de várias plataformas.

1. Instale o pacote do NET Core SDK.
   
    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```
2. Execute o script de configuração do SDK.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Passos seguintes
* [Create your first Java application on Linux (Criar a sua primeira aplicação Java no Linux)](service-fabric-create-your-first-linux-application-with-java.md)
* [Prepare your development environment on OSX (Preparar o ambiente de desenvolvimento no OSX)](service-fabric-get-started-mac.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png



<!--HONumber=Nov16_HO2-->



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
ms.date: 9/19/2017
ms.author: subramar
ms.openlocfilehash: da9aff17c16e179be200677bfbfd1287fff269e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-your-development-environment-on-linux"></a>Preparar o ambiente de desenvolvimento no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Para implementar e executar [aplicações do Azure Service Fabric](service-fabric-application-model.md) no seu computador de desenvolvimento Linux, instale o runtime e o SDK comum. Também pode instalar SDKs opcionais para programação em Java e .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

As seguintes versões do sistema operativo são suportadas para desenvolvimento:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="installation-methods"></a>Métodos de Instalação

### <a name="1-script-installation"></a>1. Instalação com script

Um script é fornecido para sua comodidade, para instalar o runtime do Service Fabric e o SDK comum do Service Fabric juntamente com a CLI **sfctl**. Execute os passos de instalação manual na secção seguinte para determinar o que está a ser instalado e as licenças que estão a ser aceites. A execução do script parte do princípio de que concorda com as licenças de todo o software que está a ser instalado. 

Após a execução com êxito do script, pode avançar diretamente para [Configurar um cluster local](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2. Instalação Manual
Para instalar manualmente o runtime do Service Fabric e o SDK comum, siga o resto deste guia.

## <a name="update-your-apt-sources"></a>Atualizar as origens do APT
Para instalar o SDK e o pacote de runtime associado através da ferramenta de linha de comandos apt-get, primeiro tem de atualizar as origens do Advanced Packaging Tool (APT).

1. Abra um terminal.
2. Adicione o repositório do Service Fabric à lista de origens.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Adicione o repositório `dotnet` à lista de origens.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Adicione a chave nova do Gnu Privacy Guard (GnuPG ou GPG) ao porta-chaves do APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Adicione a chave de GPG oficial do Docker ao porta-chaves do APT.

    ```bash
    sudo apt-get install curl
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

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>Instalar e configurar o SDK do Service Fabric para a configuração do cluster local

Depois de atualizar as origens, pode instalar o SDK. Instale o pacote do SDK do Service Fabric, confirme a instalação e aceite o contrato de licenciamento.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Os comandos seguintes automatizam a aceitação da licença para pacotes do Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>Configurar um cluster local
  Quando a instalação estiver concluída, deverá conseguir iniciar um cluster local.

  1. Execute o script de configuração do cluster.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Abra um browser e aceda a [Service Fabric Explorer](http://localhost:19080/Explorer). Se o cluster tiver sido iniciado, deverá ver o dashboard do Service Fabric Explorer.

      ![Service Fabric Explorer no Linux][sfx-linux]

  Nesta fase, pode implementar pacotes de aplicações do Service Fabric pré-configurados ou novos com base em contentores convidados ou em executáveis convidados. Para criar novos serviços com os SDKs Java ou .NET Core, siga os passos de configuração opcionais apresentados nas secções seguintes.


  > [!NOTE]
  > O Linux não suporta clusters autónomos.
  >

## <a name="set-up-the-service-fabric-cli"></a>Configurar a CLI do Service Fabric

A [CLI do Service Fabric](service-fabric-cli.md) inclui comandos para interagir com entidades do Service Fabric, incluindo clusters e aplicações.
Siga as instruções em [Service Fabric CLI](service-fabric-cli.md) (CLI do Service Fabric) para instalar a CLI.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Instalar geradores do Yeoman para contentores e executáveis convidados
O Service Fabric fornece ferramentas estruturais que o irão ajudar a criar aplicações do Service Fabric a partir de um terminal com os geradores de modelos Yeoman. Siga estes passos para configurar os geradores de modelos Yeoman do Service Fabric:

1. Instalar nodejs e NPM no seu computador

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) no seu computador a partir do NPM

  ```bash
  sudo npm install -g yo
  ```
3. Instalar o gerador de contentor Yeo do Service Fabric e o gerador executável convidado a partir do NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Depois de instalar os geradores, deverá conseguir criar serviços de contentor ou executáveis convidados ao executar `yo azuresfguest` ou `yo azuresfcontainer`, respetivamente.

## <a name="set-up-net-core-20-development"></a>Configurar o desenvolvimento em .NET Core 2.0

Instale o [.NET Core 2.0 SDK for Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) para começar a [criar aplicações do Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes para aplicações do Service Fabric do .NET Core 2.0 são alojados no NuGet.org, atualmente em pré-visualização.

## <a name="set-up-java-development"></a>Configurar o desenvolvimento em Java

Para criar serviços do Service Fabric com o Java, instale o JDK 1.8 e o Gradle para executar tarefas de compilação. O fragmento seguinte instala o Open JDK 1.8 juntamente com o Gradle. As bibliotecas Java do Service Fabric são extraídas do Maven.

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Instalar o plug-in Eclipse Neon (opcional)

Pode instalar o plug-in do Eclipse para o Service Fabric a partir do IDE do Eclipse para Programadores de Java. Pode utilizar o Eclipse para criar aplicações executáveis de convidado do Service Fabric e aplicações de contentor, além de aplicações Java do Service Fabric.

1. No Eclipse, certifique-se de que tem as versões mais recentes do Eclipse Neon e do Buildship (versão 1.0.17 ou posterior) instaladas. Para verificar as versões dos componentes instalados, escolha **Ajuda** > **Detalhes da Instalação**. Pode atualizar o Buildship com as instruções apresentadas em [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Plug-ins do Eclipse para Gradle).

2. Para instalar o plug-in do Service Fabric, selecione **Ajuda** > **Instalar Novo Software**.

3. Na caixa **Trabalhar com**, escreva **http://dl.microsoft.com/eclipse**.

4. Clique em **Adicionar**.

    ![A página Software Disponível][sf-eclipse-plugin]

5. Selecione o plug-in **ServiceFabric** e clique em **Seguinte**.

6. Conclua os passos da instalação e aceite o contrato de licença de utilizador final.

Se já tiver o plug-in do Service Fabric para o Eclipse instalado, confirme que tem a versão mais recente. Pode verificar ao selecionar **Ajuda** > **Detalhes da Instalação** e procurar o Service Fabric na lista dos plug-ins instalados. Se estiver disponível uma versão mais recente, selecione **Atualizar**.

Para obter mais informações, veja [Plug-in do Service Fabric para desenvolvimento de aplicações Java de Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Atualizar o SDK e o runtime

Para atualizar para a versão mais recente do SDK e do runtime, execute os seguintes comandos:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Para atualizar os binários SDK do Java a partir do Maven, tem de atualizar os detalhes da versão do binário correspondente no ficheiro ``build.gradle``, para que apontem para a versão mais recente. Para saber exatamente onde é necessário atualizar a versão, pode consultar qualquer ficheiro ``build.gradle`` nos exemplos de introdução ao Service Fabric, [aqui](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Atualizar os pacotes pode fazer com que a execução do cluster de desenvolvimento local pare. Reinicie o cluster local após uma atualização, seguindo as instruções nesta página.

## <a name="remove-the-sdk"></a>Remover o SDK
Para remover os SDKs do Service Fabric, execute o seguinte:

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```

## <a name="next-steps"></a>Passos seguintes

* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux (Criar a sua primeira aplicação CSharp no Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX (Preparar o ambiente de desenvolvimento no OSX)](service-fabric-get-started-mac.md)
* [Utilizar a CLI do Service Fabric para gerir as aplicações](service-fabric-application-lifecycle-sfctl.md)
* [Diferenças do Service Fabric no Windows/Linux](service-fabric-linux-windows-differences.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

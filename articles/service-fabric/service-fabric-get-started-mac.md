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
ms.date: 09/26/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: c447a92e076bacc9b208b837493400b70cd067e1
ms.contentlocale: pt-pt
ms.lasthandoff: 09/26/2017

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
O Service Fabric não é executado de forma nativa no OS X. Para executar um cluster do Service Fabric local, fornecemos uma máquina virtual do Ubuntu pré-configurada ao utilizar o Vagrant e do VirtualBox. Antes de começar, vai precisar do:

* [Vagrant (v1.8.4 ou posterior)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> Tem de utilizar versões do Vagrant e do VirtualBox mutuamente suportadas. O Vagrant pode ter um comportamento errático numa versão do VirtualBox não suportada.
>

## <a name="create-the-local-vm"></a>Criar a VM local
Para criar a VM local que contenha um cluster do Service Fabric de cinco nós, execute os seguintes passos:

1. Clonar o repositório `Vagrantfile`

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    Estes passos servem para transferir o ficheiro `Vagrantfile` que contém a configuração da VM, juntamente com a localização de onde a VM foi transferida.  O ficheiro aponta para uma imagem do Ubuntu de cotação.

2. Navegue para o clone local do repositório

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Opcional) Modifique as predefinições da VM

    Por predefinição, a VM local está configurada da seguinte forma:

   * 3 GB de memória atribuída
   * Rede de anfitrião privada configurada no IP 192.168.50.50 , permitindo pass-through do tráfego a partir do anfitrião Mac

     Pode alterar qualquer uma destas definições ou adicionar outra configuração à VM no `Vagrantfile`. Veja a [documentação do Vagrant](http://www.vagrantup.com/docs) para obter a lista completa das opções de configuração.
4. Crie a VM

    ```bash
    vagrant up
    ```


5. Iniciar sessão na VM e instalar o SDK do Service Fabric

    ```bash
    vagrant ssh
    ```

   Instale o SDK, conforme descrito em [Instalação do SDK](service-fabric-get-started-linux.md).  O script abaixo é fornecido para sua comodidade, para instalar o runtime do Service Fabric e o SDK comum do Service Fabric juntamente com a CLI sfctl. A execução do script parte do princípio de que leu e concordou com as licenças de todo o software que está a ser instalado.

    ```bash
    sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
    ```

5.  Iniciar o cluster do Service Fabric

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    >[!TIP]
    > Se a transferência da VM estiver a demorar muito tempo, pode transferi-la com o wget ou o curl, ou através de um browser ao navegar até à ligação especificada por **config.vm.box_url** no ficheiro `Vagrantfile`. Depois de transferi-la localmente, edite `Vagrantfile` para apontar para o caminho local onde transferiu a imagem. Por exemplo, se transferiu a imagem para /home/users/test/azureservicefabric.tp8.box, defina **config.vm.box_url** para esse caminho.
    >

5. Teste se o cluster foi configurado corretamente ao navegar para o Service Fabric Explorer, em http://192.168.50.50:19080/Explorer (partindo do princípio de que manteve o IP de rede privado predefinido).

    ![Service Fabric Explorer visto no Mac anfitrião][sfx-mac]

## <a name="install-the-necessary-java-artifacts-on-vagrant-to-use-service-fabric-java-programming-model"></a>Instalar os artefactos Java necessários no Vagrant para utilizar os modelos de programação Service Fabric do Java

Para criar serviços do Service Fabric com o Java, certifique-se de que tem o JDK 1.8 instalado juntamente com o Gradle que é utilizado para executar tarefas de compilação. O fragmento seguinte instala o Open JDK 1.8 juntamente com o Gradle. As bibliotecas Java do Service Fabric são extraídas do Maven.

  ```bash
  vagrant ssh
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
```

## <a name="set-up-the-service-fabric-cli"></a>Configurar a CLI do Service Fabric

A [CLI do Service Fabric](service-fabric-cli.md) inclui comandos para interagir com entidades do Service Fabric, incluindo clusters e aplicações. Baseia-se no python, por isso, certifique-se de que tem o python e o pip instalados antes de continuar com o seguinte comando:

```bash
pip install sfctl
```

## <a name="create-application-on-mac-using-yeoman"></a>Criar aplicação no Mac com o Yeoman
O Service Fabric fornece ferramentas estruturais que irão ajudá-lo a criar uma aplicação do Service Fabric a partir do terminal, através do gerador de modelos Yeoman. Siga os passos abaixo para certificar-se de que tem o gerador de modelos yeoman do Service Fabric operacional no seu computador.

1. Tem de ter o Node.js e NPM instalados no mac. Se não for possível instalar o Node.js e NPM através do Homebrew, utilize o seguinte. Para verificar as versões do Node.js e NPM instaladas no Mac, utilize a opção ``-v``.

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) no seu computador a partir do NPM

  ```bash
  npm install -g yo
  ```
3. Instale o gerador Yeoman que pretende utilizar, seguindo os passos na [documentação](service-fabric-get-started-linux.md) da introdução. Para criar Aplicações do Service Fabric com o Yeoman, siga os passos -

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. Para criar uma aplicação Java do Service Fabric no Mac, terá de ter - JDK 1.8 e Gradle instalados no computador.

## <a name="set-up-net-core-20-development"></a>Configurar o desenvolvimento em .NET Core 2.0

Instale o [.NET Core 2.0 SDK for Mac](https://www.microsoft.com/net/core#macos) para começar a [criar aplicações do Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes para aplicações do Service Fabric do .NET Core 2.0 são alojados no NuGet.org, atualmente em pré-visualização.


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Instalar o plug-in do Service Fabric para Eclipse Neon

O Service Fabric fornece um plug-in para o **IDE Eclipse Neon para Java** que pode simplificar o processo de criação e implementação de serviços Java. Pode seguir os passos de instalação mencionados nesta [documentação](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) geral sobre a instalação ou atualização do plug-in do Eclipse para o Service Fabric.

>[!TIP]
> Por predefinição, suportamos o IP predefinido, conforme mencionado no ``Vagrantfile``, no ``Local.json`` da aplicação gerada. No caso de o alterar e implementar o Vagrant com um IP diferente, atualize também o IP correspondente no ``Local.json`` da sua aplicação.

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


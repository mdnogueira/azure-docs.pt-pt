---
title: Configurar o ambiente de desenvolvimento no Mac OS X | Microsoft Docs
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
ms.date: 04/06/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e5d14eb0a656d67030f4c0d3d510aec0e9cafae7
ms.lasthandoff: 03/28/2017


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
    Estes passos trazem o ficheiro `Vagrantfile` que contém a configuração da VM, juntamente com a localização de onde a VM foi transferida.


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

   Este passo transfere a imagem da VM pré-configurada, arranca-a localmente e, depois, configura um cluster do Service Fabric local na mesma. Esta operação pode demorar alguns minutos. Se a configuração for concluída com êxito, verá uma mensagem no resultado que indica que o cluster está a ser iniciado.

    ![Início da configuração do cluster a seguir ao aprovisionamento da VM][cluster-setup-script]

>[!TIP]
> Se a transferência da VM estiver a demorar muito tempo, pode transferi-la com o wget ou o curl, ou através de um browser ao navegar até à ligação especificada por **config.vm.box_url** no ficheiro `Vagrantfile`. Depois de transferi-la localmente, edite `Vagrantfile` para apontar para o caminho local onde transferiu a imagem. Por exemplo, se transferiu a imagem para /home/users/test/azureservicefabric.tp8.box, defina **config.vm.box_url** para esse caminho.
>

5. Teste se o cluster foi configurado corretamente ao navegar para o Service Fabric Explorer, em http://192.168.50.50:19080/Explorer (partindo do princípio de que manteve o IP de rede privado predefinido).

    ![Service Fabric Explorer visto no Mac anfitrião][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Instalar o plug-in do Service Fabric para Eclipse Neon

O Service Fabric fornece um plug-in para o **IDE Eclipse Neon para Java** que pode simplificar o processo de criação e implementação de serviços Java. Pode seguir os passos de instalação mencionados nesta [documentação](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) geral sobre a instalação ou atualização do plug-in do Eclipse para o Service Fabric.

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Utilizar o plug-in do Eclipse para o Service Fabric no Mac

Certifique-se de que seguiu os passos mencionados na [documentação do plug-in do Eclipse para o Service Fabric](service-fabric-get-started-eclipse.md). Os passos para criar e implementar a aplicação Java do Service Fabric com o contentor de convidado vagrant num anfitrião Mac, são praticamente os mesmos da documentação geral, exceto alguns itens seguintes:

* Uma vez que as bibliotecas do Service Fabric são requeridas pela aplicação Java do Service Fabric, o projeto eclipse tem de ser criado num caminho partilhado. Por predefinição, o conteúdo no caminho no anfitrião onde o ``Vagrantfile`` existe, é partilhado com o ``/vagrant`` caminho no convidado.
* Se tiver o ``Vagrantfile`` num caminho, por exemplo, ``~/home/john/allprojects/``, tem de criar o seu projeto Service Fabric ``MyActor`` numa localização ``~/home/john/allprojects/MyActor`` e o caminho para a área de trabalho eclipse seria ``~/home/john/allprojects``.

## <a name="next-steps"></a>Passos seguintes
<!-- Links -->
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Create a Service Fabric cluster in the Azure portal (Criar um cluster do Service Fabric no portal do Azure)](service-fabric-cluster-creation-via-portal.md)
* [Create a Service Fabric cluster using the Azure Resource Manager (Criar um cluster do Service Fabric com o Azure Resource Manager)](service-fabric-cluster-creation-via-arm.md)
* [Understand the Service Fabric application model (Compreender o modelo de aplicações do Service Fabric)](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship


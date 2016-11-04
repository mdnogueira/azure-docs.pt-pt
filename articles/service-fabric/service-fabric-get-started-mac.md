---
title: Configurar o ambiente de desenvolvimento no Mac OS X | Microsoft Docs
description: Instale o runtime, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir esta configuração, estará pronto para criar aplicações no Mac OS X.
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: seanmck

---
# Configurar o ambiente de desenvolvimento no Mac OS X
> [!div class="op_single_selector"]
> -[ Windows](service-fabric-get-started.md)
> 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Pode criar aplicações do Service Fabric para serem executadas em clusters do Linux através do Mac OS X. Este artigo descreve como configurar o seu Mac para desenvolvimento.

## Pré-requisitos
O Service Fabric não é executado de forma nativa no OS X. Para executar um cluster do Service Fabric local, fornecemos uma máquina virtual do Ubuntu pré-configurada ao utilizar o Vagrant e do VirtualBox. Antes de começar, vai precisar do:

* [Vagrant (v1.8.4 ou posterior)](http://wwww.vagrantup.com/downloads)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## Criar a VM local
Para criar a VM local que contenha um cluster do Service Fabric de cinco nós, faça o seguinte:

1. Clone o repositório Vagrantfile
   
    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
2. Navegue para o clone local do repositório
   
    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Opcional) Modifique as predefinições da VM
   
    Por predefinição, a VM local está configurada da seguinte forma:
   
   * 3 GB de memória atribuída
   * Rede de anfitrião privada configurada no IP 192.168.50.50 , permitindo pass-through do tráfego a partir do anfitrião Mac
     
     Pode alterar qualquer uma destas definições ou adicionar outra configuração à VM no Vagrantfile. Veja a [documentação do Vagrant](http://www.vagrantup.com/docs) para obter a lista completa das opções de configuração.
4. Crie a VM
   
    ```bash
    vagrant up
    ```
   
    Este passo transfere a imagem da VM pré-configurada, arranca-a localmente e, depois, configura um cluster do Service Fabric local na mesma. Esta operação pode demorar alguns minutos. Se a configuração for concluída com êxito, verá uma mensagem no resultado que indica que o cluster está a ser iniciado.
   
    ![Início da configuração do cluster a seguir ao aprovisionamento da VM][cluster-setup-script]
5. Teste se o cluster foi configurado corretamente ao navegar para o Service Fabric Explorer, em http://192.168.50.50:19080/Explorer (partindo do princípio de que manteve o IP de rede privado predefinido).
   
    ![Service Fabric Explorer visto no Mac anfitrião][sfx-mac]

## Instalar o plug-in do Service Fabric para Eclipse Neon (opcional)
O Service Fabric fornece um plug-in para o IDE Eclipse Neon que pode simplificar o processo de criação e implementação de serviços Java.

1. No Eclipse, confirme que tem a versão 1.0.17 ou posterior do Buildship instalada. Para verificar as versões dos componentes instalados, escolha **Ajuda > Detalhes da Instalação**. Pode atualizar o Buildship ao seguir as instruções fornecidas [aqui][buildship-update].
2. Para instalar o plug-in do Service Fabric, selecione **Ajuda > Instalar Novo Software...**
3. Na caixa de texto "Trabalhar com", introduza http://dl.windowsazure.com/eclipse/servicefabric.
4. Clique em Adicionar.
   
    ![Plug-in do Eclipse Neon para o Service Fabric][sf-eclipse-plugin-install]
5. Escolha o plug-in do Service Fabric e clique em “Seguinte”.
6. Continue a instalação e aceite o contrato de licença de utilizador final.

## Passos seguintes
* [Create your first Service Fabric application for Linux (Criar a sua primeira aplicação do Service Fabric para Linux)](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

* [Create a Service Fabric cluster in the Azure portal (Criar um cluster do Service Fabric no portal do Azure)](service-fabric-cluster-creation-via-portal.md)
* [Create a Service Fabric cluster using the Azure Resource Manager (Criar um cluster do Service Fabric com o Azure Resource Manager)](service-fabric-cluster-creation-via-arm.md)
* [Understand the Service Fabric application model (Compreender o modelo de aplicações do Service Fabric)](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship



<!--HONumber=Sep16_HO4-->



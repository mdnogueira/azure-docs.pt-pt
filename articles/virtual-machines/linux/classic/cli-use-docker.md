---
title: "Com a extensão VM de Docker para Linux no Azure"
description: "Descreve as extensões de máquinas virtuais do Azure e Docker e mostra como programaticamente criar máquinas virtuais no Azure anfitriões de docker na linha de comandos utilizando a CLI do Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: a542332c921862241f1f000e6a8f0a0ae0e8a934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Utilizar a Extensão VM do Docker a partir da Interface de Linha de Comandos do Azure (CLI do Azure)
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para obter informações sobre como utilizar a extensão de VM de Docker com o modelo Resource Manager, consulte [aqui](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Este tópico descreve como criar uma VM com a extensão de VM de Docker do modo de gestão (asm) serviço na CLI do Azure em qualquer plataforma. [Docker](https://www.docker.com/) é uma das abordagens de Virtualização mais populares, que utiliza [Linux contentores](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolando os dados e computação em recursos partilhados. Pode utilizar a extensão de VM de Docker e [agente Linux do Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para criar uma VM de Docker que aloja qualquer número de contentores para as suas aplicações no Azure. Para ver um debate de alto nível de contentores e as vantagens, consulte o [Whiteboard de nível elevado de Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Como utilizar a extensão de VM de Docker com o Azure
Para utilizar a extensão de VM de Docker com o Azure, tem de instalar uma versão do [Interface de linha de comandos do Azure](https://github.com/Azure/azure-sdk-tools-xplat) (CLI do Azure) ou superior 0.8.6 (como desta redação, a versão atual é 0.10.0). Pode instalar a CLI do Azure no Mac, Linux e Windows.

O processo de conclusão para utilizar o Docker no Azure é simple:

* Instalar a CLI do Azure e as respetivas dependências no computador a partir do qual pretende controlar do Azure (no Windows, esta será uma distribuição de Linux em execução como uma máquina virtual)
* Utilize os comandos de Docker de CLI do Azure para criar um anfitrião de VM Docker no Azure
* Utilize os comandos de Docker locais para gerir os contentores de Docker na VM Docker no Azure.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Instalar o Azure Interface de linha de comandos (CLI do Azure)
Para instalar e configurar a CLI do Azure, consulte o artigo [como instalar a Interface de linha de comandos do Azure](../../../cli-install-nodejs.md). Para confirmar a instalação, introduza `azure` na linha de comandos e após um curto momento, deverá ver o art ASCII de CLI do Azure, que lista os comandos básicos disponíveis para si. Se a instalação trabalhou corretamente, deverá conseguir escreva `azure help vm` e vê que um dos comandos listados "docker".

> [!NOTE]
> Docker tem as ferramentas para Windows, [Docker máquina](https://docs.docker.com/installation/windows/), que também pode utilizar para automatizar a criação de um cliente de docker que pode utilizar para trabalhar com as VMs do Azure como anfitriões de docker.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Ligar a CLI do Azure para a sua conta do Azure
Antes de poder utilizar a CLI do Azure tem de associar as credenciais da conta do Azure com a CLI do Azure na sua plataforma. A secção [como ligar à sua subscrição do Azure](../../../xplat-cli-connect.md) explica como para o transferir e importar o **. publishsettings** de ficheiros ou associar a CLI do Azure com o id organizacional.

> [!NOTE]
> Existem algumas diferenças no comportamento quando utilizar um ou os outros métodos de autenticação, pelo que não se esqueça de ler o documento acima para compreender as diferentes funcionalidades.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Instalar Docker e utilizar a extensão de VM de Docker para o Azure
Siga o [instruções de instalação do Docker](https://docs.docker.com/installation/#installation) para instalar o Docker localmente no seu computador.

Para utilizar o Docker uma Máquina Virtual com o Azure, a imagem do Linux utilizada para a VM tem de ter o [agente de VM Linux do Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) instalado. Atualmente, existem apenas dois tipos de imagens que forneçam esta:

* Uma imagem de Ubuntu na Galeria de imagem do Azure ou
* Uma imagem de Linux personalizada que criou com o agente da VM do Azure Linux instalado e configurado. Consulte [agente de VM Linux do Azure](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre como criar uma VM com Linux personalizada com o agente da VM do Azure.

### <a name="using-the-azure-image-gallery"></a>Utilizar a galeria da imagem do Azure
A partir de um Bash ou sessão de Terminal, utilize o seguinte comando da CLI do Azure para localizar a imagem de Ubuntu mais recente na Galeria VM para utilizar escrevendo

`azure vm image list | grep Ubuntu-14_04`

e selecione um dos nomes de imagens, tais como `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`e utilize o seguinte comando para criar uma nova VM essa imagem a utilizar.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

Em que:

* *&lt;nome de VM cloudservice&gt;*  é o nome da VM que vai ser o computador de anfitrião de contentor do Docker no Azure
* *&lt;nome de utilizador&gt;*  é o nome de utilizador do utilizador raiz predefinido da VM
* *&lt;palavra-passe&gt;*  é a palavra-passe a *username* conta que cumpra as normas de complexidade do Azure

> [!NOTE]
> Atualmente, uma palavra-passe tem de ter, pelo menos, 8 carateres, contêm uma minúscula e uma maiúscula, um número e um caráter especial, tal como um dos seguintes carateres: `!@#$%^&+=`. Não, o período no fim do frase anterior não é um caráter especial.
> 
> 

Se o comando foi concluída com êxito, deverá ver algo semelhante ao seguinte, consoante as opções que utilizou e precisos argumentos:

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> Criar uma máquina virtual pode demorar alguns minutos, mas após ter sido aprovisionado (o valor de estado é `ReadyRole`) o daemon de Docker (o serviço de Docker) é iniciado e pode ligar para o anfitrião de contentor do Docker.
> 
> 

Para testar a VM de Docker que criou no Azure, escreva

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

onde  *&lt;vm----nome&gt;*  é o nome da máquina virtual que utilizou na sua chamada para `azure vm docker create`. Deverá ver algo semelhante ao seguinte, que indica que a VM de anfitriões de Docker está ativo e em execução no Azure e a aguardar que os comandos. 

Agora pode tentar estabelecer ligação utilizando o cliente de docker para obter informações de (em alguns setups de cliente de Docker, tal como no Mac, poderá ter de utilizar `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Apenas para ter a certeza de que está a trabalhar todos os, pode examinar a VM para a extensão de Docker:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Autenticação de VM do anfitrião de docker
Para além de criar a VM de Docker, o `azure vm docker create` comando cria também automaticamente os certificados necessários para permitir que o computador de cliente de Docker para ligar ao anfitrião de contentor do Azure através de HTTPS, e os certificados são armazenados em máquinas de cliente e o anfitrião, conforme apropriado. As tentativas subsequentes, os certificados existentes são reutilizados em partilhado com o novo anfitrião.

Por predefinição, os certificados são colocados em `~/.docker`, e Docker irá ser configurado para ser executado na porta **2376**. Se gostaria de utilizar uma porta diferente ou diretório, em seguida, pode utilizar um dos seguintes `azure vm docker create` opções de linha de comandos para configurar o contentor do Docker alojam a VM para utilizar uma porta diferente ou certificados diferentes para ligação de clientes:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

O daemon de Docker no anfitrião está configurado para escutar e autenticar ligações de cliente na porta especificada utilizando os certificados gerados pelo `azure vm docker create` comando. O computador cliente tem de ter estes certificados para obter acesso para o anfitrião de Docker.

> [!NOTE]
> Um anfitrião em rede em execução sem estes certificados serão vulnerável a qualquer pessoa que utilizar para ligar à máquina. Antes de modificar a configuração predefinida, certifique-se de que compreende os riscos para os seus computadores e aplicações.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* Está pronto para ir para o [Guia do utilizador Docker] e utilizar a VM de Docker. Para criar uma VM preparados para Docker no novo portal, consulte [como utilizar a extensão de VM de Docker com o Portal].
* A extensão da VM do Azure Docker também suporta o Docker Compose, que utiliza um ficheiro YAML declarativo para colocar uma aplicação modelada do programador em qualquer ambiente e gerar uma implementação consistente. Consulte [começar com o Docker e Compose para definir e executar uma aplicação de contentor multi numa máquina virtual do Azure].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[como utilizar a extensão de VM de Docker com o Portal]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guia do utilizador Docker]:https://docs.docker.com/userguide/

[começar com o Docker e Compose para definir e executar uma aplicação de contentor multi numa máquina virtual do Azure]:../docker-compose-quickstart.md

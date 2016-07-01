<properties
    pageTitle="Diferentes formas para criar uma VM com Linux | Microsoft Azure"
    description="Lista as diferentes formas para criar uma máquina virtual com Linux no Azure e fornece ligações para instruções adicionais"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/12/2016"
    ms.author="iainfou"/>

# Diferentes formas para criar uma máquina virtual com Linux com o Resource Manager

O Azure disponibiliza várias formas para criar uma VM utilizando o modelo de implementação do Resource Manager para se adequar aos diferentes utilizadores e efeitos. Este artigo resume estas diferenças e as escolhas que pode fazer para a criação de máquinas virtuais (VMs) com Linux.

## Opções de ferramentas

### Shell de comandos: CLI do Azure 

A partir do CLI, utilize a interface de linha de comandos do Azure. Pode ler mais sobre [como instalar a CLI do Azure](../xplat-cli-install.md) através de npm, contentor de Docker ou script de instalação. Os tutoriais seguintes fornecem exemplos sobre como utilizar a CLI do Azure:

* [Criar uma VM com Linux a partir da CLI do Azure para o desenvolvimento e teste](virtual-machines-linux-quick-create-cli.md) 

* [Criar uma VM com Linux protegida utilizando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Criar uma VM com Linux a partir do zero através da CLI do Azure](virtual-machines-linux-create-cli-complete.md)

### GUI: Portal do Azure

A interface gráfica do utilizador do [Portal do Azure](https://portal.azure.com) é uma forma fácil para experimentar uma VM, especialmente se é inexperiente com o Azure, uma vez que não existe nada para instalar no seu sistema. Utilize o Portal do Azure para criar a VM:

* [Criar uma máquina virtual a executar o Linux através do Portal do Azure](virtual-machines-linux-quick-create-portal.md) 

## Opções de imagem e sistema operativo

Com ambos os métodos, escolhe uma imagem com base no sistema operativo que pretende executar. O Azure e os respetivos parceiros oferecem várias imagens, algumas das quais incluem aplicações e ferramentas pré-instaladas. Em alternativa, pode carregar uma das suas próprias imagens.

### Imagens do Azure

Em todos os artigos acima, pode facilmente utilizar uma imagem do Azure existente para criar uma VM e personalizá-la para o funcionamento em rede, o balanceamento de carga e muito mais. O portal fornece o Azure Marketplace para as imagens fornecidas pelo Azure. Pode obter listas semelhantes utilizando a linha de comandos. Por exemplo, na CLI do Azure, execute `azure vm image list` para obter uma lista de todas as imagens disponíveis, por localização e publicador. Consulte o artigo [Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md) para obter exemplos sobre a navegação e a utilização de imagens disponíveis.

### Utilizar a sua própria imagem

Se necessitar de personalizações específicas, pode utilizar uma imagem com base numa VM do Azure existente ao *capturar* essa VM ou ao carregar uma imagem sua, armazenada num disco rígido virtual (VHD). Para obter mais informações sobre distros suportados e como utilizar as suas imagens, consulte os artigos seguintes:

* [Distribuições aprovadas pelo Azure](virtual-machines-linux-endorsed-distros.md)

* [Informações sobre distribuições não aprovadas](virtual-machines-linux-create-upload-generic.md)

* [Como capturar uma máquina virtual com Linux como um modelo do Resource Manager](virtual-machines-linux-capture-image.md). 

## Passos seguintes

* Experimente um dos tutoriais para criar uma VM com Linux a partir do [portal](virtual-machines-linux-quick-create-portal.md), com a [CLI](virtual-machines-linux-quick-create-cli.md) ou através de um [modelo](virtual-machines-linux-cli-deploy-templates.md) do Azure Resource Manager.

* Depois de criar uma VM com Linux, pode facilmente [adicionar um disco de dados](virtual-machines-linux-add-disk.md).

* Passos rápidos para [repor uma palavra-passe ou chaves SSH e gerir utilizadores](virtual-machines-linux-using-vmaccess-extension.md)



<!--HONumber=Jun16_HO2-->



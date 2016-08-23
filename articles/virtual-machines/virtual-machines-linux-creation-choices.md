<properties
    pageTitle="Diferentes formas para criar uma VM com Linux | Microsoft Azure"
    description="Lista as diferentes formas de criar uma máquina virtual com Linux no Azure e fornece ligações para ferramentas e tutoriais para cada método."
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
    ms.date="07/06/2016"
    ms.author="iainfou"/>

# Diferentes formas para criar uma máquina virtual com Linux com o Resource Manager

O Azure disponibiliza várias formas para criar uma VM utilizando o modelo de implementação do Resource Manager para se adequar aos diferentes utilizadores e efeitos. Este artigo resume estas diferenças e as escolhas que pode fazer para a criação de máquinas virtuais (VMs) com Linux.

## CLI do Azure 

A CLI do Azure está disponível para todas as plataformas através de um pacote npm, pacotes fornecidos pela distro ou contentor Docker. Pode ler mais sobre [como instalar e configurar a CLI do Azure](../xplat-cli-install.md). Os tutoriais seguintes fornecem exemplos sobre como utilizar a CLI do Azure. Leia cada artigo para obter mais detalhes sobre os comandos de início rápido da CLI mostrados:

* [Criar uma VM com Linux a partir da CLI do Azure para o desenvolvimento e teste](virtual-machines-linux-quick-create-cli.md)

    ```bash
    azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
    ```

* [Criar uma VM com Linux protegida utilizando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

* [Criar uma VM com Linux a partir do zero através da CLI do Azure](virtual-machines-linux-create-cli-complete.md)

* [Adicionar um disco a uma VM com Linux](virtual-machines-linux-add-disk.md)

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
    ```

## Portal do Azure

A interface gráfica do utilizador do [Portal do Azure](https://portal.azure.com) é uma forma fácil para experimentar uma VM, especialmente se é inexperiente com o Azure, uma vez que não existe nada para instalar no seu sistema. Utilize o Portal do Azure para criar a VM:

* [Criar uma VM com Linux através do portal do Azure](virtual-machines-linux-quick-create-portal.md) 
* [Anexar um disco através do portal do Azure](virtual-machines-linux-attach-disk-portal.md)

## Opções de imagem e sistema operativo
Quando cria uma VM; escolhe uma imagem com base no sistema operativo que pretende executar. O Azure e os respetivos parceiros oferecem várias imagens, algumas das quais incluem aplicações e ferramentas pré-instaladas. Em alternativa, pode carregar uma das suas próprias imagens (ver abaixo).

### Imagens do Azure
Pode utilizar os `azure vm image` comandos da CLI para ver o que está disponível por publicador, versão distro e compilações.

Listar publicadores disponíveis:

```bash
azure vm image list-publishers --location WestUS
```

Listar produtos disponíveis (ofertas) para um determinado publicador:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Listar SKUs disponíveis (versões distro) de uma determinada oferta:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Listar todas as imagens disponíveis para uma determinada versão:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Veja [Navigate and select Azure virtual machine images with the Azure CLI (Navegar e selecionar imagens da máquina virtual do Azure com a CLI do Azure)](virtual-machines-linux-cli-ps-findimage.md) para obter mais exemplos sobre a navegação e a utilização de imagens disponíveis.

Os comandos `azure vm quick-create` e `azure vm create` também têm alguns aliases que pode utilizar para aceder rapidamente a distros mais comuns e às respetivas versões mais recentes. É uma opção mais fácil do que a necessidade de especificar o publicador, oferta, SKU e versão sempre que cria uma VM:

| Alias     | Publicador | Oferta        | SKU         | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | mais recente  |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  |
| Debian    | credativ  | Debian       | 8           | mais recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  |
| RHEL      | Redhat    | RHEL         | 7.2         | mais recente  |
| SLES      | SLES      | SLES         | 12-SP1      | mais recente  |
| UbuntuLTS | Canónico | UbuntuServer | 14.04.4-LTS | mais recente  |

### Utilizar a sua própria imagem

Se necessitar de personalizações específicas, pode utilizar uma imagem com base numa VM do Azure existente ao *capturar* essa VM ou ao carregar uma imagem sua, armazenada num disco rígido virtual (VHD). Para obter mais informações sobre distros suportados e como utilizar as suas imagens, consulte os artigos seguintes:

* [Distribuições aprovadas pelo Azure](virtual-machines-linux-endorsed-distros.md)

* [Informações sobre distribuições não aprovadas](virtual-machines-linux-create-upload-generic.md)

* [Como capturar uma máquina virtual com Linux como um modelo do Resource Manager](virtual-machines-linux-capture-image.md). Comandos de início rápido:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## Passos seguintes

* Experimente um dos tutoriais para criar uma VM com Linux a partir do [portal](virtual-machines-linux-quick-create-portal.md), com a [CLI](virtual-machines-linux-quick-create-cli.md) ou através de um [modelo](virtual-machines-linux-cli-deploy-templates.md) do Azure Resource Manager.

* Depois de criar uma VM com Linux, pode facilmente [adicionar um disco de dados](virtual-machines-linux-add-disk.md).

* Passos rápidos para [repor uma palavra-passe ou chaves SSH e gerir utilizadores](virtual-machines-linux-using-vmaccess-extension.md)



<!--HONumber=Aug16_HO1-->



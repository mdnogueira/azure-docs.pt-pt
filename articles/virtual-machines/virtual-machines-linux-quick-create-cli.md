<properties
   pageTitle="Criar uma VM com Linux no Azure com a CLI | Microsoft Azure"
   description="Criar uma VM com Linux no Azure com a CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/08/2016"
   ms.author="v-livech"/>


# Criar uma VM com Linux no Azure com a CLI

Este artigo mostra como implementar rapidamente uma máquina virtual (VM) com Linux no Azure através do comando `azure vm quick-create` na interface de linha de comandos (CLI) do Azure. O comando `quick-create` implementa uma VM numa infraestrutura básica segura que pode utilizar para criar protótipos ou testar rapidamente um conceito. O artigo requer

- uma conta do Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))

- a [CLI do Azure](../xplat-cli-install.md) com sessão iniciada com `azure login`.

- A CLI do Azure _tem de estar no_ modo Azure Resource Manager `azure config mode arm`.  

Também pode implementar rapidamente uma VM com Linux através do [portal do Azure](virtual-machines-linux-quick-create-portal.md).

## Comandos rápidos

O exemplo seguinte mostra como implementar uma VM CoreOS e anexar a chave Secure Shell (SSH) (os argumentos podem ser diferentes).

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

As secções seguintes explicam o comando e os respetivos requisitos com o Ubuntu Server 14.04 LTS como a distribuição do Linux.  

## Criação rápida de aliases para VM

Uma forma rápida de escolher uma distribuição consiste em utilizar os aliases da CLI do Azure mapeados para as distribuições de SO mais comuns. A tabela seguinte lista os aliases (a partir da versão 0.10 da CLI do Azure). Todas as implementações que utilizem a predefinição `quick-create` para VMs apoiadas pelo armazenamento de unidade de estado sólido (SSD), que oferecem aprovisionamento mais rápido e acesso ao disco de elevado desempenho. (Estes aliases representam uma parte muito pequena das distribuições disponíveis no Azure. Encontre mais imagens no Azure Marketplace ao [procurar uma imagem](virtual-machines-linux-cli-ps-findimage.md) ou ao [carregar a sua própria imagem personalizada](virtual-machines-linux-create-upload-generic.md).)

| Alias     | Publicador | Oferta        | SKU         | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | mais recente  |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  |
| Debian    | credativ  | Debian       | 8           | mais recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  |
| RHEL      | Red Hat    | RHEL         | 7.2         | mais recente  |
| UbuntuLTS | Canónico | Ubuntu Server | 14.04.4-LTS | mais recente  |

As secções seguintes utilizam o alias `UbuntuLTS` para a opção **ImageURN** (`-Q`) para implementar um Ubuntu 14.04.4 LTS Server.

## Instruções detalhadas

O exemplo `quick-create` anterior apenas chamou o sinalizador `-M` para identificar a chave pública SSH para carregar ao mesmo tempo que desativou palavras-passe SSH, pelo que lhe é pedido

- o nome do grupo de recursos (normalmente, qualquer cadeia serve para o seu primeiro grupo de recursos do Azure)
- o nome da VM
- a localização (westus ou westeurope são boas predefinições)
- o linux (para que o Azure saiba que SO pretende)
- o nome de utilizador

A lista seguinte especifica todos os valores para que não sejam necessários mais pedidos. Desde que tenha um `~/.ssh/id_rsa.pub` como um ficheiro de chave pública com o formato ssh-rsa, funciona tal como está.

```bash
azure vm quick-create \
-g exampleResourceGroup \
-n exampleVMName \
-l westus \
-y Linux \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub \
-Q UbuntuLTS
```

O resultado deverá ter o aspeto do bloco de saída seguinte.

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "exampleVMName"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "exampleVMName"
+ Looking up the VM "exampleVMName"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMName
data:      User Name                     :exampleAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Inicie sessão na sua VM com o endereço IP público apresentado no resultado. Também pode utilizar o nome de domínio completamente qualificado (FQDN) apresentado.

```bash
ssh -i ~/.ssh/id_rsa.pub exampleAdminUser@138.91.247.29
```

O processo de início de sessão deve ter um aspeto semelhante ao seguinte:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

exampleAdminUser@exampleVMName:~$
```

## Passos seguintes

O comando `azure vm quick-create` é a forma mais rápida de implementar uma VM para que possa iniciar sessão numa shell bash e começar a trabalhar. No entanto, utilizar o comando `vm quick-create` não lhe dá um controlo extenso nem lhe permite criar um ambiente mais complexo.  Para implementar uma VM com Linux personalizada para a sua infraestrutura, pode seguir qualquer um dos artigos seguintes:

- [Utilizar um modelo do Azure Resource Manager para criar uma implementação específica](virtual-machines-linux-cli-deploy-templates.md)
- [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](virtual-machines-linux-create-cli-complete.md)
- [Criar uma VM com Linux Protegida por SSH no Azure através de modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Também pode [utilizar o controlador `docker-machine` do Azure com vários comandos para criar rapidamente uma VM com Linux como anfitrião docker](virtual-machines-linux-docker-machine.md).



<!--HONumber=sep16_HO2-->



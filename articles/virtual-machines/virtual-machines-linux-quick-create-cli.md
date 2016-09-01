<properties
   pageTitle="Criar uma VM com Linux no Azure utilizando a CLI | Microsoft Azure"
   description="Crie uma VM com Linux no Azure utilizando a CLI."
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
   ms.date="08/18/2016"
   ms.author="v-livech"/>


# Criar uma VM com Linux no Azure utilizando a CLI

> [AZURE.NOTE] Se tiver alguns minutos, ajude-nos a melhorar a documentação de VM do Azure Linux, ao responder a este [inquérito rápido](https://aka.ms/linuxdocsurvey) sobre as suas experiências. Cada resposta ajuda-nos a ajudá-lo a realizar o seu trabalho.

Este artigo mostra como implementar rapidamente uma Máquina Virtual com Linux no Azure através do comando `azure vm quick-create` do CLI do Azure. O comando `quick-create` implementa uma VM com uma infraestrutura básica envolvente, que pode utilizar para criar protótipos ou testar rapidamente um conceito.  O artigo requer uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e [a CLI do Azure](../xplat-cli-install.md) com sessão iniciada (`azure login`) e no modo Resource Manager (`azure config mode arm`).  Também pode implementar rapidamente uma VM com Linux através do [portal do Azure](virtual-machines-linux-quick-create-portal.md).

## Resumo do Comando Rápido

Um comando para implementar uma VM CoreOS e anexar a chave SSH:

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Implementar a VM com Linux

Iremos agora orientá-lo através do comando e explicar cada passo com o RedHat Enterprise Linux 7.2.  

## Utilizar um alias ImageURN

O comando da CLI do Azure `quick-create` tem aliases mapeados para as distribuições de SO mais comuns. A tabela seguinte lista os aliases de distribuição (a partir da CLI do Azure versão 0.10).  Todas as implementações que utilizam a predefinição `quick-create` para as VMs com cópia de segurança no armazenamento SSD, proporcionando uma experiência de elevado desempenho.

| Alias     | Publicador | Oferta        | SKU         | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | mais recente  |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  |
| Debian    | credativ  | Debian       | 8           | mais recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  |
| RHEL      | Redhat    | RHEL         | 7.2         | mais recente  |
| UbuntuLTS | Canónico | UbuntuServer | 14.04.4-LTS | mais recente  |



Para a opção **ImageURN** (`-Q`), utilizamos `RHEL` para implementar uma VM de RedHat Enterprise Linux 7.2. Estes aliases `quick-create` representam uma parte muito pequena do SO disponível no Azure.  Encontre mais imagens no mercado, [ao procurar uma imagem](virtual-machines-linux-cli-ps-findimage.md) ou [carregue a sua própria imagem personalizada](virtual-machines-linux-create-upload-generic.md).

Nas seguintes instruções sobre o comando, substitua as mensagens por valores do seu próprio ambiente.

Siga as mensagens e introduza os seus próprios nomes

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

O resultado deverá ter o aspeto do bloco de saída seguinte.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

SSH na sua VM, na porta 22 e o endereço IP público apresentado no resultado. (Pode também utilizar o FQDN apresentado.)

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
O processo de início de sessão deve ter um aspeto semelhante ao seguinte:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Passos Seguintes

O `azure vm quick-create` é a forma de implementar rapidamente uma VM para que possa iniciar sessão numa shell de deteção e começar a trabalhar. Utilizar `vm quick-create` não lhe dá as vantagens adicionais de um ambiente complexo.  Para implementar uma VM com Linux personalizada de acordo com a sua infraestrutura, pode seguir qualquer um dos artigos seguintes.

- [Utilizar um modelo do Azure Resource Manager para criar uma implementação específica](virtual-machines-linux-cli-deploy-templates.md)
- [Crie o seu ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](virtual-machines-linux-create-cli-complete.md).
- [Criar uma VM com Linux Protegida por SSH no Azure através de Modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)



<!--HONumber=ago16_HO4-->



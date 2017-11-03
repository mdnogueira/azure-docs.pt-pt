---
title: Utilize Terraform para criar uma VM com Linux completa no Azure | Microsoft Docs
description: "Saiba como utilizar Terraform para criar e gerir um ambiente de máquina virtual completado do Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: timlt
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: bd19c6bbf767fd954ad5c5a733f78ba3a1ee1f58
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Criar uma infraestrutura completa de máquina virtual Linux no Azure com Terraform

Terraform permite-lhe definir e criar implementações de toda a infraestrutura no Azure. Criar modelos de Terraform num formato legível por humanos que criar e configurar recursos do Azure de forma consistente e reproduzível. Este artigo mostra como criar um ambiente de Linux completado e de recursos com Terraform de suporte. Também pode aprender como [instalar e configurar Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Criar a ligação do Azure e o grupo de recursos

Vamos percorrer cada secção de um modelo de Terraform. Também pode ver a versão completa do [Terraform modelo](#complete-terraform-script) que pode copiar e colar.

O `provider` secção indica Terraform para utilizar um fornecedor do Azure. Para obter os valores para *subscription_id*, *client_id*, *client_secret*, e *tenant_id*, consulte [instalar e Configurar Terraform](terraform-install-configure.md). 

> [!TIP]
> Se criar variáveis de ambiente para os valores ou estiver a utilizar o [Bash de Shell de nuvem do Azure experiência](/azure/cloud-shell/overview) , não precisa de incluir as declarações de variável nesta secção.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A secção seguinte cria um grupo de recursos denominado `myResourceGroup` no `eastus` localização:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "East US"

    tags {
        environment = "Terraform Demo"
    }
}
```

Nas secções adicionais, referenciar o grupo de recursos com *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Criar a rede virtual
A secção seguinte cria uma rede virtual denominada *myVnet* no *10.0.0.0/16* espaço de endereços:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```
: A secção seguinte cria uma sub-rede designada *mySubnet* no *myVnet* rede virtual

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Criar endereço IP público
Para aceder aos recursos através da Internet, crie e atribua um endereço IP público à VM. A secção seguinte cria um endereço IP público com o nome *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "East US"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Criar o grupo de segurança de rede
Grupos de segurança de rede controlar o fluxo de tráfego de rede que entra e sai da VM. A secção seguinte cria um grupo de segurança de rede com o nome *myNetworkSecurityGroup* e define uma regra para permitir o tráfego na porta TCP 22 SSH:

```tf
resource "azurerm_network_security_group" "temyterraformpublicipnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    ;
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Criar o cartão de interface de rede virtual
Uma placa de interface de rede virtual (NIC) liga-se a VM para uma determinada rede virtual, o endereço IP público e o grupo de segurança de rede. A secção seguinte um manual de comunicação social Ansible cria uma NIC virtual com o nome *myNIC* ligados aos recursos da rede virtuais que criou:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Criar conta de armazenamento de diagnóstico
Para armazenar o diagnóstico de arranque para uma VM, necessita de uma conta de armazenamento. Este diagnóstico de arranque pode ajudar a resolver problemas e monitorizar o estado da VM. Criar a conta do storage é apenas armazenar os dados de diagnóstico de arranque. Como cada conta de armazenamento têm de ter um nome exclusivo, a secção seguinte gera algum texto aleatório:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Agora, pode criar uma conta de armazenamento. A secção seguinte cria uma conta do storage, com o nome com base no texto aleatório gerado no passo anterior:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "East US"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Criar a máquina virtual

O último passo consiste em criar uma VM e utilizar todos os recursos criados. A secção seguinte cria uma VM chamada *myVM* e anexa o NIC virtual com o nome *myNIC*. A versão mais recente *Ubuntu 16.04-LTS* imagem é utilizada e o nome de um utilizador *azureuser* é criado com a autenticação de palavra-passe desativada.

 Dados de chave SSH são fornecidos no *ssh_keys* secção. Fornecer uma chave SSH pública válida no *key_data* campo.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "East US"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Script de Terraform concluída

Para reunir todas estas secções e vê Terraform em ação, crie um ficheiro chamado *terraform_azure.tf* e cole o seguinte conteúdo:

```tf
variable "resourcename" {
  default = "myResourceGroup"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "East US"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "East US"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "East US"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "East US"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "East US"
    account_type        = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "East US"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Criar e implementar a infraestrutura
Com o modelo de Terraform criado, o primeiro passo é inicializar Terraform. Este passo garante que Terraform tem todos os pré-requisitos para criar o modelo no Azure.

```bash
terraform init
```

O passo seguinte consiste em ter Terraform reveja e confirme o modelo. Este passo compara os recursos pedidos para as informações de estado guardado pelo Terraform e, em seguida, devolve a execução planeada. Recursos estão *não* criada no Azure.

```bash
terraform plan
```

Depois de executar o comando anterior, deverá ver algo semelhante do ecrã seguinte:

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Se tudo procura correto e pronto para construir a infraestrutura no Azure, aplicar o modelo no Terraform:

```bash
terraform apply
```

Uma vez concluída a Terraform, está pronta a sua infraestrutura VM. Obter o endereço IP público da sua VM com [mostrar de vm az](/cli/azure/vm#show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Pode, em seguida, SSH para a VM:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Passos seguintes
Criou infraestrutura básica no Azure utilizando Terraform. Para cenários mais complexos, incluindo exemplos que utilize balanceadores de carga e a máquina virtual Dimensionar conjuntos, consulte o artigo várias [Terraform exemplos do Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para obter uma lista atualizada de fornecedores do Azure suportados, consulte o [Terraform documentação](https://www.terraform.io/docs/providers/azurerm/index.html).
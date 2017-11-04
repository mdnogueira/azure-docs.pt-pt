---
title: "Definir Terraform utilizado para criar um dimensionamento de máquina virtual do Azure a partir de uma imagem personalizada Packer"
description: "Utilize Terraform para configurar e a versão do conjunto de dimensionamento uma máquina virtual do Azure a partir de uma imagem personalizada gerada pelo Packer (completa com uma rede virtual e os discos ligados geridos)."
keywords: "Dimensionar terraform, devops, a máquina virtual, definida, rede, armazenamento, módulos, imagens personalizadas, packer"
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Definir Terraform utilizado para criar um dimensionamento de máquina virtual do Azure a partir de uma imagem personalizada Packer

Neste tutorial, utilize [Terraform](https://www.terraform.io/) para criar e implementar uma [conjunto de dimensionamento da máquina virtual do Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) criado com uma imagem personalizada produzida utilizando [Packer](https://www.packer.io/intro/index.html) com discos geridos utilizar o [HashiCorp configuração idioma](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a sua implementação Terraform
> * Utilizar variáveis e saídas para a implementação de Terraform 
> * Criar e implementar uma infraestrutura de rede
> * Criar uma imagem de máquina virtual personalizada utilizando Packer
> * Criar e implementar um conjunto utilizando a imagem personalizada de dimensionamento de máquina virtual
> * Criar e implementar uma jumpbox 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar
> * [Instalar Terraform e configurar o acesso ao Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Criar um par de chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) se ainda não tiver um
> * [Instalar Packer](https://www.packer.io/docs/install/index.html) se ainda não tiver Packer instalado no seu computador local


## <a name="create-the-file-structure"></a>Criar a estrutura de ficheiros

Crie novos ficheiros de três num diretório vazio com os nomes seguintes:

- ```variables.tf```Este ficheiro contém os valores das variáveis utilizadas no modelo.
- ```output.tf```Este ficheiro descreve as definições que apresentam após a implementação.
- ```vmss.tf```Este ficheiro contém o código da infraestrutura de que está a implementar.

##  <a name="create-the-variables"></a>Crie as variáveis 

Neste passo, é possível definir as variáveis que personalizar os recursos criados pelo Terraform.

Editar o `variables.tf` ficheiro, copie o seguinte código e guardar as alterações.

```tf 
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> O valor predefinido da variável resource_group_name, defina o valor do seu próprio.

Guarde o ficheiro.

Quando implementar o modelo de Terraform, pretende obter o nome de domínio completamente qualificado que é utilizado para aceder à aplicação. Utilize o ```output``` tipo de recurso dos Terraform e obter o ```fqdn``` propriedade do recurso. 

Editar o `output.tf` de ficheiros e copie o seguinte código para expor o nome de domínio completamente qualificado para as máquinas virtuais. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definir a infraestrutura de rede num modelo 

Neste passo, crie a seguinte infraestrutura de rede num novo grupo de recursos do Azure: 
  - Uma VNET com o espaço de endereços de 10.0.0.0/16 
  - Uma sub-rede com o espaço de endereços de 10.0.2.0/24
  - Dois endereços IP públicos. Utilizada pelo balanceador de carga de conjunto do dimensionamento de máquina virtual; outros utilizado para estabelecer ligação com o jumpbox SSH

Também precisa de um grupo de recursos onde todos os recursos são criados. 

Editar e copie o seguinte código a ```vmss.tf``` ficheiro: 

```tf 

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Recomendamos a etiquetagem os recursos que está a ser implementados no Azure para facilitar a sua identificação no futuro.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede

Inicializar o ambiente de Terraform, executando o seguinte comando no diretório onde criou o `.tf` ficheiros:

```bash
terraform init 
```
 
Transfira os plug-ins do fornecedor do registo Terraform para o ```.terraform``` pasta no diretório em que executou o comando.

Execute o seguinte comando para implementar a infraestrutura do Azure.

```bash
terraform apply
```

Certifique-se de que o nome de domínio completamente qualificado do endereço IP público corresponde à sua configuração.

![Nome de domínio completamente qualificado Terraform endereço IP público do conjunto de dimensionamento de máquina virtual](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

O grupo de recursos contém os seguintes recursos:

![Recursos de rede Terraform de conjunto de dimensionamento de máquina virtual](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Criar uma imagem do Azure utilizando Packer
Criar uma imagem personalizada do Linux, utilizando os passos descritos no tutorial, [como utilizar Packer para criar imagens da máquina virtual com Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer).
 
Siga o tutorial para criar uma imagem de Ubuntu desaprovisionada com NGINX instalado.

![Depois de criar a imagem de Packer, ter uma imagem](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Para efeitos deste tutorial, na imagem Packer, é executado um comando para instalações nginx. Também pode executar o seu próprio script ao criar.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Editar a infraestrutura para adicionar o conjunto de dimensionamento de máquina virtual

Neste passo, crie os seguintes recursos na rede que foi anteriormente implementada:
- Balanceador de carga do Azure para servir a aplicação e anexe-o para o endereço IP público que tenha sido implementado no passo 4
- Azure de uma carga balanceador e regras para servir a aplicação e anexe-o para o endereço IP público configurado anteriormente.
- Conjunto de endereços de back-end do Azure e atribua-o ao balanceador de carga 
- Uma porta de sonda de estado de funcionamento utilizada pela aplicação e configurado no balanceador de carga 
- Um conjunto junto por trás do Balanceador de carga, em execução numa vnet implementada anteriormente de dimensionamento de máquina virtual
- [Nginx](http://nginx.org/) em nós do dimensionamento da máquina virtual instalado a partir da imagem personalizada


Adicione o seguinte código ao fim do `vmss.tf` ficheiro.

```tf


resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Personalizar a implementação, adicionando o seguinte código para `variables.tf`:

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Implementar o dimensionamento da máquina virtual definido no Azure

Execute o seguinte comando para visualizar a implementação de conjunto de dimensionamento de máquina virtual:

```bash
terraform plan
```

O resultado do comando assemelha-se a imagem seguinte:

![Terraform adicionar plano do conjunto de dimensionamento de máquina virtual](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Implemente os recursos adicionais no Azure: 

```bash
terraform apply 
```

O conteúdo do grupo de recursos assemelha-se a imagem seguinte:

![Grupo de recursos de conjunto de dimensionamento de máquina virtual Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Abra um browser e estabelecer ligação com o nome de domínio completamente qualificado que foi devolvido pelo comando. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Adicionar um jumpbox à rede existente 

Este passo opcional permite o acesso SSH para as instâncias do conjunto, utilizando um jumpbox de dimensionamento de máquina virtual.

Adicione os seguintes recursos para a implementação existente:
- Uma interface de rede ligada à mesma sub-rede que o conjunto de dimensionamento de máquina virtual
- Uma máquina virtual com esta interface de rede

Adicione o seguinte código ao fim do `vmss.tf` ficheiro:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Editar `outputs.tf` para adicionar o seguinte código que mostra o nome de anfitrião do jumpbox quando a implementação estiver concluída:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Implementar o jumpbox

Implemente o jumpbox.

```bash
terraform apply 
```

Depois de concluída a implementação, o conteúdo do grupo de recursos assemelha-se a imagem seguinte:

![Grupo de recursos de conjunto de dimensionamento de máquina virtual Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Inicie sessão com uma palavra-passe está desativado no jumpbox e do conjunto de dimensionamento de máquina virtual que implementou. Inicie sessão com o SSH para aceder as VMs.

## <a name="clean-up-the-environment"></a>Limpar o ambiente

Os seguintes comandos a eliminar os recursos que criou neste tutorial:

```bash
terraform destroy
```

Tipo `yes` quando lhe for pedido para confirmar a eliminação dos recursos. O processo de destruição pode demorar alguns minutos a concluir.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um conjunto de dimensionamento de máquina virtual e um jumpbox no Azure utilizando Terraform. Aprendeu a:

> [!div class="checklist"]
> * Inicializar Terraform implementação
> * Utilizar variáveis e saídas para a implementação de Terraform 
> * Criar e implementar uma infraestrutura de rede
> * Criar uma imagem de máquina virtual personalizada utilizando Packer
> * Criar e implementar um conjunto utilizando a imagem personalizada de dimensionamento de máquina virtual
> * Criar e implementar uma jumpbox 
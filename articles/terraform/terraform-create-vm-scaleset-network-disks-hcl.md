---
title: Utilize Terraform e criar um conjunto utilizando HCL de dimensionamento de VM do Azure.
description: "Utilize Terraform para configurar e a versão um dimensionamento de máquina virtual do Azure, definir completo com uma rede virtual e geridos discos ligados."
keywords: "terraform, devops, dimensionamento de máquina virtual, definida, rede, armazenamento, módulos"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>Utilizar Terraform para planear e criar um conjunto com o armazenamento gerido de dimensionamento de VM do Azure em rede

Neste artigo, utilize [Terraform](https://www.terraform.io/) para criar e implementar uma [scaleset de máquina virtual do Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) com discos geridos utilizando o [Hashicorp configuração idioma](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a sua implementação Terraform
> * Utilizar variáveis e saídas para a implementação de Terraform 
> * Criar e implementar a infraestrutura de rede
> * Criar e implementar um conjunto de dimensionamento de máquina virtual e ligá-lo à rede
> * Criar e implementar uma jumpbox para ligar às VMs através de SSH

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

- [Instalar Terraform e configurar o acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure)
- [Criar um par de chaves SSH](/azure/virtual-machines/linux/mac-create-ssh-keys) se ainda não tiver um.

## <a name="create-the-file-structure"></a>Criar a estrutura de ficheiros

Crie novos ficheiros de três num diretório vazio com os nomes seguintes:

- `variables.tf`Este ficheiro contém os valores das variáveis utilizadas no modelo.
- `output.tf`Este ficheiro descreve as definições que serão apresentadas após a implementação.
- `vmss.tf`Este código para o dimensionamento da máquina virtual definir infraestrutura.

## <a name="create-the-variables-and-output-definitions"></a>Crie as variáveis e definições de saída

Neste passo, é possível definir as variáveis que personalizar os recursos criados pelo Terraform.

Editar o `variables.tf` ficheiro, copie o seguinte código e guardar as alterações.

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

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
  - Dois endereços IP públicos. Um utilizado pelo balanceador de carga do conjunto a máquina virtual escala, a outro utilizado para estabelecer ligação com o jumpbox SSH.


Editar e copie o seguinte código a `vmss.tf` ficheiro: 

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
> É uma boa ideia Etiquetar recursos que está a ser implementados no Azure para facilitar a sua identificação no futuro.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede

Inicializar o ambiente de Terraform, executando o seguinte comando no diretório onde criou o `.tf` ficheiros:

```bash
terraform init 
```

Em seguida, execute o seguinte comando para implementar a infraestrutura no Azure.

```bash
terraform apply
```

Certifique-se de que o FQDN do endereço IP público corresponde à sua configuração: ![VMSS terraform FQDN para o endereço IP público](./media/tf-create-vmss-step4-fqdn.png)


O grupo de recursos deve ter os seguintes recursos: ![recursos de rede de terraform VMSS](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Editar a infraestrutura para adicionar o conjunto de dimensionamento de máquina virtual

Neste passo, adicione os seguintes recursos para o modelo:

- Azure de uma carga balanceador e regras para servir a aplicação e anexe-o para o endereço IP público configurado anteriormente.
- Conjunto de endereços de back-end do Azure e atribuí-lo ao loadbalancer 
- Uma estado de funcionamento sonda a porta utilizada pela aplicação e configurado no loadbalancer 
- Um conjunto junto por trás do Balanceador de carga, em execução numa vnet implementada anteriormente de dimensionamento de máquina virtual
- [Nginx](http://nginx.org/) em nós do dimensionamento da máquina virtual a utilizar uma extensão de script personalizado.

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
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
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

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
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

O resultado do comando deve ter o seguinte aspeto.
![Terraform adicionar vmss plano](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

Em seguida, implemente os recursos adicionais no Azure: 

```bash
terraform apply 
```

O conteúdo do grupo de recursos deve ter o seguinte aspeto:

![Grupo de recursos do Terraform vm scaleset](./media/tf-create-vmss-step6-apply.png)

Abra um browser e estabelecer ligação com o FQDN que foi devolvido pelo comando. 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>Adicionar um jumpbox SSH para a rede existente 

Neste passo, configure os seguintes recursos:
- Uma interface de rede ligado à mesma sub-rede que o conjunto de dimensionamento de máquina virtual.
- Uma máquina virtual ligada com esta interface de rede. Este jumpbox é acessível remotamente. Assim que estiver ligado, pode SSH para qualquer uma das máquinas virtuais no conjunto de dimensionamento.



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

Editar `outputs.tf` e adicione o seguinte código para apresentar o nome de anfitrião do jumpbox quando a implementação está completa:

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

Depois de concluída a implementação, o conteúdo do grupo de recursos é aspeto:

![Grupo de recursos do Terraform vm scaleset](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> Inicie sessão com uma palavra-passe está desativado no jumpbox e do conjunto de dimensionamento de máquina virtual que implementou. Inicie sessão com o SSH para aceder as VMs.

## <a name="clean-up-the-environment"></a>Limpar o ambiente

Os seguintes comandos a eliminar os recursos que criou neste tutorial:

```bash
terraform destroy
```

Tipo `yes` quando lhe for pedido para confirmar a eliminação dos recursos. O processo de destruição demora alguns minutos a concluir.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou um conjunto no Azure utilizando Terraform de dimensionamento de máquina virtual. Aprendeu a:

> [!div class="checklist"]
> * Inicializar Terraform implementação
> * Utilizar variáveis e saídas para a implementação de Terraform 
> * Criar e implementar uma infraestrutura de rede
> * Criar e implementar um conjunto de dimensionamento de máquina virtual e ligue-a num ambiente existente
> * Criar e implementar uma jumpbox para ligar às VMs através de SSH 

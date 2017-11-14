---
title: Criar um cluster VM com Terraform e HCL
description: "Utilizar Terraform e idioma de configuração de HashiCorp (HCL) para criar um cluster de máquina virtual do Linux com um balanceador de carga no Azure"
keywords: "terraform devops, máquina virtual, rede, módulos"
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Criar um cluster VM com Terraform e HCL

Este tutorial demonstra a criação de um cluster de computação pequenos utilizando o [HashiCorp configuração idioma](https://www.terraform.io/docs/configuration/syntax.html) (HCL). A configuração cria um balanceador de carga, duas VMs com Linux num [conjunto de disponibilidade](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)e todos os recursos de rede necessários.

Neste tutorial:

> [!div class="checklist"]
> * Configurar a autenticação do Azure
> * Criar um ficheiro de configuração Terraform
> * Inicializar Terraform
> * Criar um plano de execução Terraform
> * Aplicar o plano de execução Terraform

## <a name="1-set-up-azure-authentication"></a>1. Configurar a autenticação do Azure

> [!NOTE]
> Se lhe [utilizar variáveis de ambiente de Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables), ou executar este tutorial no [Shell de nuvem do Azure](terraform-cloud-shell.md), ignore esta secção.

Nesta secção, gerar um principal de serviço do Azure e dois Terraform os ficheiros de configuração que contém as credenciais do principal de segurança.

1. [Configurar um principal de serviço do Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) para ativar Terraform para Aprovisionar recursos no Azure. Ao criar o principal, tome nota dos valores para o ID de subscrição, o inquilino, o appId e a palavra-passe.

2. Abra uma linha de comandos.

3. Crie um diretório no qual pretende armazenar os ficheiros de Terraform vazio.

4. Crie um novo ficheiro que contém as declarações de variáveis. Pode atribuir o nome deste ficheiro tudo o que quiser com um `.tf` extensão.

5. Copie o seguinte código para o ficheiro de declaração de variável:

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Crie um novo ficheiro que contém os valores de variáveis do seu Terraform. É comum para o nome do ficheiro de variável Terraform `terraform.tfvars` como Terraform carrega automaticamente qualquer ficheiro com o nome `terraform.tfvars` (ou segue um padrão de `*.auto.tfvars`) se estiver presente no diretório atual. 

7. Copie o seguinte código para o ficheiro de variáveis. Certifique-se de que substitui os marcadores de posição da seguinte forma: para `subscription_id`, utilize o ID de subscrição do Azure especificado durante a execução `az account set`. Para `tenant_id`, utilize o `tenant` valor devolvido do `az ad sp create-for-rbac`. Para `client_id`, utilize o `appId` valor devolvido do `az ad sp create-for-rbac`. Para `client_secret`, utilize o `password` valor devolvido do `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Criar um ficheiro de configuração Terraform

Nesta secção, crie um ficheiro que contém as definições de recursos para a sua infraestrutura.

1. Criar um novo ficheiro designado `main.tf`. 

2. Copiar os seguintes definições de recursos de exemplo criado recentemente na `main.tf` ficheiro: 

  ```tf
  resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
  }

  resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
  }

  resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
  }

  resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
  }

  resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
  }

  resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
  }

  resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
  }

  resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
  }
  ```

## <a name="3-initialize-terraform"></a>3. Inicializar Terraform 

O [terraform init comando](https://www.terraform.io/docs/commands/init.html) é utilizada para inicializar um diretório que contém os ficheiros de configuração de Terraform - os ficheiros criados com secções anteriores. Deve executar sempre o `terraform init` comando depois de escrever uma nova configuração de Terraform. 

> [!TIP]
> O `terraform init` comando é idempotent, o que significa que este pode ser chamado repetidamente ao produzir o mesmo resultado. Por conseguinte, se estiver a trabalhar num ambiente de colaboração e pensa que os ficheiros de configuração poderão ter sido alterados, é sempre boa ideia para chamar o `terraform init` comando antes de executar ou aplicar um esquema.

Para inicializar Terraform, execute o seguinte comando:

  ```cmd
  terraform init
  ```

  ![A inicializar Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Criar um plano de execução Terraform

O [comandos de plano terraform](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. Para gerar um plano de execução, Terraform agrega todos os `.tf` ficheiros no diretório atual. 

Se estiver a trabalhar num ambiente de colaboração, onde a configuração pode ser alterado entre a hora de criação do plano de execução e a hora de aplicar o plano de execução, deve utilizar o [do comando de plano terraform-parâmetroout](https://www.terraform.io/docs/commands/plan.html#out-path)para guardar o plano de execução para um ficheiro. Caso contrário, se estiver a trabalhar num ambiente única pessoa, pode omitir o `-out` parâmetro.

Se o nome do ficheiro Terraform variáveis não é `terraform.tfvars` e não siga a `*.auto.tfvars` padrão, tem de especificar o nome de ficheiro utilizando o [parâmetro de ficheiro - var do comando de plano terraform](https://www.terraform.io/docs/commands/plan.html#var-file-foo) ao executar o `terraform plan`comando.

Ao processar o `terraform plan` comando, Terraform efetua uma atualização e determina as ações são necessárias para atingir o estado pretendido especificado nos seus ficheiros de configuração.

Se não pretender guardar o seu plano de execução, execute o seguinte comando:

  ```cmd
  terraform plan
  ```

Se precisar de guardar o seu plano de execução, execute o seguinte comando (substituir a &lt;caminho > marcador de posição com o caminho de saída pretendidas):

  ```cmd
  terraform plan -out=<path>
  ```

![Criar um plano de execução Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Aplicar o plano de execução Terraform

O passo final deste tutorial consiste em utilizar o [terraform aplicar comandos](https://www.terraform.io/docs/commands/apply.html) para aplicar o conjunto de ações geradas pelo `terraform plan` comando.

Se pretender aplicar o plano de execução mais recente, execute o seguinte comando:

  ```cmd
  terraform apply
  ```

Se pretender aplicar um esquema de execução guardado anteriormente, execute o seguinte comando (substituir a &lt;caminho > marcador de posição com o caminho que contém o plano de execução guardada):

  ```cmd
  terraform apply <path>
  ```

![Aplicar um esquema de execução Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passos seguintes

- Procurar a lista de [módulos Terraform do Azure](https://registry.terraform.io/modules/Azure)
- Criar um [do conjunto de dimensionamento de máquina virtual com Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
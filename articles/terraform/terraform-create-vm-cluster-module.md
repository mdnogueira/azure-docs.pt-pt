---
title: "Utilize módulos Terraform para criar um cluster VM no Azure"
description: "Saiba como utilizar módulos Terraform para criar um cluster de máquina virtual do Windows no Azure"
keywords: "terraform devops, máquina virtual, rede, módulos"
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: 23d79fa4a1794a6dea69e6ae24da714babf54e62
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Criar um cluster VM com Terraform com o módulo de registo

Este artigo explica como criar um cluster VM pequeno com o Terraform [módulo de computação do Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). Neste tutorial, ficará a saber como: 

> [!div class="checklist"]
> * Configurar a autenticação com o Azure
> * Criar o modelo de Terraform
> * Visualizar as alterações com o plano
> * Aplicar a configuração para criar o cluster VM

Para obter mais informações sobre Terraform, consulte o [Terraform documentação](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Configurar a autenticação com o Azure

> [!TIP]
> Se lhe [utilizar variáveis de ambiente de Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) ou executar este tutorial no [Shell de nuvem do Azure](/azure/cloud-shell/overview), ignore este passo.

 Reveja [Terraform instalar e configurar o acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure) para criar um Azure principal de serviço. Utilize este principal de serviço para preencher um novo ficheiro `azureProviderAndCreds.tf` num diretório vazio com o seguinte código:

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Criar o modelo

Criar um novo modelo de Terraform `main.tf` com o seguinte código:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output = "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Executar `terraform init` no seu diretório de configuração. Utilizar uma versão de Terraform de, pelo menos, 0.10.6 mostra o seguinte resultado:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualizar as alterações com o plano

Executar `terraform plan` para pré-visualizar a infraestrutura de máquina virtual criada pelo modelo.

![Plano de Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Criar as máquinas virtuais com aplicar

Executar `terraform apply` para aprovisionar as VMs no Azure.

![Aplicar Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Passos seguintes

- Procurar a lista de [módulos Terraform do Azure](https://registry.terraform.io/modules/Azure)
- Criar um [do conjunto de dimensionamento de máquina virtual com Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
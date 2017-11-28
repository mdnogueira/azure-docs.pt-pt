---
title: Instalar e configurar Terraform para aprovisionar as VMs e outra infraestrutura no Azure | Microsoft Docs
description: Saiba como instalar e configurar Terraform para criar recursos do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: c156776103a466af8923ba7249d96835ff339268
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalar e configurar Terraform para aprovisionar as VMs e outra infraestrutura no Azure
 
Terraform fornece uma forma fácil de definir, de pré-visualização e implementar a infraestrutura de nuvem utilizando um [idioma templating simples](https://www.terraform.io/docs/configuration/syntax.html). Este artigo descreve os passos necessários para utilizar Terraform para Aprovisionar recursos no Azure. 

> [!TIP]
Para mais informações sobre como utilizar Terraform com o Azure, visite o [Terraform Hub](/azure/terraform). Terraform é instalado por predefinição no [nuvem Shell](/azure/terraform/terraform-cloud-shell). Utilizando a Shell de nuvem, pode ignorar as partes de instalação/configuração deste documento.

## <a name="install-terraform"></a>Instalar Terraform

Para instalar Terraform, [transferir](https://www.terraform.io/downloads.html) diretório de instalação do pacote apropriado para o seu sistema operativo para um separado. A transferência contém um único ficheiro executável, para o qual também deve definir um caminho global. Para obter instruções sobre como definir o caminho no Linux e Mac, aceda a [esta página Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obter instruções sobre como definir o caminho no Windows, aceda a [esta página Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Certifique-se a configuração de caminho com o `terraform` comando. Deverá ver uma lista de opções de Terraform disponíveis como saída:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurar o acesso de Terraform para o Azure

Configurar [um principal de serviço do Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) para ativar Terraform para Aprovisionar recursos no Azure. O serviço principal atribui os scripts de Terraform utilizando as credenciais para Aprovisionar recursos na sua subscrição do Azure.

Existem várias formas para criar uma aplicação do Azure AD e um serviço do Azure AD principal. Mais fácil e mais rápida são forma hoje utilizar o Azure CLI 2.0, que [pode transferir e instalar no Windows, Linux ou Mac](/cli/azure/install-azure-cli).

A iniciar sessão para administrar a sua subscrição do Azure através da emissão do comando seguinte:

   `az login`

Se tiver várias subscrições do Azure, os detalhes são devolvidos pelo `az login` comando. Definir o `SUBSCRIPTION_ID` variável de ambiente para conter o valor da devolvido `id` campo da subscrição que pretende utilizar. 

Configurar a subscrição que pretende utilizar para esta sessão.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

A conta ao obter a subscrição valores de ID de inquilino e ID de consulta.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Em seguida, crie as credenciais em separado para Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

O appId, palavra-passe, sp_name e inquilinos são devolvidos. Anote o appId e a palavra-passe.

Para testar as suas credenciais, abra uma nova shell e execute o seguinte comando, utilizando os valores devolvidos sp_name, palavra-passe, e inquilinos:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Configurar variáveis de ambiente de Terraform

Configure Terraform para utilizar o ID do inquilino, o ID de subscrição, o ID de cliente e o cliente segredo do principal de serviço ao criar recursos do Azure. Definir as seguintes variáveis de ambiente, que são utilizadas automaticamente pelo [módulos do Azure Terraform](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Pode utilizar este exemplo de script de shell para definir essas variáveis:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

## <a name="run-a-sample-script"></a>Executar um script de exemplo

Criar um ficheiro `test.tf` num diretório vazio e cole o seguinte script. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Guarde o ficheiro e, em seguida, execute `terraform init`. Este comando transfere os módulos do Azure necessários para criar um grupo de recursos do Azure. Consulte o seguinte resultado:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Pré-visualize o script com `terraform plan`e, em seguida, crie o `testResouceGroup` grupo de recursos com `terraform apply`:

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Passos seguintes

Ter instalado Terraform e configurado credenciais do Azure para que possa começar a implementar a infraestrutura na sua subscrição do Azure. Em seguida, testar a instalação através da criação de um grupo de recursos do Azure vazio.

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com Terraform](terraform-create-complete-vm.md)


---
title: Implementar com Terraform com Bash na Shell de nuvem do Azure | Microsoft Docs
description: Implementar recursos do Azure com Terraform no Bash
services: Azure
documentationcenter: 
author: tomarcher
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: c75b5d521dc3eacaf5c5921c35442b1afeb4fa13
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="terraform-and-bash-in-cloud-shell"></a>Terraform e Bash na Shell de nuvem
Este artigo explica como criar um grupo de recursos com o [Terraform AzureRM fornecedor](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de código aberto codifies APIs para ficheiros de configuração declarativas que podem ser partilhada entre os membros da equipa para ser editado, rever e com a versão. O fornecedor do Microsoft AzureRM é utilizado para interagir com recursos suportados pelo Azure Resource Manager através de AzureRM APIs. 

## <a name="automatic-authentication"></a>Autenticação automática
Por predefinição, Terraform está instalado no Bash na Shell de nuvem. Além disso, a Shell de nuvem automaticamente autentica sua subscrição do Azure CLI 2.0 predefinido para implementar recursos através de módulos do Terraform Azure.

Terraform utiliza a subscrição do Azure CLI 2.0 predefinido que é definida. Para atualizar subscrições predefinido, execute:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Instruções
### <a name="launch-bash-in-cloud-shell"></a>Iniciar Bash na Shell de nuvem
1. Inicie o Shell de nuvem da sua localização preferencial
2. Verificar a que sua subscrição preferencial está definida

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Criar um modelo de Terraform
Crie um novo modelo de Terraform com o nome main.tf com seu editor de texto preferido.

```
vim main.tf
```

Copie/cole o seguinte código na Shell de nuvem.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Guardar o ficheiro e saia do editor de texto.

### <a name="terraform-init"></a>Terraform init
Comece por executar `terraform init`.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

O [terraform init comando](https://www.terraform.io/docs/commands/init.html) é utilizada para inicializar um diretório de trabalho que contém ficheiros de configuração Terraform. O `terraform init` comando é o primeiro comando que deve ser executado depois de escrever uma nova configuração de Terraform ou clonagem de um existente do controlo de versão. É seguro executar este comando várias vezes.

### <a name="terraform-plan"></a>Plano de Terraform
Pré-visualize os recursos que sejam criados pelo modelo Terraform com `terraform plan`.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

O [comandos de plano terraform](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. Terraform efetua uma atualização, a menos que explicitamente desativado e, em seguida, determina as ações são necessárias para atingir o estado pretendido especificado nos ficheiros de configuração. Pode guardar o plano utilizando-out e, em seguida, é fornecido para terraform aplicam-se para garantir que são executadas apenas as ações previamente planeadas.

### <a name="terraform-apply"></a>Aplicar Terraform
Aprovisionar os recursos do Azure com `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

O [terraform aplicar comandos](https://www.terraform.io/docs/commands/apply.html) é utilizado para aplicar as alterações necessárias ao atingir o estado pretendido da configuração.

### <a name="verify-deployment-with-azure-cli-20"></a>Verificar a implementação com o Azure CLI 2.0
Executar `az group show -n myRgName` para verificar o recurso foi bem sucedida de aprovisionamento.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Limpar com terraform destruir
Limpar o grupo de recursos criado com o [Terraform destruir comando](https://www.terraform.io/docs/commands/destroy.html) para limpar a infraestrutura Terraform criados.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Um recurso do Azure através de Terraform foi criada com êxito. Visite os passos seguintes para saber mais sobre a Shell de nuvem.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o fornecedor do Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash no início rápido da Shell de nuvem](quickstart.md)
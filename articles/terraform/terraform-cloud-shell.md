---
title: Utilizar Terraform com a Shell de nuvem do Azure
description: "Utilize Terraform com a Shell de nuvem do Azure para simplificar a autenticação e a configuração de modelo."
keywords: "terraform, devops, dimensionamento de máquina virtual, definida, rede, armazenamento, módulos"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 253e5d341f93e61d851893eb05832fbf35707cfc
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="terraform-cloud-shell-development"></a>Desenvolvimento de Terraform nuvem Shell 

Terraform great funciona de uma linha de comandos de Bash, tais como macOS Terminal ou Bash no Windows ou Linux. Com o seu Terraform a experiência de configurações de Bash do [Shell de nuvem do Azure](/azure/cloud-shell/overview) tem algumas vantagens exclusivas para acelerar o ciclo de desenvolvimento.

Este artigo conceitos abrange Shell nuvem funcionalidades que o ajudam a escrever scripts de Terraform que implementar no Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração da credencial automática

Terraform está instalado e imediatamente disponível na Shell de nuvem. Scripts de Terraform autenticar com o Azure quando tem sessão iniciada numa Shell na nuvem para gerir a infraestrutura sem qualquer configuração adicional. Autenticação automática ignora a necessidade de criar manualmente um serviço do Active Directory principal e configurar as variáveis de fornecedor Terraform do Azure.


## <a name="using-modules-and-providers"></a>Utilização de módulos e fornecedores

Módulos do Azure Terraform necessitem de credenciais para aceder e efetuar alterações aos recursos na sua subscrição do Azure. Ao trabalhar na Shell do Cloud, adicione o seguinte código para os scripts para utilizar módulos do Azure Terraform na Shell do Cloud:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

A Shell de nuvem transmite os valores necessários o `azurerm` fornecedor através de variáveis de ambiente quando utilizar qualquer um do `terraform` comandos da CLI.

## <a name="other-cloud-shell-developer-tools"></a>Outras ferramentas de programador da Shell de nuvem

Ficheiros e Estados de shell são mantidas no armazenamento do Azure entre sessões de Shell de nuvem. Utilize [Explorador de armazenamento do Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para copiar e carregar ficheiros para a Shell de nuvem a partir do seu computador local.

O 2.0 CLI do Azure está disponível na Shell de nuvem e é uma ótima ferramenta para testar configurações e a verificar o seu trabalho após um `terraform apply` ou `terraform destroy` for concluída.


## <a name="next-steps"></a>Passos seguintes

[Criar um cluster VM pequeno, com o módulo de registo](terraform-create-vm-cluster-module.md)
[criar um cluster VM pequeno utilizando HCL personalizado](terraform-create-vm-cluster-with-infrastructure.md)

---
title: Utilizar Terraform com o Azure
description: "Introdução à utilização Terraform para vesion e implementar a infraestrutura do Azure."
ms.service: virtual-machines-linux
keywords: "terraform, devops, descrição geral, planear, aplicar, automatizar"
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 61b7677b2941fe95106e43012eee458692a3cc43
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="terraform-with-azure"></a>Terraform com o Azure

[Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de código aberto para o aprovisionamento e gestão de infraestrutura de nuvem. -Codifies infraestrutura nos ficheiros de configuração que descrevem a topologia dos recursos de nuvem, como máquinas virtuais, contas de armazenamento e interfaces de rede. Interface de linha de comandos do Terraform (CLI) fornece um mecanismo de simple para implementar e a versão os ficheiros de configuração para o Azure ou qualquer outra nuvem suportado.

Este artigo descreve as vantagens de utilizar Terraform para gerir a infraestrutura do Azure.

## <a name="automate-infrastructure-management"></a>Automatizar a gestão de infraestrutura.

Ficheiros de configuração baseada em modelo do Terraform permitem-lhe definir, aprovisionar e configurar recursos do Azure de forma repetíveis e previsível. Automatizar a infraestrutura tem várias vantagens:

- Reduz a possibilidade de erros humanos ao implementar e gerir a infraestrutura.
- Implementa o mesmo modelo várias vezes para criar ambientes de desenvolvimento, teste e produção idênticos.
- Reduz o custo de ambientes de desenvolvimento e teste criando-los a pedido.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Alterações na infraestrutura de compreender antes de estes serem aplicadas 

Como um recurso de topologia torna-se complexa, compreender o significado e o impacto das alterações de infraestrutura pode ser difícil.

Terraform fornece uma interface de linha de comandos (CLI) que permite aos utilizadores valide e pré-visualize alterações na infraestrutura antes que sejam implementados. Alterações na infraestrutura no Cofre de pré-visualização, forma produtiva tem várias vantagens:
- Os membros da equipa podem colaborar de forma mais eficaz por rapidamente compreender alterações propostas e o respetivo impacto.
- Alterações indesejadas podem ser detetadas no início do processo de desenvolvimento


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Implementar a infraestrutura para várias nuvens

Terraform é uma opção de ferramenta popular para cenários de cloud multi, onde está implementada infraestrutura semelhante do Azure e fornecedores adicionais em nuvem ou em centros de dados no local. Permite aos programadores utilizar o mesmas ferramentas e os ficheiros de configuração para gerir a infraestrutura de vários fornecedores de nuvem.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma descrição geral do Terraform e os respetivos benefícios, Eis os passos sugeridos:

- Introdução por [instalar Terraform e configurá-lo para utilizar o Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/terraform-install-configure).
- [Criar uma máquina virtual do Azure utilizando Terraform](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/terraform-create-complete-vm)
- Explorar o [módulo Azure Resource Manager para Terraform](https://www.terraform.io/docs/providers/azurerm/) 
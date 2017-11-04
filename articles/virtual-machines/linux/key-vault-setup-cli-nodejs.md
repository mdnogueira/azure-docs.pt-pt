---
title: Configurar o Cofre de chaves para VMs com Linux com a CLI do Azure 1.0 | Microsoft Docs
description: "Como configurar o Cofre de chaves para utilização com uma máquina virtual do Azure Resource Manager com a CLI do Azure 1.0."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: fed612a354d45f34619f2a66bd40d78740c43ac7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>Configurar o Cofre de chaves para máquinas virtuais no Azure Resource Manager com a CLI do Azure 1.0
Na pilha do Azure Resource Manager, os segredos/certificados são modelados como recursos que são fornecidos pelo fornecedor de recursos do Cofre de chaves. Para saber mais sobre o Cofre de chaves do Azure, consulte o artigo [que é o Cofre de chaves do Azure?](../../key-vault/key-vault-whatis.md) Para que o Cofre de chaves ser utilizado com virtual machines do Azure Resource Manager, o *EnabledForDeployment* propriedade no Cofre de chaves tem de ser definida como true. Pode fazê-lo em vários clientes. Este artigo mostra como configurar o Cofre de chaves para utilização com máquinas virtuais do Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões do CLI

- [Azure CLI 1.0](#quick-commands) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="use-cli-10-to-set-up-key-vault"></a>Utilize o CLI 1.0 para configurar o Cofre de chaves
Para criar um cofre de chaves utilizando a interface de linha de comandos (CLI), consulte [gerir o Cofre de chaves ao utilizar a CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Para CLI 1.0, tem de criar o Cofre de chaves antes de atribuir a política de implementação. Em seguida, pode atribuir a política utilizando o seguinte comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utilizar modelos para configurar o Cofre de chaves
Quando utiliza um modelo, tem de definir o `enabledForDeployment` propriedade `true` para o recurso do Cofre de chaves.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para outras opções que pode configurar quando criar um cofre de chaves utilizando os modelos, consulte [criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

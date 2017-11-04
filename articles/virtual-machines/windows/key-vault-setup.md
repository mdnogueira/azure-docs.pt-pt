---
title: Configurar a chave de cofre para VMs do Windows no Azure Resource Manager | Microsoft Docs
description: "Como configurar o Cofre de chaves para utilização com uma máquina virtual do Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a5083a5216efbfd76fd912ec48c2f0ec3b30c4a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o Cofre de chaves para máquinas virtuais no Gestor de recursos do Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Na pilha do Azure Resource Manager, os segredos/certificados são modelados como recursos que são fornecidos pelo fornecedor de recursos do Cofre de chaves. Para saber mais sobre o Cofre de chaves, consulte o artigo [que é o Cofre de chaves do Azure?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Para que o Cofre de chaves ser utilizado com virtual machines do Azure Resource Manager, o **EnabledForDeployment** propriedade no Cofre de chaves tem de ser definida como true. Pode fazê-lo em vários clientes.
> 2. O Cofre de chaves tem de ser criado na mesma subscrição e localização que a Máquina Virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Utilize o PowerShell para configurar o Cofre de chaves
Para criar um cofre de chaves utilizando o PowerShell, consulte [introdução ao Cofre de chaves do Azure](../../key-vault/key-vault-get-started.md#vault).

Para novos cofres de chaves, pode utilizar este cmdlet do PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para cofres de chaves existentes, pode utilizar este cmdlet do PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>-Na CLI para configurar o Cofre de chaves
Para criar um cofre de chaves utilizando a interface de linha de comandos (CLI), consulte [gerir o Cofre de chaves ao utilizar a CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Para a CLI, tem de criar o Cofre de chaves antes de atribuir a política de implementação. Pode fazê-lo utilizando o comando seguinte:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utilizar modelos para configurar o Cofre de chaves
Apesar de utilizar um modelo, tem de definir o `enabledForDeployment` propriedade `true` para o recurso do Cofre de chaves.

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

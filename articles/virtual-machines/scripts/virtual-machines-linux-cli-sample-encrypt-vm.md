---
title: CLI do Azure Script de exemplo - encriptar uma VM com Linux | Microsoft Docs
description: CLI do Azure Script de exemplo - encriptar uma VM com Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/02/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5f5c57a9c5a20e6e6a514b5b4c9d2e040d504983
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Encriptar uma máquina virtual do Linux no Azure

Este script cria um cofre de chaves segura do Azure, as chaves de encriptação, principal de serviço do Azure Active Directory e uma máquina virtual (VM) do Linux. A VM é então encriptada com a chave de encriptação do Cofre de chaves e as credenciais de principal de serviço.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, o Cofre de chaves do Azure, o principal de serviço, a máquina virtual e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ keyvault](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Cria um cofre de chaves do Azure para armazenar os dados seguros, tais como chaves de encriptação. |
| [Criar chave de keyvault AZ](https://docs.microsoft.com/cli/azure/keyvault/key#az_keyvault_key_create) | Cria uma chave de encriptação no Cofre de chaves. |
| [ad AZ SP2 criar-para-rbac](https://docs.microsoft.com/cli/azure/ad/sp#az_ad_sp_create_for_rbac) | Cria um Azure Active Directory principal de serviço para autenticar e controlar o acesso às chaves de encriptação de forma segura. |
| [política de conjunto do keyvault AZ](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Define as permissões no Cofre de chaves para conceder o acesso de principal de serviço para as chaves de encriptação. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Cria a máquina virtual e liga-o para a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser utilizada e credenciais administrativas.  |
| [Ativar de encriptação de vm AZ](https://docs.microsoft.com/cli/azure/vm/encryption#az_vm_encryption_enable) | Ativa a encriptação numa VM com as credenciais de principal de serviço e a chave de encriptação. |
| [Mostrar de encriptação de vm AZ](https://docs.microsoft.com/cli/azure/vm/encryption#az_vm_encryption_show) | Mostra o estado do processo de encriptação de VM. |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação de VM do Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

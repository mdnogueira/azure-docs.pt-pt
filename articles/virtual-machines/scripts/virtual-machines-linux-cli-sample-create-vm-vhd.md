---
title: CLI do Azure Script de exemplo - criar uma VM com um VHD | Microsoft Docs
description: "Script CLI do Azure de exemplo - criar uma VM utilizando um disco rígido virtual."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: 6234473d9f7f0eb18ea85e52273eb82a9ce04da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Criar uma VM com um disco rígido virtual

Este exemplo cria uma máquina virtual utilizando um VHD.
Cria um grupo de recursos, uma conta do storage e um contentor, em seguida, cria uma VM através do carregamento o VHD para o contentor.
Substitui o ssh pública da chave com a chave pública, por isso, que tem acesso à VM.

Irá precisar de um VHD de arranque.
Pode transferir o VHD que utilizámos do https://azclisamples.blob.core.windows.net/vhds/sample.vhd ou utilizar o seu próprio VHD. O script procura `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, máquina virtual, conjunto de disponibilidade, o Balanceador de carga e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [lista de contas de armazenamento AZ](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_list) | Apresenta uma lista de contas de armazenamento |
| [verificação de conta de armazenamento de AZ-name](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_check_name) | Verifica se um nome de conta de armazenamento é válido e que já não existe |
| [lista de chaves de conta de armazenamento AZ](https://docs.microsoft.com/cli/azure/storage/account/keys#az_storage_account_keys_list) | Apresenta uma lista de chaves para as contas de armazenamento |
| [blob de armazenamento AZ existe](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_exists) | Verifica se o blob existe |
| [Criar contentor de armazenamento AZ](https://docs.microsoft.com/cli/azure/storage/container#az_storage_container_create) | Cria um contentor numa conta do storage. |
| [carregamento de blob de armazenamento AZ](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_upload) | Cria um blob no contentor ao carregar o VHD. |
| [lista de vm AZ](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Utilizado com `--query` Verifique se o nome da VM está em utilização. | 
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Cria as máquinas virtuais. |
| [AZ vm acesso conjunto linux-utilizador](https://docs.microsoft.com/cli/azure/vm/access#az_vm_access_set_linux_user) | Repõe a chave SSH para conceder o acesso do utilizador atual para a VM. |
| [AZ vm lista--endereços ip](https://docs.microsoft.com/cli/azure/vm#az_vm_list-ip-addresses) | Obtém o endereço IP da VM que foi criada. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação de VM do Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

---
title: "Script CLI do Azure de exemplo - criação de um anfitrião do Docker | Microsoft Docs"
description: "Script CLI do Azure de exemplo - criação de um anfitrião do Docker"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c1e0be0e305ba4d53d8e26c55a92b63e1291171d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-docker"></a>Criar uma VM com o Docker

Este script cria uma máquina virtual com o Docker ativada e inicia um contentor de Docker NGINX a executar. Depois de executar o script, que pode aceder ao servidor de web NGINX através do FQDN da máquina virtual do Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Docker Host")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar a implementação. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Cria a máquina virtual e liga-o para a placa de rede, a rede virtual, a sub-rede e o grupo de segurança de rede. Este comando também especifica a imagem de máquina virtual a ser utilizada e credenciais administrativas.  |
| [abrir-porta AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_open_port) | Cria uma regra de grupo de segurança de rede para permitir tráfego de entrada. Neste exemplo, a porta 80 está aberta para tráfego HTTP. |
| [conjunto de extensão de vm do Azure](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Adiciona e executa uma extensão da máquina virtual a uma VM. Neste exemplo, a extensão de VM de Docker é utilizada para configurar um anfitrião do Docker.|
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação de VM do Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

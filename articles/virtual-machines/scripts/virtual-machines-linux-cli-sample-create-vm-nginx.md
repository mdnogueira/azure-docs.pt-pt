---
title: CLI do Azure Script de exemplo - criar uma VM com Linux com NGINX | Microsoft Docs
description: CLI do Azure Script de exemplo - criar uma VM com Linux com NGINX
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0a6c33d84f1fab85e6ed2933c47c041ca2e59520
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-nginx"></a>Criar uma VM com NGINX

Este script cria uma Máquina Virtual do Azure e utiliza a extensão de Script de personalizado de Máquina Virtual do Azure para a instalação NGINX. Depois de executar o script, pode aceder a um site de demonstração do endereço IP público da máquina virtual.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Extensão de Script Personalizado

A extensão de script personalizado copia este script para a máquina virtual. O script é executado, em seguida, instalar e configurar um servidor de web NGINX. 

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Cria a máquina virtual. Este comando também especifica a imagem de máquina virtual a ser utilizada e credenciais administrativas.  |
| [abrir-porta AZ vm](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Cria uma regra de grupo de segurança de rede para permitir tráfego de entrada. Neste exemplo, a porta 80 está aberta para tráfego HTTP. |
| [conjunto de extensão de vm do Azure](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Adiciona e executa uma extensão da máquina virtual a uma VM. Neste exemplo, a extensão de script personalizado é utilizada para instalar NGINX.|
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação de VM do Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

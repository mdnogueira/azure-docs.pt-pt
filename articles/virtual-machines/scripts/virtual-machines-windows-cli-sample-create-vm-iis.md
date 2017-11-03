---
title: Exemplo de Script do CLI do Azure - instale o IIS | Microsoft Docs
description: Exemplo de Script do CLI do Azure - instale o IIS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: nepeters
ms.openlocfilehash: 224fbebd40a44dfb2e032150612467af3a8aca8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Guia de introdução criar uma máquina virtual com a CLI do Azure

Este script cria uma máquina de Virtual do Azure a executar o Windows Server 2016 e utiliza a extensão de Script de personalizado de Máquina Virtual do Azure para instalar o IIS. Depois de executar o script, pode aceder o Web site do IIS predefinido no endereço IP público da máquina virtual.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-windows-iis/create-vm-windows-iis.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Cria a máquina virtual e liga-o para a placa de rede, a rede virtual, a sub-rede e o grupo de segurança de rede. Este comando também especifica a imagem de máquina virtual a ser credenciais administrativas e utilizadas.  |
| [abrir-porta AZ vm](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Cria uma regra de grupo de segurança de rede para permitir tráfego de entrada. Neste exemplo, a porta 80 está aberta para tráfego HTTP. |
| [conjunto de extensão de vm do Azure](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Adiciona e executa uma extensão da máquina virtual a uma VM. Neste exemplo, a extensão de script personalizado é utilizada para instalar o IIS.|
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação do Azure Windows VM](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

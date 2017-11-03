---
title: Criar uma VM com Linux utilizando um modelo do Azure com a CLI do Azure 1.0 | Microsoft Docs
description: Crie uma VM com Linux no Azure utilizando a CLI do Azure 1.0 e um modelo Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d4aaa78fcdf3bd9e2e236606f2d3049f464a8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Como criar uma VM com Linux utilizando a CLI do Azure 1.0 um modelo Azure Resource Manager
Este artigo mostra como implementar rapidamente uma Máquina Virtual de Linux utilizando a CLI do Azure 1.0 e um modelo Azure Resource Manager. O artigo requer:

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* o [CLI do Azure 1.0](../../cli-install-nodejs.md) registado com `azure login`.
* a CLI do Azure *tem de estar no* modo Azure Resource Manager`azure config mode arm`.

Também pode implementar rapidamente um modelo da VM do Linux através do [portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#quick-command-summary) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](create-ssh-secured-vm-from-template.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="quick-command-summary"></a>Resumo do Comando Rápido
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Instruções Detalhadas
Os modelos permitem-lhe criar VMs no Azure com definições que pretende personalizar durante a iniciação, tais como nomes de utilizador e nomes de anfitriões. Para este artigo, vamos iniciar um modelo do Azure que utilize uma VM Ubuntu juntamente com um grupo de segurança de rede (NSG) com a porta 22 aberta para SSH.

Os modelos do Resource Manager do Azure são ficheiros JSON que podem ser utilizados para tarefas pontuais simples, como iniciar uma VM Ubuntu como efetuado neste artigo.  Os Modelos do Azure podem também ser utilizados para construir complexas configurações do Azure de ambientes completos, como uma pilha de implementação de teste, desenvolvimento ou de produção.

## <a name="create-the-linux-vm"></a>Criar a VM com Linux
O exemplo de código seguinte mostra como chamar `azure group create` para criar um grupo de recursos e implementar uma VM com Linux protegidas por SSH ao mesmo tempo utilizando [este modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Lembre-se de que no exemplo tem de utilizar nomes que são exclusivos no seu ambiente. Este exemplo utiliza *myResourceGroup* como o nome do grupo de recursos, e *myVM* como o nome da VM.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

O resultado deverá ser semelhante ao do bloco de saída seguinte:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Esse exemplo implementou uma VM utilizando o parâmetro `--template-uri`.  Também pode transferir ou criar um modelo localmente e passar o modelo através do parâmetro `--template-file` com um caminho para o ficheiro de modelo como um argumento. A CLI do Azure pede-lhe os parâmetros exigidos pelo modelo.

## <a name="next-steps"></a>Passos seguintes
Pesquise a [galeria de modelos](https://azure.microsoft.com/documentation/templates/) para descobrir que estruturas de aplicações implementar a seguir.


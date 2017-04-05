---
title: Criar uma VM com Linux utilizando um modelo Azure | Microsoft Docs
description: "Crie uma VM com Linux no Azure através de um modelo do Azure Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: v-livech
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c8b4c8cd7e6e6e07efbaae7ac024160e1e0a3568
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-create-a-linux-vm-using-an-azure-resourec-manager-template"></a>Como criar uma VM com Linux através de um modelo do Azure Resource Manager
Este artigo mostra-lhe como implementar rapidamente uma Máquina Virtual do Linux no Azure através de um Modelo do Azure.  O artigo requer:

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* a [CLI do Azure](../../cli-install-nodejs.md) com sessão iniciada com `azure login`.
* a CLI do Azure *tem de estar no* modo Azure Resource Manager`azure config mode arm`.

Também pode implementar rapidamente um modelo da VM do Linux através do [portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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
O exemplo de código seguinte mostra como chamar `azure group create` para criar um grupo de recursos e implementar uma VM com Linux protegidas por SSH ao mesmo tempo utilizando [este modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Lembre-se de que no exemplo tem de utilizar nomes que são exclusivos no seu ambiente. Este exemplo utiliza `myResourceGroup` como o nome do grupo de recursos e `myVM` como o nome da VM.

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



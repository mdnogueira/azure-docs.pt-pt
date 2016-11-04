---
title: Criar uma VM com Linux utilizando um modelo Azure | Microsoft Docs
description: Crie uma VM com Linux no Azure através de um modelo do Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: v-livech

---
# Criar uma VM com Linux utilizando um modelo do Azure
Este artigo mostra como implementar rapidamente um Computador Virtual com Linux no Azure através de um Modelo do Azure.  O artigo requer uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) com o [CLI do Azure](../xplat-cli-install.md) com sessão iniciada (`azure login`) e no modo Resource Manager (`azure config mode arm`).  Também pode implementar rapidamente uma VM com Linux através do [Portal do Azure](virtual-machines-linux-quick-create-portal.md) ou do [CLI do Azure](virtual-machines-linux-quick-create-cli.md).

## Resumo do Comando Rápido
```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Instruções Detalhadas
Os modelos permitem-lhe criar VMs no Azure com definições que pretende personalizar durante a iniciação, tais como nomes de utilizador e nomes de anfitriões. Para este artigo, vamos iniciar um modelo do Azure que utilize uma VM Ubuntu juntamente com um grupo de segurança de rede (NSG) com a porta 22 aberta para SSH.

Os modelos do Resource Manager do Azure são ficheiros JSON que podem ser utilizados para tarefas pontuais simples, como iniciar uma VM Ubuntu como efetuado neste artigo.  Os Modelos do Azure podem também ser utilizados para construir complexas configurações do Azure de ambientes completos, como uma pilha de implementação de teste, desenvolvimento ou de produção.

## Criar a VM com Linux
O exemplo de código seguinte mostra como chamar `azure group create` para criar um grupo de recursos e implementar uma VM com Linux protegidas por SSH ao mesmo tempo utilizando [este modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Lembre-se de que no exemplo tem de utilizar nomes que são exclusivos no seu ambiente. Este exemplo utiliza `quicksecuretemplate` como o nome do grupo de recursos, `securelinux` como o nome da VM e `quicksecurelinux` como o nome de um subdomínio.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Saída

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Esse exemplo implementou uma VM utilizando o parâmetro `--template-uri`.  Também pode transferir ou criar um modelo localmente e passar o modelo através do parâmetro `--template-file` com um caminho para o ficheiro de modelo como um argumento. A CLI do Azure pede-lhe os parâmetros exigidos pelo modelo.

## Passos seguintes
Pesquise a [galeria de modelos](https://azure.microsoft.com/documentation/templates/) para descobrir que estruturas de aplicações implementar a seguir.

<!--HONumber=Sep16_HO3-->



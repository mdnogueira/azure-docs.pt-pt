---
title: Como criar imagens de VM do Linux do Azure com Packer | Microsoft Docs
description: "Saiba como utilizar Packer criar imagens de máquinas virtuais do Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/18/2017
ms.author: iainfou
ms.openlocfilehash: 1752d2e0a497bf94309a744562cf4462866d6f99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Como utilizar Packer para criar imagens da máquina virtual com Linux no Azure
Cada máquina virtual (VM) no Azure é criada a partir de uma imagem que define a distribuição de Linux e versão do SO. Imagens podem incluir aplicações pré-instaladas e configurações. O Azure Marketplace fornece várias imagens primeira e de terceiros para distribuições mais comuns e ambientes de aplicação, ou pode criar as suas próprias imagens personalizadas e adaptadas às suas necessidades. Este artigo fornece detalhes sobre como utilizar a ferramenta de código aberto [Packer](https://www.packer.io/) para definir e criar imagens personalizadas no Azure.


## <a name="create-azure-resource-group"></a>Criar grupo de recursos do Azure
Durante o processo de compilação, Packer cria temporários recursos do Azure baseia-se a VM de origem. Para essa VM de origem para utilização como uma imagem de captura, tem de definir um grupo de recursos. A saída do processo de compilação de Packer é armazenada neste grupo de recursos.

Crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Criar as credenciais do Azure
Packer autentica com o Azure utilizando um principal de serviço. Um principal de serviço do Azure é uma identidade de segurança que pode utilizar com aplicações, serviços e ferramentas de automatização como Packer. Controlar e definir as permissões para as operações que pode efetuar o principal de serviço no Azure.

Criar um serviço principal com [az ad sp criar-para-rbac](/cli/azure/ad/sp#create-for-rbac) e as credenciais que Packer necessita de saída:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Segue-se um exemplo de saída a partir dos comandos do anteriores:

```azurecli
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Para autenticar para o Azure, também terá de obter o ID de subscrição do Azure com [mostrar de conta az](/cli/azure/account#show):

```azurecli
az account show --query "{ subscription_id: id }"
```

Utilize o resultado destes dois comandos no próximo passo.


## <a name="define-packer-template"></a>Definir Packer modelo
Para criar imagens, crie um modelo como um ficheiro JSON. O modelo, é possível definir construtores e provisioners realizar o processo de compilação real. Packer tem um [provisioner para o Azure](https://www.packer.io/docs/builders/azure.html) que permite-lhe definir os recursos do Azure, tais como as credenciais de principal de serviço criadas no precedente passo.

Crie um ficheiro denominado *ubuntu.json* e cole o seguinte conteúdo. Introduza os seus próprios valores para o seguinte:

| Parâmetro                           | Onde obtê |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Primeira linha do resultado de `az ad sp` criar comando - *appId* |
| *client_secret*                     | Segunda linha de saída de `az ad sp` criar comando - *palavra-passe* |
| *tenant_id*                         | Terceira linha de saída de `az ad sp` criar comando - *inquilino* |
| *SUBSCRIPTION_ID*                   | O resultado da `az account show` comando |
| *managed_image_resource_group_name* | Nome do grupo de recursos que criou no primeiro passo |
| *managed_image_name*                | Nome para a imagem de disco gerido que é criada |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Este modelo cria uma imagem de Ubuntu 16.04 LTS, instala NGINX e deprovisions a VM.

> [!NOTE]
> Se expandir neste modelo para aprovisionar as credenciais de utilizador, ajuste o comando de provisioner deprovisions o agente do Azure para ler `-deprovision` vez `deprovision+user`.
> O `+user` sinalizador remove todas as contas de utilizador a VM de origem.


## <a name="build-packer-image"></a>Compilar Packer imagem
Se ainda não tiver Packer instalado no seu computador local, [siga as instruções de instalação Packer](https://www.packer.io/docs/install/index.html).

Compilar a imagem, especificando o Packer ficheiro do modelo da seguinte forma:

```bash
./packer build ubuntu.json
```

Segue-se um exemplo de saída a partir dos comandos do anteriores:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```


## <a name="create-vm-from-azure-image"></a>Criar a VM da imagem do Azure
Agora pode criar uma VM a partir da imagem com [az vm criar](/cli/azure/vm#create). Especifique a imagem que criou com a `--image` parâmetro. O exemplo seguinte cria uma VM chamada *myVM* de *myPackerImage* e gera chaves SSH, caso ainda não existam:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Demora alguns minutos para criar a VM. Quando tiver sido criada a VM, tome nota do `publicIpAddress` apresentado pela CLI do Azure. Este endereço é utilizado para aceder ao site NGINX através de um browser web.

Para permitir o tráfego da web alcançar a VM, abra a porta 80 da Internet com [az vm open-porta](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testar a VM e NGINX
Agora pode abrir um browser e introduza `http://publicIpAddress` na barra de endereço. Forneça o suas próprias público endereço IP da VM criar o processo. É apresentada a página predefinida de NGINX como no exemplo seguinte:

![Site predefinido do NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Passos seguintes
Neste exemplo, Packer que utilizou para criar uma imagem VM com NGINX já instalado. Pode utilizar esta imagem de VM em conjunto com fluxos de trabalho de implementação existentes, tal como para implementar a sua aplicação para VMs criadas a partir da imagem com Ansible, Chef ou Puppet.

Para modelos de Packer de exemplo adicionais para outros distros Linux, consulte [este repositório do GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
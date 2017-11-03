---
title: "Utilizar a extensão de VM de Docker do Azure com a CLI do Azure 1.0 | Microsoft Docs"
description: "Saiba como utilizar a extensão de VM de Docker rapidamente e segura implementar num ambiente de Docker no Azure utilizando modelos do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a3cbcf63533f4042dcd695e141655c5814bd7068
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Criar um ambiente de Docker no Azure através da extensão de VM de Docker com a CLI do Azure 1.0
Docker é uma gestão de contentores populares e a plataforma de processamento de imagens que permite-lhe trabalhar rapidamente com contentores no Linux (e Windows, bem como). No Azure, existem várias formas como pode implementar Docker consoante as suas necessidades. Este artigo foca-se utilizando a extensão de VM de Docker e modelos Azure Resource Manager. 

Para obter mais informações sobre os métodos de implementação diferentes, incluindo a utilização de Docker máquina e serviços de contentor do Azure, consulte os artigos seguintes:

* Para criar rapidamente protótipos uma aplicação, pode criar um único anfitrião Docker utilizando [Docker máquina](docker-machine.md).
* Para ambientes maiores e mais estáveis, pode utilizar a extensão de VM de Docker do Azure, que também suporta [Docker Compose](https://docs.docker.com/compose/overview/) para gerar implementações de contentores consistente. Este detalhes de artigo com a extensão de VM de Docker do Azure.
* Para criar ambientes prontos para produção, dimensionáveis, que fornecem ferramentas adicionais de agendamento e de gestão, pode implementar um [cluster Docker Swarm nos serviços de contentor do Azure](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](dockerextension.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos 

## <a name="azure-docker-vm-extension-overview"></a>Descrição geral de extensão de VM de Docker do Azure
A extensão da VM do Azure Docker instala e configura o daemon de Docker, o cliente de Docker e o Docker Compose na sua máquina virtual (VM) do Linux. Ao utilizar a extensão de VM de Docker do Azure, terá de mais controlo e funcionalidades que simplesmente a utilização do computador de Docker ou criar o anfitrião de Docker por si. Adicionais estas funcionalidades, tais como [Docker Compose](https://docs.docker.com/compose/overview/), marca a extensão da VM do Azure Docker adequada para ambientes mais robustos de programador ou de produção.

Modelos Azure Resource Manager definem a estrutura do seu ambiente completa. Os modelos permitem-lhe criar e configurar os recursos, tais como o anfitrião de Docker VMs, armazenamento, controlos de acesso baseado em funções (RBAC) e diagnóstico. Pode reutilizar estes modelos para criar implementações adicionais de forma consistente. Para obter mais informações sobre o Azure Resource Manager e modelos, consulte [descrição geral do Gestor de recursos](../../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implementar um modelo com a extensão de VM de Docker do Azure
Vamos utilizar um modelo existente de início rápido para criar uma VM com Ubuntu que utiliza a extensão de VM de Docker do Azure para instalar e configurar o anfitrião de Docker. Pode ver o modelo aqui: [Simple implementação de uma VM com Ubuntu com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

É necessário o [mais recente do Azure CLI](../../cli-install-nodejs.md) instalado e inicia sessão utilizando o modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

Implemente o modelo com a CLI do Azure, especificando o modelo de URI. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westus*. Utilize o seu nome de grupo de recursos e localização da seguinte forma:

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Responda os pedidos para o nome da sua conta do storage, forneça um nome de utilizador e palavra-passe e forneça um nome DNS. O resultado é semelhante ao seguinte exemplo:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

O devolve CLI do Azure para a linha depois de apenas alguns segundos, mas o anfitrião de Docker ainda a ser criada e configurada, a extensão da VM do Azure Docker. Demora alguns minutos para a implementação para concluir. Pode ver os detalhes sobre o Docker anfitrião Estado o `azure vm show` comando.

O exemplo seguinte verifica o estado da VM com o nome *myDockerVM* (o nome predefinido do modelo - não altere este nome) no grupo de recursos denominado *myResourceGroup*. Introduza o nome do grupo de recursos que criou no passo anterior:

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

O resultado a `azure vm show` comando é semelhante ao seguinte exemplo:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Perto da parte superior da saída, consulte o **ProvisioningState** da VM. Quando esta ação apresenta *com êxito*, a implementação foi concluído e pode SSH para a VM.

Para o fim da saída, *FQDN* apresenta o nome de domínio completamente qualificado do anfitrião do Docker. Este FQDN é o que utilizar para SSH para o anfitrião de Docker nos restantes passos.

## <a name="deploy-your-first-nginx-container"></a>Implementar o contentor nginx primeiro
Depois de concluir a implementação, o SSH para o novo anfitrião do Docker do seu computador local. Introduza o seu próprio nome de utilizador e o FQDN da seguinte forma:

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

Depois de a sessão para o anfitrião de Docker, vamos executar um contentor nginx:

```bash
sudo docker run -d -p 80:80 nginx
```

O resultado é semelhante ao seguinte exemplo de como a imagem de nginx é transferida e um contentor iniciado:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Verifique o estado dos contentores em execução no anfitrião do Docker da seguinte forma:

```bash
sudo docker ps
```

O resultado é semelhante ao exemplo seguinte, que mostra que o contentor nginx está em execução e as portas TCP 80 e 443 e a ser reencaminhadas:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver o contentor em ação, abrir um browser e introduza o nome do FQDN do anfitrião do Docker:

![Contentor de ngnix em execução](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Referência de modelo de extensão de VM de Docker do Azure
O exemplo anterior utiliza um modelo existente de início rápido. Também pode implementar a extensão de VM de Docker do Azure com os seus próprios modelos do Resource Manager. Para tal, adicione o seguinte para os modelos do Resource Manager, que define o *vmName* da sua VM corretamente:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Pode encontrar mais instruções sobre como utilizar os modelos do Resource Manager lendo [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Passos seguintes
Pode pretender [configurar o daemon de Docker a porta TCP](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), compreender [segurança Docker](https://docs.docker.com/engine/security/security/), ou implementar contentores utilizando [Docker Compose](https://docs.docker.com/compose/overview/). Para obter mais informações sobre a extensão de VM do Azure Docker em si, consulte o [GitHub projeto](https://github.com/Azure/azure-docker-extension/).

Ler mais informações sobre as opções de implementação de Docker adicionais no Azure:

* [Utilizar o Docker máquina com o controlador do Azure](docker-machine.md)  
* [Introdução ao Docker e Compose para definir e executar uma aplicação de contentor multi numa máquina virtual do Azure](docker-compose-quickstart.md).
* [Implementar um cluster do Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)


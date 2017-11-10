---
title: "Utilizar o Docker máquina criar anfitriões do Linux no Azure | Microsoft Docs"
description: "Descreve como utilizar o Docker máquina para criar os anfitriões de Docker no Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: iainfou
ms.openlocfilehash: efd0b60079017e476b54acfc65ffe8f35ffe4933
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Como utilizar o Docker máquina para criar anfitriões no Azure
Este artigo fornece detalhes sobre como utilizar [Docker máquina](https://docs.docker.com/machine/) criar anfitriões no Azure. O `docker-machine` comando cria uma máquina virtual (VM) do Linux no Azure, em seguida, instala Docker. Em seguida, pode gerir os anfitriões de Docker no Azure com o mesmas ferramentas locais e fluxos de trabalho. Para utilizar o docker máquina no Windows 10, tem de utilizar Linux bash.

## <a name="create-vms-with-docker-machine"></a>Criar as VMs com a máquina do Docker
Em primeiro lugar, obter o ID de subscrição do Azure com [mostrar de conta az](/cli/azure/account#show) da seguinte forma:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Crie o Docker anfitrião VMs no Azure com `docker-machine create` especificando *azure* como o controlador. Para obter mais informações, consulte o [documentação do controlador de Azure do Docker](https://docs.docker.com/machine/drivers/azure/)

O exemplo seguinte cria uma VM chamada *myVM*, com base no plano de "D2 padrão v2", que cria uma conta de utilizador com o nome *azureuser*e abre-se a porta *80* no anfitrião VM. Siga as instruções para iniciar sessão sua conta do Azure e conceder permissões de Docker máquina para criar e gerir recursos.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2" \
    myvm
```

O resultado semelhante ao seguinte exemplo:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvmdocker) Completed machine pre-create checks.
Creating machine...
(myvmdocker) Querying existing resource group.  name="docker-machine"
(myvmdocker) Creating resource group.  name="docker-machine" location="westus"
(myvmdocker) Configuring availability set.  name="docker-machine"
(myvmdocker) Configuring network security group.  name="myvmdocker-firewall" location="westus"
(myvmdocker) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvmdocker) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvmdocker) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvmdocker) Creating public IP address.  name="myvmdocker-ip" static=false
(myvmdocker) Creating network interface.  name="myvmdocker-nic"
(myvmdocker) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvmdocker) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvmdocker"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvmdocker
```

## <a name="configure-your-docker-shell"></a>Configurar a shell do Docker
Para ligar ao seu anfitrião de Docker no Azure, defina as definições de ligação adequado. Conforme indicado no final de saída, ver as informações de ligação para o anfitrião de Docker da seguinte forma: 

```bash
docker-machine env myvmdocker
```

O resultado é semelhante ao seguinte exemplo:

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvmdocker)
```

Para definir a ligação definições que pode executam o comando de configuração sugeridos (`eval $(docker-machine env myvmdocker)`), ou pode definir as variáveis de ambiente manualmente. 

## <a name="run-a-container"></a>Executar um contentor
Para ver um contentor em ação, permite executar um webserver NGINX básico. Criar um contentor com `docker run` e expor a porta 80 para o tráfego web da seguinte forma:

```bash
docker run -d -p 80:80 --restart=always nginx
```

O resultado é semelhante ao seguinte exemplo:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Vista de contentores com `docker ps`. O resultado de exemplo seguinte mostra o contentor NGINX em execução com a porta 80 exposto:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>O contentor de teste
Obter o endereço IP público do anfitrião de Docker da seguinte forma:


```bash
docker-machine ip myvmdocker
```

Para ver o contentor em ação, abra um browser e introduza o endereço IP público que anotou na saída do comando anterior:

![Contentor de ngnix em execução](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Passos seguintes
Também pode criar anfitriões com o [extensão da VM Docker](dockerextension.md). Para obter exemplos sobre como utilizar o Docker Compose, consulte [começar com o Docker e Compose no Azure](docker-compose-quickstart.md).

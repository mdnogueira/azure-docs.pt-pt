---
title: Utilize Docker Compose numa VM com Linux no Azure | Microsoft Docs
description: "Como utilizar o Docker e Compose em máquinas virtuais do Linux com a CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: e187b51769754a757991f7b5bdb335e62512b488
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Introdução ao Docker e Compose para definir e executar uma aplicação de contentor multi no Azure
Com [Compose](http://github.com/docker/compose), utilizar um ficheiro de texto simples para definir uma aplicação consiste de vários contentores de Docker. Em seguida, rotação de cópia de segurança da aplicação num único comando que tudo para implementar o seu ambiente definido. Por exemplo, este artigo mostra como configurar rapidamente um blogue do WordPress com um base de dados do SQL de MariaDB numa VM com Ubuntu de back-end. Também pode utilizar Compose para configurar aplicações mais complexas.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Configurar uma VM com Linux como um anfitrião do Docker
Pode utilizar vários procedimentos do Azure e imagens disponíveis ou modelos do Resource Manager no Azure Marketplace para criar uma VM com Linux e configurá-lo como um anfitrião de Docker. Por exemplo, consulte [através da extensão de VM de Docker para implementar o seu ambiente](dockerextension.md) criar rapidamente uma VM com Ubuntu com a extensão de VM de Docker do Azure, utilizando um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Quando utiliza a extensão de VM de Docker, a VM é automaticamente configurada como um anfitrião do Docker e Compose já está instalado.


### <a name="create-docker-host-with-azure-cli-20"></a>Criar anfitrião Docker com o Azure CLI 2.0
Instalar a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) e início de sessão para um Azure conta através de [início de sessão az](/cli/azure/#login).

Em primeiro lugar, crie um grupo de recursos para o seu ambiente de Docker com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implementar uma VM com [criar a implementação do grupo az](/cli/azure/group/deployment#create) que inclui a extensão de VM de Docker do Azure a partir do [este modelo Azure Resource Manager no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Fornecer os seus próprios valores exclusivos para *newStorageAccountName*, *adminUsername*, *adminPassword*, e *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Demora alguns minutos para a implementação para concluir. Assim que a implementação estiver concluída, [mover para o passo seguinte](#verify-that-compose-is-installed) para SSH para a VM. 

Opcionalmente, em vez disso, devolver o controlo ao pedido e permitem a implementação continuar em segundo plano, adicionar o `--no-wait` sinalizador para o comando anterior. Este processo permite-lhe efetuar outras tarefas no CLI enquanto continua a implementação de alguns minutos. Em seguida, pode ver detalhes sobre o estado do anfitrião de Docker com [mostrar de vm az](/cli/azure/vm#show). O exemplo seguinte verifica o estado da VM com o nome *myDockerVM* (o nome predefinido do modelo - não altere este nome) no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

Quando este comando devolve *com êxito*, a implementação foi concluído e pode SSH para a VM no passo seguinte.


## <a name="verify-that-compose-is-installed"></a>Certifique-se de que o Compose está instalado
Para ver os detalhes da sua VM, incluindo o nome DNS, utilize [mostrar de vm az](/cli/azure/vm#show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH para o novo anfitrião do Docker. Forneça o seu próprio nome DNS da seguinte forma:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Para verificar que Compose está instalado na VM, execute o seguinte comando:

```bash
docker-compose --version
```

Poderá ver um resultado semelhante ao *1.6.2 de compose docker, crie 4d 72027*.

> [!TIP]
> Se utilizou o outro método para criar um anfitrião do Docker e tem de instalar a compor por si, consulte o [compor documentação](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Criar um ficheiro de configuração docker-Compose.yml
Em seguida, crie um `docker-compose.yml` ficheiro, que é apenas um ficheiro de configuração texto, para definir os contentores de Docker para executar na VM. O ficheiro Especifica a imagem para executar em cada contentor (ou pode ser uma compilação de um Dockerfile), as variáveis de ambiente necessário e dependências, portas e as ligações entre contentores. Para obter detalhes sobre a sintaxe de ficheiro yml, consulte [compor a referência de ficheiro](https://docs.docker.com/compose/compose-file/).

Criar um *docker-Compose.yml* ficheiro. Utilize o editor de texto preferido para adicionar alguns dados ao ficheiro. O exemplo seguinte cria o ficheiro com um pedido para `sensible-editor` e escolha um editor que pretende utilizar:

```bash
sensible-editor docker-compose.yml
```

Cole o seguinte exemplo no seu ficheiro Docker Compose. Esta configuração utilize imagens a partir de [DockerHub registo](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (open source para blogging e o conteúdo de sistema de gestão) e um base de dados MariaDB SQL de back-end ligado. Introduza os seus próprios *MYSQL_ROOT_PASSWORD* da seguinte forma:

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Os contentores de começar com Compose
No mesmo diretório do seu *docker-Compose.yml* ficheiros, execute o seguinte comando (dependendo do seu ambiente, poderá ter de executar `docker-compose` utilizando `sudo`):

```bash
docker-compose up -d
```

Este comando inicia os contentores de Docker especificados no *docker-Compose.yml*. Demorará um minuto ou dois para este passo concluir. Poderá ver um resultado semelhante ao seguinte exemplo:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Certifique-se de utilizar o **-d** opção no arranque, para que os contentores executam continuamente em segundo plano.


Para verificar se os contentores estão a, escreva `docker-compose ps`. Deverá ver algo semelhante ao seguinte:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Agora pode ligar ao WordPress diretamente na VM na porta 80. Abra um browser e introduza o nome DNS da sua VM (tais como `http://mypublicdns.eastus.cloudapp.azure.com`). Deverá ver o WordPress ecrã principal, onde pode concluir a instalação e começar a utilizar a aplicação.

![Ecrã de início do WordPress][wordpress_start]

## <a name="next-steps"></a>Passos seguintes
* Vá para o [Guia do utilizador de extensão de VM de Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) para mais opções configurar Docker e Compose na sua VM de Docker. Por exemplo, uma opção é colocar o ficheiro de yml Compose (convertido em JSON) diretamente na configuração da extensão de VM de Docker.
* Veja o [compor a referência da linha de comandos](http://docs.docker.com/compose/reference/) e [Guia do utilizador](http://docs.docker.com/compose/) para obter mais exemplos de criar e implementar aplicações de várias contentor.
* Está a utilizar um modelo Azure Resource Manager, o próprio ou um contribuíram do [Comunidade](https://azure.microsoft.com/documentation/templates/), para implementar uma VM do Azure com o Docker e uma aplicação com o Compose. Por exemplo, o [implementar um blogue do WordPress com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) modelo utiliza Docker e Compose implementar rapidamente WordPress com um back-end de MySQL numa VM com Ubuntu.
* Tente integrar o Docker Compose com um cluster Docker Swarm. Consulte [utilizando compor com Swarm](https://docs.docker.com/compose/swarm/) para cenários.

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png

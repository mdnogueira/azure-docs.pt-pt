---
title: "Criar os anfitriões de Docker no Azure com o Docker máquina | Microsoft Docs"
description: "Descreve a utilização da máquina de Docker para criar os anfitriões de docker no Azure."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 766d327a87ed13e04166d71c3d9ae0a1e7a66d19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Criar Anfitriões do Docker no Azure com Máquina do Docker
Executar [Docker](https://www.docker.com/) contentores necessita de um anfitrião VM a executar o daemon de docker.
Este tópico descreve como utilizar o [docker máquina](https://docs.docker.com/machine/) comando para criar novas VMs do Linux, configurados com o daemon de Docker, em execução no Azure. 

**Nota:** 

* *Este artigo depende 0.9.0-rc2 de versão da máquina de docker ou superior*
* *Contentores do Windows serão suportados através do docker máquina num futuro próximo*

## <a name="create-vms-with-docker-machine"></a>Criar as VMs com a máquina do Docker
Crie docker anfitrião VMs no Azure com o `docker-machine create` comando utilizando a `azure` controlador. 

O controlador do Azure requer o ID de subscrição. Pode utilizar o [CLI do Azure](cli-install-nodejs.md) ou [Portal do Azure](https://portal.azure.com) ao obter a sua subscrição do Azure. 

**No Portal do Azure**

* Selecione **subscrições** da página de navegação esquerdo e o id de subscrição de cópia.

**Com a CLI do Azure**

* Tipo ```azure account list``` e copie o id de subscrição.

Tipo `docker-machine create --driver azure` para ver as opções e os respetivos valores predefinidos.
Também pode ver o [documentação do controlador de Azure do Docker](https://docs.docker.com/machine/drivers/azure/) para obter mais informações. 

O exemplo seguinte baseia-se após o [valores predefinidos](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), mas, opcionalmente, defina estes valores: 

* dns do Azure para o nome associado ao IP público e certificados gerados. Este é o nome DNS da sua máquina virtual. A VM pode, em seguida, em segurança parar, versão de IP dinâmico e fornecem a capacidade de voltar a ligar depois da vm entrar novamente com um IP de novo. O prefixo do nome tem de ser exclusivo para essa região UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Abra a porta 80 na VM para acesso de internet de saída
* tamanho da VM para utilizar o armazenamento premium mais rápido
* armazenamento Premium utilizado para o disco da vm

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Escolha um anfitrião de docker com máquina docker
Assim que tiver uma entrada na máquina de docker para o anfitrião, pode definir o anfitrião predefinido ao executar comandos de docker.

## <a name="using-powershell"></a>Com o PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Utilizar Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Agora, pode executar comandos de docker contra o anfitrião especificado

```
docker ps
docker info
```

## <a name="run-a-container"></a>Executar um contentor
Com um anfitrião configurado, pode agora executar um servidor web simples para testar se o anfitrião foi configurado corretamente.
Aqui vamos utilizar uma imagem de padrão nginx, especifique se deve escutar na porta 80 e que, se o anfitrião de VM é reiniciado, o contentor irá reiniciar bem (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

A saída deve ter um aspeto semelhante a seguinte:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>O contentor de teste
Examine os contentores em execução com `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

E, para ver o contentor em execução, escreva `docker-machine ip <VM name>` para localizar o endereço IP a introduzir no browser:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Contentor de ngnix em execução](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Resumo
Com o docker-máquina, pode aprovisionar facilmente os anfitriões de docker no Azure para as validações de anfitriões de docker individuais.
Para produção de alojamento de contentores, consulte o [serviço de contentor do Azure](http://aka.ms/AzureContainerService)

Para desenvolver aplicações do .NET Core com o Visual Studio, consulte [Docker Tools para Visual Studio](http://aka.ms/DockerToolsForVS)


---
title: "Tutorial de instâncias de contentor do Azure – preparar a sua aplicação"
description: "Prepare uma aplicação para implementação no Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 52d99411b2dc9ae9c3f2ebd3b9f346973a91e7c9
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Criar um contentor para implementação em Instâncias do Azure Container

As Instâncias do Azure Container permitem implementar contentores do Docker na infraestrutura do Azure sem que seja necessário aprovisionar máquinas virtuais ou adotar serviços de nível superior. Neste tutorial, pode criar uma aplicação web pequeno Node.js e pacote-lo num contentor que pode ser executado utilizando as instâncias de contentor do Azure. Abordamos:

> [!div class="checklist"]
> * A clonagem de origens das aplicações a partir do GitHub
> * A criação de imagens de contentores a partir das origens das aplicações
> * Os testes às imagens em ambientes do Docker locais

Nos tutoriais subsequentes, carregue a imagem para um registo de contentor do Azure e, em seguida, implementá-la para instâncias de contentor do Azure.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial requer que está a executar a CLI do Azure versão 2.0.20 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli).

Este tutorial assume uma compreensão básica do núcleo conceitos do Docker como contentores, imagens de contentor e basic `docker` comandos. Se for necessário, veja [Get started with Docker]( https://docs.docker.com/get-started/) (Introdução ao Docker) para obter um manual sobre as noções básicas dos contentores.

Para concluir este tutorial, precisa de um ambiente de desenvolvimento do Docker. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Shell de nuvem do Azure não inclui os componentes de Docker necessários para concluir cada passo neste tutorial. Por conseguinte, recomendamos uma instalação local do ambiente de desenvolvimento CLI do Azure e Docker.

## <a name="get-application-code"></a>Obter o código da aplicação

O exemplo deste tutorial inclui uma aplicação Web simples criada em [Node.js](http://nodejs.org). A aplicação serve uma página HTML estática e tem este aspeto:

![Tutorial de aplicação mostrada no browser][aci-tutorial-app]

Utilize o git para transferir o exemplo:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Criar a imagem do contentor

O Dockerfile fornecido no repositório de exemplos mostra como é que o contentor é criado. Começa com uma [imagem Node.js oficial][dockerhub-nodeimage] baseada em [Alpine Linux](https://alpinelinux.org/), uma pequena distribuição adequada para utilização com contentores. Depois, copia os ficheiros da aplicação para o contentor, instala as dependências com o Node Package Manager e, por fim, inicia a aplicação.

```Dockerfile
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Utilize o comando `docker build` para criar a imagem do contentor, identificando-a como *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Utilize `docker images` para ver a imagem criada:

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Executar o contentor localmente

Antes de tentar implementar o contentor nas Instâncias do Azure Container, execute-o localmente, para confirmar que está a funcionar. O comutador `-d` permite que o contentor seja executado em segundo plano, ao passo que, com `-p`, pode mapear uma porta arbitrária do computador para a porta 80 do contentor.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Abra o browser em http://localhost:8080 para confirmar que o contentor está em execução.

![Executar a aplicação localmente no browser][aci-tutorial-app-local]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma imagem de contentor que pode ser implementada em Instâncias do Azure Container. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Clonar a origem da aplicação a partir do GitHub
> * Imagens de contentor criado a partir da origem de aplicação
> * Testado localmente o contentor

Avance para o próximo tutorial para saber mais sobre o armazenamento das imagens de contentores em registos do Azure Container Registry.

> [!div class="nextstepaction"]
> [Push images to Azure Container Registry](./container-instances-tutorial-prepare-acr.md) (Enviar imagens para o Azure Container Registry)

<!-- LINKS -->
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png
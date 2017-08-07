---
title: "Tutorial do Azure Container Instances – Preparar a sua aplicação | Documentos do Azure"
description: "Prepare uma aplicação para implementação no Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 07ad1a6edbcb4d6160b37b4923586e23058f3c04
ms.contentlocale: pt-pt
ms.lasthandoff: 08/02/2017

---

# <a name="create-container-for-deployment-to-azure-container-instances"></a>Criar um contentor para implementação em Instâncias do Azure Container

As Instâncias do Azure Container permitem implementar contentores do Docker na infraestrutura do Azure sem que seja necessário aprovisionar máquinas virtuais ou adotar serviços de nível superior. Neste tutorial, vai criar uma aplicação Web simples em Node.js e empacotá-lo num contentor que pode ser executado com Instâncias do Azure Container. Vamos abordar:

> [!div class="checklist"]
> * A clonagem de origens das aplicações a partir do GitHub  
> * A criação de imagens de contentores a partir das origens das aplicações
> * Os testes às imagens em ambientes do Docker locais

Nos tutoriais subsequentes, vai carregar a imagem para um registo do Azure Container Registry e, em seguida, vai implementá-lo em Instâncias do Azure Container.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial pressupõe conhecimentos básicos dos principais conceitos do Docker, como contentores, imagens de contentores e comandos simples do Docker. Se for necessário, veja [Get started with Docker]( https://docs.docker.com/get-started/) (Introdução ao Docker) para obter um manual sobre as noções básicas dos contentores. 

Para concluir este tutorial, precisa de um ambiente de desenvolvimento do Docker. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="get-application-code"></a>Obter o código da aplicação

O exemplo deste tutorial inclui uma aplicação Web simples criada em [Node.js](http://nodejs.org). A aplicação serve uma página HTML estática e tem este aspeto:

![Tutorial de aplicação mostrada no browser][aci-tutorial-app]

Utilize o git para transferir o exemplo:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Criar a imagem do contentor

O Dockerfile fornecido no repositório de exemplos mostra como é que o contentor é criado. Começa com uma [imagem Node.js oficial][dockerhub-nodeimage] baseada em [Alpine Linux](https://alpinelinux.org/), uma pequena distribuição adequada para utilização com contentores. Depois, copia os ficheiros da aplicação para o contentor, instala as dependências com o Node Package Manager e, por fim, inicia a aplicação.

```
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
> * Clonagem da origem da aplicação a partir do GitHub  
> * A criação de imagens de contentores a partir das origens das aplicações
> * Testar o contentor localmente

Avance para o próximo tutorial para saber mais sobre o armazenamento das imagens de contentores em registos do Azure Container Registry.

> [!div class="nextstepaction"]
> [Push images to Azure Container Registry](./container-instances-tutorial-prepare-acr.md) (Enviar imagens para o Azure Container Registry)

<!-- LINKS -->
[dockerhub-nodeimage]: https://hub.docker.com/r/library/node/tags/8.2.0-alpine/

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

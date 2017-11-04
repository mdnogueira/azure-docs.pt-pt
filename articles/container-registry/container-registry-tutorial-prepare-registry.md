---
title: "Tutorial de registo de contentor do Azure – preparar um registo de contentor do Azure georreplicação"
description: "Criar um registo de contentor do Azure, configure a georreplicação, preparar uma imagem de Docker e implementá-la ao registo. Parte de um de uma série de três partes."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: Docker, contentores, registo, Azure
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 75408dc88b23b615971a23dc6235c563229d75aa
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="prepare-a-geo-replicated-azure-container-registry"></a>Preparar um registo de contentor do Azure georreplicação

Um registo de contentor do Azure é um registo de Docker privado implementado no Azure, pode manter o fecho de rede para as implementações. Este conjunto de três artigos tutorial, irá aprender a utilizar a georreplicação para implementar uma aplicação de web de ASP.NET Core em execução num contentor Linux a dois [Web Apps para contentores](../app-service/containers/index.yml) instâncias. Verá como Azure implementa automaticamente a imagem para cada instância de aplicação Web do repositório georreplicação mais próximo.

Neste tutorial, parte de um de uma série de três partes:

> [!div class="checklist"]
> * Criar um registo de contentor do Azure georreplicação
> * Clonar o código fonte da aplicação a partir do GitHub
> * Compilar um Docker imagem do contentor da origem da aplicação
> * Enviar a imagem de contentor para o seu registo

Nos tutoriais subsequentes, implementa o contentor a partir do seu registo privado a uma aplicação web em execução em duas regiões do Azure. Em seguida, atualizar o código na aplicação e atualizar ambas as instâncias da aplicação Web com um único `docker push` ao seu registo.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial requer que está a executar a CLI do Azure versão 2.0.20 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).

Este tutorial pressupõe conhecimentos básicos dos principais conceitos do Docker, como contentores, imagens de contentores e comandos simples do Docker. Se for necessário, veja [Get started with Docker]( https://docs.docker.com/get-started/) (Introdução ao Docker) para obter um manual sobre as noções básicas dos contentores.

Para concluir este tutorial, precisa de um ambiente de desenvolvimento do Docker. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Shell de nuvem do Azure não inclui os componentes de Docker necessários para concluir cada passo neste tutorial. Por conseguinte, recomendamos uma instalação local do ambiente de desenvolvimento CLI do Azure e Docker.

> [!IMPORTANT]
> A funcionalidade de georreplicação do registo de contentor do Azure está atualmente na **pré-visualização**. Pré-visualizações ficam disponíveis para si condition que está a concordar com o [termos de utilização suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade pode ser alterada antes da disponibilidade geral (DG).
>

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Inicie sessão no [Portal do Azure](http://portal.azure.com).

Selecione **novo** > **contentores** > **registo de contentor do Azure**.

![Criar um registo de contentor no portal do Azure][tut-portal-01]

Configure o seu registo de novo com as seguintes definições:

* **Nome do registo**: criar um nome de registo que é globalmente exclusivo no Azure e contém apenas carateres alfanuméricos 5 50
* **Grupo de recursos**: **criar novo** > `myResourceGroup`
* **Localização**:`West US`
* **Utilizador de Admin**: `Enable` (necessário para a aplicação Web para contentores solicitar imagens)
* **SKU**: `Premium` (necessário para georreplicação)

Selecione **criar** para implementar a instância ACR.

![Criar um registo de contentor no portal do Azure][tut-portal-02]

Em todo o resto deste tutorial, utilizamos `<acrName>` como um marcador de posição para o contentor **nome do registo** que escolheu.

> [!TIP]
> Porque os registos do contentor do Azure são, normalmente, os recursos que são utilizados em vários anfitriões do contentor, recomendamos que crie o seu registo no seu próprio grupo de recursos. Como configurar registos georreplicação e webhooks, estes recursos adicionais são colocados no mesmo grupo de recursos.
>

## <a name="configure-geo-replication"></a>Configurar georreplicação

Agora que tem um registo de Premium, pode configurar a georreplicação. Aplicação web, que configura no tutorial seguinte para executar em duas regiões, em seguida, pode solicitar as respectivas imagens contentor a partir do registo mais próximo.

Navegue para o novo registo de contentor do Azure portal e selecione **replicações** em **serviços**:

![Replicações no registo do contentor de portal do Azure da IU][tut-portal-03]

Um mapa é apresentado a verdes hexagons que representa a regiões do Azure disponíveis para georreplicação a mostrar:

 ![Mapa de região no portal do Azure][tut-map-01]

Replicar o registo para a região EUA leste, selecionando o respetivo hexagon verde, em seguida, selecione **criar** em **criar replicação**:

 ![Criar a IU de replicação no portal do Azure][tut-portal-04]

Quando a replicação estiver concluída, o portal reflete *pronto* para ambas as regiões. Utilize o **atualizar** botão para atualizar o estado da replicação; pode demorar um minuto ou para as réplicas a ser criada e sincronizada.

![Estado de replicação da IU no portal do Azure][tut-portal-05]

## <a name="container-registry-login"></a>Início de sessão de registo de contentor

Agora que configurou a georreplicação, criar uma imagem de contentor e enviá-lo para o registo. Tem primeiro de iniciar sessão para a instância ACR antes de enviar imagens à mesma. Com [básicas, Standard e Premium SKUs](container-registry-skus.md), pode autenticar utilizando a identidade do Azure.

Utilize o [início de sessão do az acr](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) comando para autenticar e coloca em cache as credenciais para o registo. Substitua `<acrName>` com o nome do registo que criou nos passos anteriores.

```azurecli
az acr login --name <acrName>
```

O comando devolve `Login Succeeded` quando concluir.

## <a name="get-application-code"></a>Obter o código da aplicação

O exemplo neste tutorial inclui uma aplicação web pequeno criada com [ASP.NET Core](http://dot.net). A aplicação funciona de uma página HTML que apresenta a região a partir da qual a imagem foi implementada pelo registo de contentor do Azure.

![Tutorial de aplicação mostrada no browser][tut-app-01]

Utilize o git para transferir o exemplo para um diretório local, e `cd` para o diretório:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Atualizar Dockerfile

Dockerfile incluído no exemplo mostra como o contentor é criado. Inicia a partir de um oficial [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore) imagem, copia os ficheiros da aplicação para o contentor, instala as dependências, compila o resultado utilizando oficial [aspnetcore compilação](https://store.docker.com/community/images/microsoft/aspnetcore-build) imagem e por fim, cria uma imagem de aspnetcore otimizada.

O Dockerfile está localizado em `./AcrHelloworld/Dockerfile` na origem de clonado.

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

A aplicação no *acr olámundo* imagem tenta determinar a região a partir do qual o respectivo contentor foi implementado ao consultar o DNS para obter informações sobre o servidor de início de sessão no registo. Tem de especificar o URL do servidor de início de sessão do registo no `DOCKER_REGISTRY` variável de ambiente no Dockerfile.

Em primeiro lugar, obter o URL do servidor de início de sessão no registo com o `az acr show` comando. Substitua `<acrName>` com o nome do registo que criou nos passos anteriores.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Saída:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Em seguida, atualize o `DOCKER_REGISTRY` linha com o URL de servidor de início de sessão do seu registo. Neste exemplo, vamos atualizar a linha para refletir o nome do registo nosso exemplo, *uniqueregistryname*:

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Compilar a imagem do contentor

Agora que atualizou o Dockerfile com o URL do seu registo, pode utilizar `docker build` para criar a imagem de contentor. Execute o seguinte comando para criar a imagem e marcá-la com o URL do seu registo privado, substituindo novamente `<acrName>` com o nome do seu registo:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Várias linhas de saída são apresentadas como a imagem de Docker baseia-se (mostrados aqui truncada):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Utilize o `docker images` comando para ver a imagem incorporada:

```bash
docker images
```

Saída:

```bash
REPOSITORY                                     TAG                 IMAGE ID            CREATED              SIZE
uniqueregistryname.azurecr.io/acr-helloworld   v1                  c9ca1763cfb1        About a minute ago   285MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Imagem de push para o registo de contentor do Azure

Por último, utilize o `docker push` comando para emitir o *acr olámundo* imagem ao seu registo. Substitua `<acrName>` com o nome do seu registo.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Porque que configurou o registo a georreplicação, a imagem automaticamente é replicada para ambos os *EUA oeste* e *EUA Leste* regiões com este único `docker push` comando.

Saída:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
9716cfe18412: Pushed
074867a942d5: Pushed
a77666945b96: Pushed
953ff32f2036: Pushed
aa2e77726d3c: Pushed
98b800c91d50: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:c515bcebf249b591b558318e2d0ec21d1320340dbf335730eb32372ff7d34255 size: 1792
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um registo de contentor privado, georreplicação, criado uma imagem de contentor e, em seguida, instalada dessa imagem no seu registo. Ao seguir os passos neste tutorial, pode:

> [!div class="checklist"]
> * Criar um registo de contentor do Azure georreplicação
> * Aplicação clonada o código de origem a partir do GitHub
> * Criada uma imagem de contentor do Docker da origem da aplicação
> * A imagem do contentor é feito o push para o registo

Avançar para o próximo tutorial para saber mais sobre como implementar o contentor para várias aplicações Web para instâncias de contentores, utilizar a georreplicação para servir as imagens localmente.

> [!div class="nextstepaction"]
> [Implementar a aplicação web a partir do registo de contentor do Azure](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png

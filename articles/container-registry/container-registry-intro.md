---
title: Criar registos privados de contentores Docker no Azure | Microsoft Docs
description: "Introdução ao serviço de Registo de Contentores do Azure, que proporciona registos do Docker baseado na cloud, geridos e privados."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: ba82a5e25750fc6d9bd047ace3abb087e5dedfb3
ms.contentlocale: pt-pt
ms.lasthandoff: 07/15/2017

---
# <a name="introduction-to-private-docker-container-registries"></a>Introdução aos registos privados de contentores Docker


O Registo de Contentores do Azure é um serviço de [registo do Docker](https://docs.docker.com/registry/) gerido e baseado no Docker Registry 2.0. Crie e mantenha registos de contentores do Azure para armazenar e gerir as suas imagens privadas do [contentor do Docker](https://www.docker.com/what-docker). Utilize os registos de contentores no Azure com os seus pipelines atuais de desenvolvimento e implementação e beneficie dos conhecimentos da comunidade do Docker.

Para obter informações sobre o Docker e os contentores, veja:

* [Docker user guide (Guia de utilizador do Docker)](https://docs.docker.com/engine/userguide/)




## <a name="use-cases"></a>Casos de utilização
Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações contentorizadas em clusters de anfitriões, incluindo [CD/SO](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) e [Kubernetes](http://kubernetes.io/docs/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Container Service](../container-service/index.yml), o [Serviço de Aplicações](/app-service/index.md), o [Batch](../batch/index.md), o [Service Fabric](/azure/service-fabric/) e outros.

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. Por exemplo, podem segmentar um registo de contentores de uma ferramenta de integração contínua e programação, como o [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) ou o [Jenkins](https://jenkins.io/).





## <a name="key-concepts"></a>Conceitos-chave
* **Registo** - crie um ou mais registos de contentores na sua subscrição do Azure. Cada registo tem por base uma [conta de armazenamento](../storage/storage-introduction.md) standard do Azure na mesma localização. Tire partido do armazenamento local e perto da rede das imagens do seu contentor ao criar um registo na mesma localização do Azure das suas implementações. Os nomes de registo completamente qualificados têm o formato `myregistry.azurecr.io`.

  Para [controlar o acesso](container-registry-authentication.md) a um registo de contentores, utilize um [principal de serviço](../active-directory/active-directory-application-objects.md) baseado no Azure Active Directory ou uma conta de administrador fornecida. Execute o comando `docker login` padrão para se autenticar num registo.

* **Registo Gerido** - uma camada que oferece capacidades adicionais para os registos em três SKUs - Básico, Standard e Premium. As imagens nestes SKUs são armazenadas em Contas de Armazenamento geridas pelo serviço de Registos de Contentores do Azure, o que melhora a fiabilidade e permite novas funcionalidades. As novas capacidades incluem a integração de webhooks, autenticação do repositório no Azure Active Directory e suporte para a funcionalidade de eliminação. Os utilizadores podem escolher entre registos geridos ou criar um registo apoiado pelas suas Contas de Armazenamento durante a criação de registos.

* **Repositório** - um registo contém um ou mais repositórios, que são grupos de imagens do contentor. O Registo de Contentores do Azure suporta espaços de nomes de repositórios com múltiplos níveis. Esta funcionalidade permite-lhe agrupar coleções de imagens relacionadas com uma aplicação específica ou uma coleção de aplicações para equipas de programação ou operacionais específicas. Por exemplo:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representa uma imagem transversal a toda a empresa
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representa uma imagem utilizada para criar aplicações .NET, partilhadas em todo o departamento de garantias
  * `myregistry.azrecr.io/warrantydept/customersubmissions/web` representa uma imagem Web, agrupada na aplicação de submissões do cliente, que é propriedade do departamento de garantias

* **Imagem** - armazenadas num repositório, as imagens são um instantâneo só de leitura de contentores do Docker. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório.

* **Contentor** - um contentor define uma aplicação de software e as respetivas dependências, envoltas num sistema de ficheiros completo, que inclui código, tempo de execução, ferramentas do sistema e bibliotecas. Execute os contentores do Docker com base nas imagens do Windows ou do Linux que extrai de um registo de contentores. Os contentores executados num computador individual partilham o kernel do sistema operativo. Os contentores do Docker são totalmente portáteis para as principais distribuições de Linux e para Mac e Windows.




## <a name="next-steps"></a>Passos seguintes
* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Push your first image using the Docker CLI (Enviar a sua primeira imagem com a CLI do Docker)](container-registry-get-started-docker-cli.md)
* Para criar uma integração contínua e um fluxo de trabalho de implementação através dos Visual Studio Team Services, do Azure Container Service e do Azure Container Registry, veja [este tutorial](../container-service/container-service-docker-swarm-setup-ci-cd.md).
* Se quiser configurar um registo privado do Docker no Azure (sem um ponto final público), veja [Deploying Your Own Private Docker Registry on Azure (Implementar o Seu Primeiro Registo Privado do Docker no Azure)](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md).


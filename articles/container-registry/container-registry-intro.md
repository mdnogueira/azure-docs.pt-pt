---
title: "Introdução ao Registo de Contentores do Azure | Microsoft Docs"
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
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: 806ffa8ce0c434d2a6cc1c7ec3a6e89be9145679
ms.openlocfilehash: 6661766d71d56e49b8840aeb250c105e132b9b04

---
# <a name="what-is-azure-container-registry"></a>O que é o Registo de Contentores do Azure?
> [!NOTE]
> O Registo de Contentores está em pré-visualização atualmente.


O Registo de Contentores do Azure é um serviço de [registo do Docker](https://docs.docker.com/registry/) gerido e baseado no Docker Registry v2. Crie e mantenha registos de contentores do Azure para armazenar e gerir as suas imagens privadas do [contentor do Docker](https://www.docker.com/what-docker). Utilize os registos de contentores no Azure com os seus pipelines atuais de desenvolvimento e implementação e beneficie dos conhecimentos da comunidade do Docker.

Para obter informações sobre o Docker e os contentores, veja:

* [Docker user guide (Guia de utilizador do Docker)](https://docs.docker.com/engine/userguide/)
* [Anúncio da pré-visualização do Registo de Contentores do Azure](https://azure.microsoft.com/blog/azure-container-registry-preview/) 

## <a name="key-concepts"></a>Conceitos-chave
* **Registo** - crie um ou mais registos de contentores na sua subscrição do Azure. Cada registo tem por base uma [conta de armazenamento](../storage/storage-introduction.md) standard do Azure na mesma localização. Crie um registo na mesma localização do Azure das suas implementações para tirar partido do armazenamento local e perto da rede das imagens do seu contentor. 

  Os registos são nomeados num domínio raiz com base no [inquilino do Azure Active Directory](../active-directory/active-directory-howto-tenant.md) da subscrição. Por exemplo, se tiver uma conta organizacional no domínio Contoso, a forma do seu nome de registo completamente qualificado é `myregistry-contoso.azurecr.io`. 
  
  Para [controlar o acesso](container-registry-authentication.md) a um registo de contentores, utilize um [principal de serviço](../active-directory/active-directory-application-objects.md) baseado no Azure Active Directory ou uma conta de administrador fornecida. Execute o comando `docker login` padrão para se autenticar num registo. 

* **Repositório** - um registo contém um ou mais repositórios, que são grupos de imagens do contentor. O Registo de Contentores do Azure suporta espaços de nomes de repositórios com múltiplos níveis. Esta funcionalidade permite-lhe agrupar coleções de imagens relacionadas com uma aplicação específica ou uma coleção de aplicações para equipas de programação ou operacionais específicas. Por exemplo:
  
  * `myregistry-contoso.azurecr.io/aspnetcore:1.0.1` representa uma imagem transversal a toda a empresa
  * `myregistry-contoso.azurecr.io/warrantydept/dotnet-build` representa uma imagem utilizada para criar aplicações .NET, partilhadas em todo o departamento de garantias
  * `myregistry-contoso.azrecr.io/warrantydept/customersubmissions/web` representa uma imagem Web, agrupada na aplicação constomersubmissions, que é propriedade do departamento de garantias

* **Imagem** - armazenadas num repositório, as imagens são um instantâneo só de leitura de contentores do Docker. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório. 

* **Contentor** - um contentor define uma aplicação de software e as respetivas dependências, envoltas num sistema de ficheiros completo, que inclui código, tempo de execução, ferramentas do sistema e bibliotecas. Execute os contentores do Docker com base nas imagens do Windows ou do Linux que extrai de um registo de contentores. Os contentores executados num computador individual partilham o kernel do sistema operativo. Os contentores do Docker são totalmente portáteis para as principais distribuições de Linux e para Mac e Windows.

## <a name="use-cases"></a>Casos de utilização
Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações contentorizadas em clusters de anfitriões, incluindo [CD/SO](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) e [Kubernetes](http://kubernetes.io/docs/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Container Service](../container-service/index.md), o [Serviço de Aplicações](/app-service/index.md), o [Batch](../batch/index.md) e o [Service Fabric](../service-fabric/index.md). 

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. Por exemplo, podem segmentar um registo de contentores de uma ferramenta de integração contínua e programação, como o [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) ou o [Jenkins](https://jenkins.io/).





## <a name="next-steps"></a>Passos seguintes
* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Push your first image using the Docker CLI (Enviar a sua primeira imagem com a CLI do Docker)](container-registry-get-started-docker-cli.md)
* Para criar uma integração contínua e um fluxo de trabalho de implementação através dos Visual Studio Team Services, do Azure Container Service e do Azure Container Registry, veja [este tutorial](../container-service/container-service-setup-ci-cd.md).
* Se quiser configurar um registo privado do Docker no Azure (sem um ponto final público), veja [Deploying Your Own Private Docker Registry on Azure (Implementar o Seu Primeiro Registo Privado do Docker no Azure)](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md).



<!--HONumber=Nov16_HO4-->



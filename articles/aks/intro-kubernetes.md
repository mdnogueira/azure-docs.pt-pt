---
title: "Introdução ao Azure Container Service para Kubernetes | Microsoft Docs"
description: "O Azure Container Service para Kubernetes permite a implementação e gestão fácil de aplicações baseadas em contentores no Azure."
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, Containers, Microsserviços, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 9fba9fdda3503ec80fede845466858825e3677a5
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Introdução ao serviço de contentor do Azure (AKS)

Serviço de contentor do Azure (AKS) torna simples de criar, configurar e gerir um cluster de máquinas virtuais que estão pré-configuradas para executar aplicações de. Isto permite-lhe utilizar as suas competências ou basear-se num corpo extenso e crescente de conhecimentos da comunidade, para implementar e gerir aplicações baseadas em contentores do Microsoft Azure.

Ao utilizar AKS, pode tirar partido das funcionalidades de nível empresarial do Azure, mantendo ainda portabilidade de aplicações através de Kubernetes e o formato de imagem de Docker.

## <a name="managed-kubernetes-in-azure"></a>Gerido Kubernetes no Azure

AKS reduz o complexidade e a sobrecarga operacional de gerir um cluster de Kubernetes ao descarregar grande parte desse responsabilidade para o Azure. Como um serviço alojado do Kubernetes, tarefas de crítico de identificadores do Azure, como a monitorização de estado de funcionamento e manutenção para si. Além disso, paga apenas os nós de agente nos seus clusters, não para modelos de estrutura mestres. Como um serviço gerido Kubernetes, AKS fornece:

> [!div class="checklist"]
> * As atualizações de versão Kubernetes automatizada e aplicação de patches
> * O dimensionamento de clusters fácil
> * Autorrecuperação alojado plane de controlo (modelos de estrutura mestres)
> * Reduções de custos - pague apenas pelo que executa o agente de nós de conjunto

Com o Azure lidar com a gestão de nós do cluster AKS, já não terá de realizar muitas tarefas manualmente, como atualizações de cluster. Porque o Azure processa estas tarefas de manutenção crítica por si, AKS não fornece acesso direto (tal como com SSH) para o cluster.

## <a name="using-azure-container-service-aks"></a>Utilizar o serviço de contentor do Azure (AKS)
O objetivo de AKS consiste em fornecer um contentor de ambiente de alojamento utilizando ferramentas open source e tecnologias que estão atualmente populares entre os clientes. Para este fim, expomos os pontos finais da API do Kubernetes padrão. Ao utilizar estes pontos finais padrão, pode tirar partido de qualquer software que consiga comunicar com um cluster do Kubernetes. Por exemplo, pode escolher [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) ou [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Criar um cluster de Kubernetes utilizando o serviço de contentor do Azure (AKS)
Para começar a utilizar AKS, implementar um cluster AKS com o [CLI do Azure](./kubernetes-walkthrough.md) ou através do portal (Procurar no Marketplace para **serviço de contentor Azure**). Se for utilizador avançado e precisar de mais controlo sobre os modelos do Azure Resource Manager, pode utilizar o projeto [acs-engine](https://github.com/Azure/acs-engine) para criar o seu próprio cluster do Kubernetes personalizado e implementá-lo através da CLI `az`.

### <a name="using-kubernetes"></a>Utilizar Kubernetes
O Kubernetes automatiza a implementação, o dimensionamento e a gestão de aplicações no contentor. Tem um conjunto avançado de funcionalidades, incluindo:
* Empacotamento automático
* Autorrecuperação
* Dimensionamento horizontal
* Deteção do serviço e balanceamento de carga
* Implementações e reversões automáticas
* Gestão de segredos e de configurações
* Orquestração de armazenamento
* Execução de lotes

## <a name="videos"></a>Vídeos

O contentor do Azure Service (AKS) - Azure sexta-feira, Outubro de 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Ferramentas para desenvolver e implementar aplicações em Kubernetes - OpenDev do Azure, Junho de 2017:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Saiba mais sobre como implementar e gerir AKS com o guia de introdução AKS.

> [!div class="nextstepaction"]
> [Tutorial AKS](./kubernetes-walkthrough.md)
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
ms.date: 10/24/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: fa46d16e2105c0354cc533c58fb9e0093c129ced
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Introdução ao serviço de contentor do Azure (AKS)

Serviço de contentor do Azure (AKS) torna simples de criar, configurar e gerir um cluster de máquinas virtuais que estão pré-configuradas para executar aplicações de. Isto permite-lhe utilizar as suas competências ou basear-se num corpo extenso e crescente de conhecimentos da comunidade, para implementar e gerir aplicações baseadas em contentores do Microsoft Azure.

Ao utilizar AKS, pode tirar partido das funcionalidades de nível empresarial do Azure, mantendo ainda portabilidade de aplicações através de Kubernetes e o formato de imagem de Docker.

## <a name="using-azure-container-service-aks"></a>Utilizar o serviço de contentor do Azure (AKS)
O nosso objetivo com AKS consiste em fornecer um contentor de ambiente de alojamento utilizando ferramentas open source e tecnologias que estão atualmente populares entre os nossos clientes. Para este fim, expomos os pontos finais da API do Kubernetes padrão. Ao utilizar estes pontos finais padrão, pode tirar partido de qualquer software que consiga comunicar com um cluster do Kubernetes. Por exemplo, pode escolher [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) ou [draft](https://github.com/Azure/draft).

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

Suporte de Kubernetes no Azure Container Service (Azure Friday, janeiro de 2017):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Ferramentas para Programar e Implementar Aplicações no Kubernetes (Azure OpenDev, junho de 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Saiba mais sobre como implementar e gerir AKS com o guia de introdução AKS.

> [!div class="nextstepaction"]
> [Tutorial AKS](./kubernetes-walkthrough.md)
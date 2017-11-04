---
title: Monitorizar um cluster do Azure Kubernetes com CoScale | Microsoft Docs
description: "Monitor de um cluster de Kubernetes no serviço de contentor Azure utilizando CoScale"
services: container-service
documentationcenter: 
author: fryckbos
manager: 
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: ec73646f7b49669da82d9cda2a1348d447942619
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Monitor de um cluster de Kubernetes de serviço de contentor do Azure com CoScale

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Neste artigo, vamos mostrar-lhe como implementar o [CoScale](https://www.coscale.com/) agente para monitorizar todos os nós e contentores no seu cluster Kubernetes no serviço de contentor do Azure. Precisa de uma conta com CoScale para esta configuração. 


## <a name="about-coscale"></a>Sobre CoScale 

CoScale é uma plataforma de monitorização que recolhe métricas e eventos de todos os contentores em várias plataformas de orquestração. CoScale oferece monitorização completa pilha para ambientes de Kubernetes. Fornece visualizações e análise de todas as camadas na pilha: o SO, Kubernetes, Docker e aplicações em execução dentro os contentores. CoScale oferece várias dashboards de monitorização incorporadas e tem de deteção de anomalias incorporada para permitir que os operadores e programadores, para localizar problemas de infraestrutura e da aplicação rápida.

![CoScale da IU](./media/container-service-kubernetes-coscale/coscale.png)

Como é mostrado neste artigo, pode instalar agentes num cluster Kubernetes para executar CoScale como uma solução de SaaS. Se pretender manter os seus dados no local, CoScale também está disponível para instalação no local.


## <a name="prerequisites"></a>Pré-requisitos

Terá primeiro de [criar uma conta de CoScale](https://www.coscale.com/free-trial).

Esta instrução parte do princípio de que tem [criado um cluster de Kubernetes utilizando o serviço de contentor do Azure](container-service-kubernetes-walkthrough.md).

Também parte do princípio que tem o `az` CLI do Azure e `kubectl` as ferramentas instaladas.

Pode testar se tiver o `az` ferramenta instalada através da execução:

```azurecli
az --version
```

Se não tiver o `az` ferramenta instalada, existem instruções [aqui](/cli/azure/install-azure-cli).

Pode testar se tiver o `kubectl` ferramenta instalada através da execução:

```bash
kubectl version
```

Se não tiver `kubectl` instalado, pode executar:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalar o agente de CoScale com um DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) são utilizados pelo Kubernetes para executar uma única instância de um contentor em cada anfitrião no cluster.
Se estiver a perfeita para a execução de agentes de monitorização, tais como o agente de CoScale.

Depois de iniciar sessão CoScale, avance para o [página agente](https://app.coscale.com/) para instalar agentes de CoScale no seu cluster utilizando um DaemonSet. A IU de CoScale fornece passos de configuração orientada para criar um agente e iniciar a monitorização completa do seu cluster Kubernetes.

![Configuração do agente coScale](./media/container-service-kubernetes-coscale/installation.png)

Para iniciar o agente no cluster, execute o comando indicado:

![Iniciar o agente de CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

Já está! Assim que os agentes estão em execução, deve ver os dados na consola do dentro de alguns minutos. Visite o [página agente](https://app.coscale.com/) para ver um resumo do cluster, execute os passos de configuração adicional e ver dashboards, tais como o **Kubernetes descrição geral do cluster**.

![Descrição geral do cluster Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

O agente de CoScale é implementado automaticamente em novos computadores no cluster. As atualizações de agente automaticamente quando é lançada uma nova versão.


## <a name="next-steps"></a>Passos seguintes

Consulte o [CoScale documentação](http://docs.coscale.com/) e [blogue](https://www.coscale.com/blog) para obter informações sobre CoScale soluções de monitorização. 


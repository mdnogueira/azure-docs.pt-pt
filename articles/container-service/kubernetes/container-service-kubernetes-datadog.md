---
title: Monitor do Azure Kubernetes cluster com Datadog | Microsoft Docs
description: "Monitorização de Kubernetes cluster no serviço de contentor Azure utilizando Datadog"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: b2f88f663745f1caef856e9b2b5803000c284cb9
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Monitor de um cluster do serviço de contentor do Azure com DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Pré-requisitos
Esta instrução parte do princípio de que tem [criado um cluster de Kubernetes utilizando o serviço de contentor do Azure](container-service-kubernetes-walkthrough.md).

Também parte do princípio que tem o `az` cli do Azure e `kubectl` as ferramentas instaladas.

Pode testar se tiver o `az` ferramenta instalada através da execução:

```console
$ az --version
```

Se não tiver o `az` ferramenta instalada, existem instruções [aqui](https://github.com/azure/azure-cli#installation).

Pode testar se tiver o `kubectl` ferramenta instalada através da execução:

```console
$ kubectl version
```

Se não tiver `kubectl` instalado, pode executar:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog é um serviço de monitorização que recolhe dados de monitorização dos contentores num cluster do serviço de contentor do Azure. Datadog tem um Dashboard de integração do Docker onde pode ver métricas específicas dentro os contentores. Métricas recolhidas a partir dos contentores estão organizadas por da CPU, memória, rede e e/s. Datadog divide as métricas numa contentores e imagens.

Terá primeiro de [criar uma conta](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalar o agente de Datadog com um DaemonSet
DaemonSets são utilizados pelo Kubernetes para executar uma única instância de um contentor em cada anfitrião no cluster.
Se estiver a perfeita para a execução de agentes de monitorização.

Depois de ter sessão Datadog, pode seguir o [Datadog instruções](https://app.datadoghq.com/account/settings#agent/kubernetes) para instalar agentes de Datadog no seu cluster utilizando um DaemonSet.

## <a name="conclusion"></a>Conclusão
Já está! Assim que os agentes estão em execução, deverá ver dados na consola dentro de alguns minutos. Pode visitar o integrada [kubernetes dashboard](https://app.datadoghq.com/screen/integration/kubernetes) para ver um resumo do cluster.

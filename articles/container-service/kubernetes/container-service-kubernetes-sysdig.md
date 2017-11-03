---
title: Cluster de Azure Kubernetes monitor - Sysdig | Microsoft Docs
description: "Monitorização de Kubernetes cluster no serviço de contentor Azure utilizando Sysdig"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 283cae7031fd275fc1f355e25f01206d143756fe
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>Monitorizar um cluster de Kubernetes de serviço de contentor Azure utilizando Sysdig

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Pré-requisitos
Esta instrução parte do princípio de que tem [criado um cluster de Kubernetes utilizando o serviço de contentor do Azure](container-service-kubernetes-walkthrough.md).

Também parte do princípio que tem as ferramentas do azure cli e kubectl, instaladas.

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

## <a name="sysdig"></a>Sysdig
Sysdig é um externo monitorização como uma empresa de serviço que pode monitorizar contentores no seu cluster Kubernetes em execução no Azure. Utilizar Sysdig requer uma conta ativa do Sysdig.
Pode inscrever para uma conta respetivo [site](https://app.sysdigcloud.com).

Após ter iniciado sessão no site na nuvem do Sysdig, clique no seu nome de utilizador e deverá ver a sua "Chave de Acesso" na página. 

![Chave de API do Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalar os agentes de Sysdig Kubernetes
Para monitorizar os contentores, Sysdig executa um processo em cada máquina utilizando um Kubernetes `DaemonSet`.
DaemonSets são objetos de Kubernetes API que executem uma única instância de um contentor por máquina.
Se estiver a perfeita para instalar as ferramentas como o agente de monitorização do Sysdig.

Para instalar o daemonset Sysdig, deve transferir [o modelo](https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml) de sysdig. Guarde este ficheiro como `sysdig-daemonset.yaml`.

No Linux e OS X, pode executar:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

No PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Em seguida, edite nesse ficheiro para inserir a sua chave de acesso, que obteve na sua conta Sysdig.

Por fim, crie o DaemonSet:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Ver a monitorização
Depois de instalado e em execução, os agentes devem pump dados Sysdig.  Volte à [sysdig dashboard](https://app.sysdigcloud.com) e deverá ver informações sobre os contentores.

Também pode instalar dashboards Kubernetes específicos através da [Assistente de novo dashboard](https://app.sysdigcloud.com/#/dashboards/new).

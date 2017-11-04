---
title: "Tutorial de serviço de contentor do Azure – Monitor Kubernetes | Microsoft Docs"
description: "Tutorial de serviço de contentor do Azure – Kubernetes Monitor com o Microsoft Operations Management Suite (OMS)"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, contentores, microserviços, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f72944fe819a79edbafb73fba635d73642f33e4f
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Monitor de um cluster de Kubernetes com o Operations Management Suite

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

A monitorização do seu Kubernetes cluster e contentores é crítica, especialmente quando gerir um cluster de produção em escala com várias aplicações. 

Pode tirar partido das várias Kubernetes soluções de monitorização, a partir do Microsoft ou de outros fornecedores de serviços. Neste tutorial, pode monitorizar o seu cluster Kubernetes ao utilizar a solução de contentores no [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md), solução de gestão de IT baseado na nuvem da Microsoft. (A solução de contentores do OMS está em pré-visualização.)

Neste tutorial, parte sete sete, abrange as seguintes tarefas:

> [!div class="checklist"]
> * Obter definições da área de trabalho do OMS
> * Configurar os agentes do OMS em nós Kubernetes
> * Aceder a informações de monitorização no portal do OMS ou do portal do Azure

## <a name="before-you-begin"></a>Antes de começar

Tutoriais anteriores, uma aplicação foi compactada imagens de contentor, estas imagens carregadas para o registo de contentor do Azure e um cluster de Kubernetes criada. 

Se não o fez estes passos e gostaria de acompanhar, regresse ao [Tutorial 1 – criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="get-workspace-settings"></a>Obter definições da área de trabalho

Quando lhe pode aceder a [portal do OMS](https://mms.microsoft.com), aceda a **definições** > **origens ligadas** > **servidores Linux**. Aqui, pode encontrar o *ID da área de trabalho* e um site primário ou secundário *chave da área de trabalho*. Tome nota destes valores, o que precisa de configurar os agentes do OMS no cluster.

## <a name="set-up-oms-agents"></a>Configurar os agentes do OMS

Segue-se um ficheiro YAML para configurar os agentes do OMS em nós de cluster do Linux. Cria um Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), que é executada uma única pod idêntico em cada nó de cluster. O recurso de DaemonSet é ideal para implementar um agente de monitorização. 

Guarde o seguinte texto num ficheiro denominado `oms-daemonset.yaml`e substitua os valores de marcador de posição para *myWorkspaceID* e *myWorkspaceKey* com o ID da área de trabalho OMS e a chave. (Na produção, pode de codificar estes valores como segredos.)

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
```

Crie o DaemonSet com o seguinte comando:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Para ver o que é criado o DaemonSet, execute:

```azurecli-interactive
kubectl get daemonset
```

O resultado é semelhante ao seguinte:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Após os agentes estiverem em execução, que demora alguns minutos para OMS para a ingestão e processar os dados.

## <a name="access-monitoring-data"></a>Acesso a dados de monitorização

Ver e analisar o dados com de monitorização do contentor OMS o [solução contentor](../../log-analytics/log-analytics-containers.md) no portal do OMS ou no portal do Azure. 

Para instalar a solução de contentor utilizando o [portal do OMS](https://mms.microsoft.com), aceda a **soluções galeria**. Em seguida, adicione **contentor solução**. Em alternativa, adicione a solução de contentores do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

No portal do OMS, procure um **contentores** mosaico resumo no dashboard do OMS. Clique no mosaico para obter detalhes, incluindo: eventos de contentor, erros, estado, inventário de imagem e a utilização da CPU e memória. Para informações mais detalhadas, clique nos mesmos qualquer mosaico ou efetuar uma [pesquisa registo](../../log-analytics/log-analytics-log-searches.md).

![Dashboard de contentores no portal do OMS](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Da mesma forma, no portal do Azure, aceda a **Log Analytics** e selecione o nome da sua área de trabalho. Para ver o **contentores** mosaico resumo, clique em **soluções** > **contentores**. Para ver detalhes, clique no mosaico.

Consulte o [documentação Log Analytics do Azure](../../log-analytics/index.yml) para orientações detalhadas sobre a consultar e analisar dados de monitorização.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, monitorizados o cluster de Kubernetes com o OMS. Tarefas abrangidos incluídos:

> [!div class="checklist"]
> * Obter definições da área de trabalho do OMS
> * Configurar os agentes do OMS em nós Kubernetes
> * Aceder a informações de monitorização no portal do OMS ou do portal do Azure


Siga esta ligação para ver script pré-criadas amostras do serviço de contentor.

> [!div class="nextstepaction"]
> [Exemplos de script do serviço de contentor do Azure](cli-samples.md)

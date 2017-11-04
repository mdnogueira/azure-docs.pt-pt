---
title: "Kubernetes no tutorial do Azure – Kubernetes de Monitor"
description: Tutorial AKS - Kubernetes Monitor com o Microsoft Operations Management Suite (OMS)
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, contentores, microserviços, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a41f699291a65129906680cbb6719c2478c0d830
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="monitor-azure-container-service-aks"></a>Serviço de contentor do Azure do monitor (AKS)

O Kubernetes cluster e contentores de monitorização é essencial, especialmente quando executar um cluster de produção em escala, com várias aplicações.

Neste tutorial, configurar a monitorização do seu cluster AKS utilizando o [solução de contentores para análise de registos](../log-analytics/log-analytics-containers.md).

Neste tutorial, parte sete oito, abrange as seguintes tarefas:

> [!div class="checklist"]
> * Configurar o solução de monitorização do contentor
> * Configurar os agentes de monitorização
> * Aceder a informações de monitorização no portal do Azure

## <a name="before-you-begin"></a>Antes de começar

Tutoriais anteriores, uma aplicação foi compactada imagens de contentor, estas imagens carregadas para o registo de contentor do Azure e um cluster de Kubernetes criada.

Se não o fez estes passos e gostaria de acompanhar, regresse ao [Tutorial 1 – criar imagens de contentor](./tutorial-kubernetes-prepare-app.md).

## <a name="configure-the-monitoring-solution"></a>Configurar a solução de monitorização

No portal do Azure, selecione **novo** e procure `Container Monitoring Solution`. Depois de localizar, selecionar **criar**.

![Adicionar solução](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Criar uma área de trabalho do OMS novo ou selecione um existente. A forma da área de trabalho OMS orienta-o ao longo deste processo.

Ao criar a área de trabalho, selecione **afixar ao dashboard** para obtenção fácil.

![Área de trabalho do OMS](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Quando terminar, selecione **OK**. Depois de concluída a validação, selecione **criar** para criar o solução de monitorização do contentor.

Quando tiver sido criada a área de trabalho, é apresentada ao utilizador no portal do Azure.

## <a name="get-workspace-settings"></a>Obter definições da área de trabalho

A análise de registos ID da área de trabalho e a chave é necessária para configurar o agente de solução em nós Kubernetes.

Para obter estes valores, selecione **área de trabalho OMS** do contentor soluções menu da esquerda. Selecione **definições avançadas** e tome nota do **ID da área de trabalho** e **chave primária**.

## <a name="configure-monitoring-agents"></a>Configurar os agentes de monitorização

O ficheiro de manifesto Kubernetes seguinte pode ser utilizado para configurar o agentes num Kubernetes cluster de monitorização do contentor. Cria um Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), que é executada uma única pod em cada nó de cluster.

Guarde o seguinte texto num ficheiro denominado `oms-daemonset.yaml`e substitua os valores de marcador de posição para `WSID` e `KEY` com o ID de área de trabalho de análise do registo e a chave.

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
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
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: container-hostname
      hostPath:
       path: /etc/hostname
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

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Após os agentes estiverem em execução, que demora alguns minutos para OMS para a ingestão e processar os dados.

## <a name="access-monitoring-data"></a>Acesso a dados de monitorização

No portal do Azure, selecione a área de trabalho de análise de registos foi afixada ao dashboard do portal. Clique em de **solução de monitorização do contentor** mosaico. Aqui pode encontrar informações sobre o cluster AKS e contentores do cluster.

![Dashboard](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Consulte o [documentação Log Analytics do Azure](../log-analytics/index.yml) para orientações detalhadas sobre a consultar e analisar dados de monitorização.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, monitorizados o cluster de Kubernetes com o OMS. Tarefas abrangidos incluídos:

> [!div class="checklist"]
> * Configurar o solução de monitorização do contentor
> * Configurar os agentes de monitorização
> * Aceder a informações de monitorização no portal do Azure

Avançar para o próximo tutorial para saber mais sobre a atualização Kubernetes para uma nova versão.

> [!div class="nextstepaction"]
> [Atualizar Kubernetes](./tutorial-kubernetes-upgrade-cluster.md)
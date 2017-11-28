---
title: "Cluster de Azure Kubernetes monitor - operações de gestão | Microsoft Docs"
description: "Monitorização de Kubernetes cluster no serviço de contentor Azure utilizando o Microsoft Operations Management Suite"
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
ms.openlocfilehash: c402d81841ed5a79df8a84d1f69f984d010dbe0d
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Monitor de um cluster do serviço de contentor do Azure com o Microsoft Operations Management Suite (OMS)

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Pré-requisitos
Esta instrução parte do princípio de que tem [criado um cluster de Kubernetes utilizando o serviço de contentor do Azure](container-service-kubernetes-walkthrough.md).

Também parte do princípio que tem o `az` cli do Azure e `kubectl` as ferramentas instaladas.

Pode testar se tiver o `az` ferramenta instalada através da execução:

```console
$ az --version
```

Se não tiver o `az` ferramenta instalada, existem instruções [aqui](https://github.com/azure/azure-cli#installation).  
Em alternativa, pode utilizar [Shell de nuvem do Azure](https://docs.microsoft.com/azure/cloud-shell/overview), que tem o `az` cli do Azure e `kubectl` ferramentas já instaladas por si.  

Pode testar se tiver o `kubectl` ferramenta instalada através da execução:

```console
$ kubectl version
```

Se não tiver `kubectl` instalado, pode executar:
```console
$ az acs kubernetes install-cli
```

Para testar se tiver chaves kubernetes instaladas na sua ferramenta kubectl que pode executar:
```console
$ kubectl get nodes
```

Se os erros de comando acima terminar, tem de instalar as chaves de cluster kubernetes para a ferramenta de kubectl. Pode fazê-lo com o seguinte comando:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Contentores de monitorização no Operations Management Suite (OMS)

Gestão de operações do Microsoft (OMS) é baseado na nuvem IT solução de gestão que o ajuda a gerir e proteger no local e a infraestrutura de nuvem. da Microsoft Contentor é uma solução na análise de registos do OMS, que ajuda-o a ver o inventário de contentor, o desempenho e a registos numa única localização. Pode de auditoria, resolver problemas de contentores através da visualização de registos de localização centralizada e localizar inúteis consumir contentor em excesso num anfitrião.

![](media/container-service-monitoring-oms/image1.png)

Para obter mais informações sobre a solução de contentor, consulte o [análise de registos do contentor solução](../../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Instalar OMS Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Obter o ID da área de trabalho e a chave
Para o OMS agente para comunicar com o serviço, tem de ser configurado com um id de área de trabalho e uma chave de área de trabalho. Para obter o id da área de trabalho e a chave tem de criar uma conta do OMS no <https://mms.microsoft.com>. Siga os passos para criar uma conta. Quando tiver terminado a criar a conta, tem de obter o id e a chave clicando **definições**, em seguida, **origens ligadas**e, em seguida, **servidores Linux**, conforme mostrado abaixo.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Instalar o agente do OMS utilizando um DaemonSet
DaemonSets são utilizados pelo Kubernetes para executar uma única instância de um contentor em cada anfitrião no cluster.
Se estiver a perfeita para a execução de agentes de monitorização.

Eis o [ficheiro DaemonSet YAML](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Guarde-o num ficheiro denominado `oms-daemonset.yaml` e substitua os valores de marcador de posição-para `WSID` e `KEY` com o id da área de trabalho e a chave no ficheiro.

Depois de adicionar o ID da área de trabalho e a chave para a configuração de DaemonSet, pode instalar o agente do OMS no seu cluster com o `kubectl` ferramenta de linha de comandos:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-oms-agent-using-a-kubernetes-secret"></a>Instalar o agente do OMS utilizando um segredo Kubernetes
Para proteger o seu ID da área de trabalho OMS e da chave pode utilizar Kubernetes segredo como parte do ficheiro de DaemonSet YAML.

 - Copie o script, ficheiro de modelo secreta e o ficheiro DaemonSet YAML (de [repositório](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) e certifique-se de que estão no mesmo diretório. 
      - segredo gerar script - gen.sh segredo
      - modelo secreto - template.yaml segredo
   - Ficheiro DaemonSet YAML - omsagent-ds-secrets.yaml
 - Execute o script. O script irá perguntar-para o ID da área de trabalho OMS e a chave primária. Insira o que e o script irá criar um ficheiro de yaml secreta, pelo que pode executá-lo.   
   ```
   #> sudo bash ./secret-gen.sh 
   ```

   - Crie o pod segredos executando o seguinte:``` kubectl create -f omsagentsecret.yaml ```
 
   - Para verificar, execute o seguinte: 

   ``` 
   root@ubuntu16-13db:~# kubectl get secrets
   NAME                  TYPE                                  DATA      AGE
   default-token-gvl91   kubernetes.io/service-account-token   3         50d
   omsagent-secret       Opaque                                2         1d
   root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
   Name:           omsagent-secret
   Namespace:      default
   Labels:         <none>
   Annotations:    <none>

   Type:   Opaque

   Data
   ====
   WSID:   36 bytes
   KEY:    88 bytes 
   ```
 
  - Criar a sua omsagent daemon-set executando``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Conclusão
Já está! Após alguns minutos, deverá conseguir ver os dados enviados para o dashboard do OMS.

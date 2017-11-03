---
title: Gerir o cluster Azure Kubernetes com IU da web | Microsoft Docs
description: "Utilizando a IU da web de Kubernetes no serviço de contentor do Azure"
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
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 15d423bff629650e205f70c61c592da91339c834
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Utilizando a IU da web de Kubernetes com o serviço de contentor do Azure

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Pré-requisitos
Esta instrução parte do princípio de que tem [criado um cluster de Kubernetes utilizando o serviço de contentor do Azure](container-service-kubernetes-walkthrough.md).


Também parte do princípio que tem o 2.0 CLI do Azure e `kubectl` as ferramentas instaladas.

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

## <a name="overview"></a>Descrição geral

### <a name="connect-to-the-web-ui"></a>Estabelecer ligação com a IU da web
Pode iniciar a IU da web de Kubernetes executando:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Isto deve abrir um browser configurado para comunicar com um proxy seguro ligar o seu computador local para a IU da web de Kubernetes.

### <a name="create-and-expose-a-service"></a>Criar e expor um serviço
1. Na Kubernetes IU da web, clique em **criar** botão na janela superior direito.

    ![Kubernetes criar a IU](./media/container-service-kubernetes-ui/create.png)

    Abre de caixa de diálogo onde podem iniciar a criação da sua aplicação.

2. Atribua o nome `hello-nginx`. Utilize o [ `nginx` contentor do Docker](https://hub.docker.com/_/nginx/) e implementar três réplicas deste serviço web.

    ![Caixa de diálogo de criar Kubernetes Pod](./media/container-service-kubernetes-ui/nginx.png)

3. Em **serviço**, selecione **externo** e introduza a porta 80.

    Esta definição carga balanceia tráfego para as três réplicas.

    ![Caixa de diálogo Criar do serviço de Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Clique em **implementar** para implementar estes serviços e contentores.

    ![Implementar Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Ver os contentores
Depois de clicar em **implementar**, a IU mostra uma vista do seu serviço, como implementa:

![Estado de Kubernetes](./media/container-service-kubernetes-ui/status.png)

Pode ver o estado de cada objeto Kubernetes no círculo no lado esquerdo da IU, em **Pods**. Se for um círculo parcialmente completo, em seguida, o objeto ainda está em implementação. Quando um objeto é totalmente implementado, apresenta uma marca de verificação verde:

![Kubernetes implementado](./media/container-service-kubernetes-ui/deployed.png)

Assim que tudo está a ser executado, clique das suas pods para ver detalhes sobre o serviço web em execução.

![Kubernetes Pods](./media/container-service-kubernetes-ui/pods.png)

No **Pods** vista, pode ver informações sobre os contentores no pod, bem como os recursos de CPU e memória utilizados desses contentores:

![Kubernetes recursos](./media/container-service-kubernetes-ui/resources.png)

Se não vir os recursos, poderá ter de aguardar alguns minutos para que os dados de monitorização propagar.

Para ver os registos para o contentor, clique em **ver registos**.

![Registos de Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Visualizar o seu serviço
Além de executar os contentores, a IU de Kubernetes criou um externo `Service` que aprovisiona um balanceador de carga para colocar o tráfego para os contentores do cluster.

No painel de navegação esquerdo, clique em **serviços** para ver todos os serviços (deve haver apenas um).

![Serviços de Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

Nessa vista, deverá ver um ponto final externo (endereço IP) que foi atribuído ao seu serviço.
Se clicar nesse endereço IP, deverá ver o contentor Nginx em execução por detrás do Balanceador de carga.

![Vista de nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Redimensionar o seu serviço
Além de visualizar os objetos na IU, pode editar e atualizar os objetos de Kubernetes API.

Em primeiro lugar, clique em **implementações** no painel de navegação à esquerda para ver a implementação para o seu serviço.

Assim que estiver nessa vista, clique no conjunto de réplicas e, em seguida, clique em **editar** na barra de navegação superior:

![Editar Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Editar o `spec.replicas` campo ser `2`e clique em **atualização**.

Isto faz com que o número de réplicas para remover a dois, eliminando um dos seus pods.

 


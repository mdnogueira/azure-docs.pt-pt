---
title: Gerir o cluster Azure Kubernetes com IU da web | Microsoft Docs
description: Utilizar o dashboard de Kubernetes no AKS
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 764bbb95b661bb750d7802ee5996d8a491be928d
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Dashboard de Kubernetes com o serviço de contentor do Azure (AKS)

A CLI do Azure pode ser utilizada para iniciar o Kubernetes Dashboard. Este documento explica a partir do dashboard Kubernetes com a CLI do Azure e também explica algumas operações básicas do dashboard. Para obter mais informações, consulte o Kubernetes dashboard, [Kubernetes Web IU Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste documento partem do princípio de que já criou um cluster do AKS e estabeleceu uma ligação de kubectl com o cluster. Se precisar destes itens, veja [Início rápido do AKS](./kubernetes-walkthrough.md).

Precisa também da versão 2.0.20 da CLI do Azure ou posterior instalada e configurada. Execute az --version para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="start-kubernetes-dashboard"></a>Iniciar Kubernetes dashboard

Utilize o `az aks browse` comando para iniciar o dashboard Kubernetes. Quando executar este comando, substitua o nome de grupo e o cluster de recursos.

```azurecli
az aks browse --resource-group myResourceGroup --name myK8SCluster
```

Este comando cria um proxy entre o sistema de desenvolvimento e a API de Kubernetes e abre um browser para o dashboard Kubernetes.

## <a name="run-an-application"></a>Executar uma aplicação

No dashboard do Kubernetes, clique em de **criar** botão na janela superior direito. Atribua o nome de implementação `nginx` e introduza `nginx:latest` para o nome de imagens. Em **serviço**, selecione **externo** e introduza `80` para a porta e a porta de destino.

Quando estiver pronto, clique em **implementar** para criar a implementação.

![Caixa de diálogo Criar do serviço de Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Ver a aplicação

Estado acerca da aplicação pode ser visto no dashboard do Kubernetes. Assim que a aplicação é executada, cada componente tem uma caixa de verificação verde junto ao mesmo.

![Kubernetes Pods](./media/container-service-kubernetes-ui/complete-deployment.png)

Para obter mais informações sobre os pods de aplicação, clique em **Pods** no menu da esquerda e, em seguida, selecione o **NGINX** pod. Aqui pode ver informações de pod específicas, tais como o consumo de recursos.

![Kubernetes recursos](./media/container-service-kubernetes-ui/running-pods.png)

Para localizar o endereço IP da aplicação, clique em **serviços** no menu da esquerda e, em seguida, selecione o **NGINX** serviço.

![Vista de nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Editar a aplicação

Além de criar e aplicações de visualização, o dashboard Kubernetes pode ser utilizado para edite e Atualize as implementações de aplicações.

Para editar uma implementação, clique em **implementações** no menu da esquerda e, em seguida, selecione o **NGINX** implementação. Por fim, selecione **editar** na barra de navegação direito superior.

![Editar Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Localize o `spec.replica` valor, o que deve ser 1, altere este valor para 3. Ao fazê-lo, a contagem de réplicas da implementação NGINX aumenta de 1 a 3.

Selecione **atualização** quando está preparado.

![Editar Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o dashboard Kubernetes, consulte a documentação de Kubernetes.

> [!div class="nextstepaction"]
> [Dashboard de IU da Web de Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)
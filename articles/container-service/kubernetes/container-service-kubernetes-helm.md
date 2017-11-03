---
title: Implementar contentores com Helm no Azure Kubernetes | Microsoft Docs
description: "Utilize a ferramenta de empacotamento de Helm para implementar contentores num cluster Kubernetes no serviço de contentor do Azure"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 9dd137de99864fbac2c73400a88956f6afa324db
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>Utilizar Helm para implementar contentores num Kubernetes cluster

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Helm](https://github.com/kubernetes/helm/) é uma ferramenta de empacotamento de open source que o ajuda a instalar e gerir o ciclo de vida das aplicações Kubernetes. Semelhante para os gestores de pacote do Linux, tais como Apt get e Yum, Helm é utilizado para gerir Kubernetes gráficos, que são pacotes de recursos de Kubernetes pré-configurada. Este artigo mostra como trabalhar com Helm num cluster Kubernetes implementado no serviço de contentor do Azure.

Helm tem dois componentes: 
* O **Helm CLI** for um cliente que é executado no seu computador local ou na nuvem  

* **Tiller** é um servidor que executa no Kubernetes cluster e gere o ciclo de vida das aplicações Kubernetes 
 
## <a name="prerequisites"></a>Pré-requisitos

* [Criar um cluster de Kubernetes](container-service-kubernetes-walkthrough.md) no serviço de contentor do Azure

* [Instalar e configurar `kubectl` ](../container-service-connect.md) num computador local

* [Instalar Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) num computador local

## <a name="helm-basics"></a>Noções básicas de Helm 

Para ver informações sobre o cluster Kubernetes que está a instalar Tiller e implementar as aplicações, escreva o seguinte comando:

```bash
kubectl cluster-info 
```
![informações do cluster kubectl](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Depois de ter instalado Helm, instale Tiller no seu cluster Kubernetes, escrevendo o seguinte comando:

```bash
helm init --upgrade
```
Quando for concluída com êxito, poderá ver um resultado como o seguinte:

![Instalação de tiller](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Para ver os gráficos de Helm disponíveis no repositório, escreva o seguinte comando:

```bash 
helm search 
```

Consulte o resultado como o seguinte:

![Pesquisa de Helm](./media/container-service-kubernetes-helm/helm-search.png)
 
Para atualizar os gráficos para obter as versões mais recentes, escreva:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Implementar um gráfico de controlador de entrada Nginx 
 
Para implementar um gráfico de controlador de entrada Nginx, escreva um comando único:

```bash
helm install stable/nginx-ingress 
```
![Implementar controlador de entrada](./media/container-service-kubernetes-helm/nginx-ingress.png)

Se escrever `kubectl get svc` para ver todos os serviços que estão em execução no cluster, pode ver que está atribuído um endereço IP para o controlador de entrada. (Durante a atribuição, consulte `<pending>`. Demora alguns minutos a concluir.) 

Depois do IP está atribuído um endereço, navegue para o valor do endereço IP externo para ver o back-end de Nginx em execução. 
 
![Endereço IP de entrada](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Para ver uma lista dos gráficos instalado no seu cluster, escreva:

```bash
helm list 
```

Pode abbreviate o comando para `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Implementar um gráfico de MariaDB e cliente

Agora pode implemente um gráfico de MariaDB e um cliente MariaDB para ligar à base de dados.

Para implementar o gráfico de MariaDB, escreva o seguinte comando:

```bash
helm install --name v1 stable/mariadb
```

onde `--name` é uma etiqueta utilizada para versões.

> [!TIP]
> Se a implementação falhar, execute `helm repo update` e tente novamente.
>
 
 
Para ver todos os gráficos implementados no seu cluster, escreva:

```bash 
helm list
```
 
Para ver todas as implementações em execução no seu cluster, escreva:

```bash
kubectl get deployments 
``` 
 
 
Por fim, para executar um pod para acesso de cliente, escreva:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Para ligar ao cliente, escreva o seguinte comando, substituindo `v1-mariadb` com o nome da sua implementação:

```bash
sudo mysql –h v1-mariadb
```
 
 
Agora, pode utilizar comandos do SQL Server standard para criar bases de dados, tabelas, etc. Por exemplo, `Create DATABASE testdb1;` cria uma base de dados vazio. 
 
 
 
## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a gestão Kubernetes gráficos, consulte o [Helm documentação](https://github.com/kubernetes/helm/blob/master/docs/index.md). 


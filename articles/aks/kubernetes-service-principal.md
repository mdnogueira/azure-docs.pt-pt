---
title: "Principal de serviço do cluster de Kubernetes do Azure | Microsoft Docs"
description: "Criar e gerir um principal de serviço do Azure Active Directory para um cluster de Kubernetes no AKS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a532c8f69bfb19d26538aafe7c74f062dee06d9f
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Principais de serviço com o Azure Container Service (AKS)

Um cluster do AKS requer um [principal de serviço do Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) para interagir com APIs do Azure. O principal de serviço tem de gerir dinamicamente recursos, como [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) e o [Balanceador de Carga do Azure de Camada 4](../load-balancer/load-balancer-overview.md).

Este artigo mostra as diferentes opções para configurar um principal de serviço para o cluster de Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste documento partem do princípio de que já criou um cluster do AKS e estabeleceu uma ligação de kubectl com o cluster. Se precisar destes itens, veja [Início rápido do AKS](./kubernetes-walkthrough.md).

Para criar um principal de serviço do Azure AD, tem de ter permissões para registar uma aplicação no seu inquilino do Azure AD e para atribuir a aplicação a uma função na sua subscrição. Se não tiver as permissões necessárias, poderá ter de pedir ao administrador do Azure AD ou da subscrição para atribuir as permissões necessárias ou pré-criar um principal de serviço para o cluster de Kubernetes.

Precisa também da versão 2.0.20 da CLI do Azure ou posterior instalada e configurada. Execute az --version para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-sp-with-aks-cluster"></a>Criar o SP com o cluster do AKS

Quando implementar um cluster do AKS com o comando `az aks create`, tem a opção de gerar automaticamente um principal de serviço.

No exemplo seguinte, é criado um cluster do AKS e, por não existir um principal de serviço especificado, é criado um principal de serviço para o cluster. Para concluir esta operação, a sua conta tem de ter os direitos adequados para criar um principal de serviço. 

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Utilizar um SP existente

Pode ser utilizado ou pré-criado um principal de serviço existente do Azure AD para utilização num cluster do AKS. Isto é útil ao implementar um cluster a partir do portal do Azure, onde tem de fornecer as informações do principal de serviço.

Quando utilizar um principal de serviço existente, tem de cumprir os seguintes requisitos:

- Âmbito: a subscrição utilizada para implementar o cluster
- Função: Contribuidor
- Segredo do cliente: tem de ser uma palavra-passe

## <a name="pre-create-a-new-sp"></a>Pré-criar um novo SP

Para criar o principal de serviço com a CLI do Azure, utilize o comando [az ad sp create-for-rbac]().

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

O resultado é semelhante ao seguinte. Tome nota do `appId` e da `password`. Estes valores são utilizados quando criar um cluster do AKS.

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Utilizar um SP existente

Quando utilizar um principal de serviço pré-criado, forneça o `appId` e a `password` como valores de argumento para o comando `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-princal <appId> ----client-secret <password>
```

Se estiver a implementar um cluster do AKS a partir do portal do Azure, introduza estes valores no formulário de configuração de clusters do AKS.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Considerações adicionais

Quando utilizar principais de serviço do AKS e do Azure AD, tenha em atenção o seguinte.

* O principal de serviço para Kubernetes faz parte da configuração do cluster. No entanto, não utilize a identidade para implementar o cluster.
* Cada principal de serviço está associado a uma aplicação do Azure AD. O principal de serviço para um cluster de Kubernetes pode ser associado a qualquer nome de aplicação do Azure AD válido (por exemplo: `https://www.contoso.org/example`). O URL para a aplicação não tem de ser um ponto final real.
* Quando especificar o principal de serviço **ID de Cliente**, pode utilizar o valor do `appId` (como mostrado neste artigo) ou o principal de serviço correspondente `name` (por exemplo, `https://www.contoso.org/example`).
* Em VMs do nó e mestras no cluster de Kubernetes, as credenciais do principal de serviço são armazenadas no ficheiro /etc/kubernetes/azure.json.
* Quando utilizar o comando `az aks create` para gerar automaticamente o principal de serviço, as credenciais do principal de serviço são escritas no ficheiro ~/.azure/acsServicePrincipal.json na máquina utilizada para executar o comando.
* Quando utiliza o comando `az aks create` para gerar automaticamente o principal de serviço, o principal de serviço também pode autenticar com um [Azure container registry](../container-registry/container-registry-intro.md) criado na mesma subscrição.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre principais de serviço do Azure Active Directory, veja a documentação de aplicações do Azure AD.

> [!div class="nextstepaction"]
> [Objetos de aplicação e de principal de serviço](../active-directory/develop/active-directory-application-objects.md)

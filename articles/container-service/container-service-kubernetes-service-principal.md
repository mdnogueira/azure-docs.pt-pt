---
title: "Principal de serviço do cluster de Kubernetes do Azure | Microsoft Docs"
description: "Criar e gerir um principal de serviço do Azure Active Directory num cluster do Azure Container Service com Kubernetes"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: b76020e3e5855a63c416851d9b9adefdbdc5874a
ms.contentlocale: pt-pt
ms.lasthandoff: 05/03/2017


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Sobre o principal de serviço do Azure Active Directory para um cluster de Kubernetes no Azure Container Service



No Azure Container Service, o Kubernetes requer um [principal de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) como uma conta de serviço para interagir com APIs do Azure. O principal de serviço tem de gerir dinamicamente recursos, como [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) e o [Balanceador de Carga do Azure de Camada 4](../load-balancer/load-balancer-overview.md).

Este artigo mostra as diferentes opções para especificar um principal de serviço para o cluster de Kubernetes. Por exemplo, se instalou e configurou o [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), pode executar o comando [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create), para criar o cluster de Kubernetes e o principal de serviço ao mesmo tempo.



## <a name="requirements-for-the-service-principal"></a>Requisitos para o principal de serviço

Seguem os requisitos para o principal de serviço do Azure Active Directory para um cluster de Kubernetes no Azure Container Service. 

* **Âmbito**: o grupo de recursos no qual o cluster é implementado

* **Função**: **Contribuinte**

* **Segredo do cliente**: tem de ser uma palavra-passe. Atualmente, não pode utilizar um principal de serviço configurado para autenticação de certificados.

> [!NOTE]
> Cada principal de serviço está associado a uma aplicação do Azure Active Directory. O principal de serviço para um cluster de Kubernetes pode ser associado a qualquer nome de aplicação do Azure Active Directory válido.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Opções de principais do serviço para um cluster de Kubernetes

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>Opção 1: passar o segredo de cliente e o ID de cliente principal de serviço

Forneça o **ID de cliente** (também designado pelo `appId`, para o ID da Aplicação) e **segredo do cliente** (`password`) de um principal de serviço existente como parâmetros ao criar o cluster de Kubernetes. Se estiver a utilizar um principal de serviço existente, certifique-se de que cumpre os requisitos na secção anterior. Se precisar de criar um principal de serviço, consulte [Create a service principal (Criar um principal de serviço)](#create-a-service-principal-in-azure-active-directory) posteriormente neste artigo.

Pode especificar estes parâmetros quando [implementar o cluster de Kubernetes](./container-service-deployment.md) com o portal, a Interface de Linha de Comandos (CLI) do Azure 2.0, o Azure PowerShell ou outros métodos.

>[!TIP] 
>Quando especificar a **ID de cliente**, certifique-se de que utiliza o `appId`, e não o `ObjectId`, do principal de serviço.
>

O exemplo seguinte mostra uma forma para passar os parâmetros com a CLI do Azure 2.0 (consulte[installation and setup instructions (instruções de instalação e configuração)](/cli/azure/install-az-cli2)). Este exemplo utiliza o [modelo de início rápido de Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Transfira](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) o ficheiro de parâmetros do modelo `azuredeploy.parameters.json` a partir do GitHub.

2. Para especificar o principal de serviço, introduza os valores para `servicePrincipalClientId` e `servicePrincipalClientSecret` no ficheiro. (Também tem de fornecer os seus próprios valores para `dnsNamePrefix` e `sshRSAPublicKey`. Este último é a chave pública SSH para aceder ao cluster.) Guarde o ficheiro.

    ![Passar parâmetros do principal de serviço](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Execute o seguinte comando com o `--parameters`, para definir o caminho para o ficheiro azuredeploy.parameters.json. Este comando implementa o cluster num grupo de recursos que cria denominado `myResourceGroup`, na região E.U.A. Oeste.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus" 
    
    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20"></a>Opção 2: gerar o principal de serviço ao criar o cluster com o CLI do Azure 2.0

Se instalou e configurou o [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), pode executar o comando [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) para [criar o cluster](./container-service-create-acs-cluster-cli.md).

Tal como com outras opções de criação de cluster de Kubernetes, pode especificar parâmetros para um principal de serviço existente quando executa `az acs create`. No entanto, quando omitir estes parâmetros, o Azure Container Service cria um serviço principal automaticamente. Isto é efetuado de forma transparente durante a implementação. 

O seguinte comando cria um cluster de Kubernetes e gera chaves SSH e credenciais do principal de serviço:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar um principal de serviço no Azure Active Directory

Se pretender criar um principal de serviço no Azure Active Directory para utilizar num cluster de Kubernetes, o Azure fornece vários métodos. 

Os comandos de exemplo seguintes mostram-lhe como fazê-lo com o [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2). Em alternativa pode criar um principal de serviço através do [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md), o [portal clássico](../azure-resource-manager/resource-group-create-service-principal-portal.md) ou outros métodos.

> [!IMPORTANT]
> Certifique-se de que revê os requisitos do principal de serviço mencionados anteriormente neste artigo.
>

```azurecli
az login

az account set --subscription "mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Esta ação devolve resultados semelhantes ao seguinte (mostrados aqui):

![Criar um principal de serviço](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

São realçados o **ID de cliente** (`appId`) e o **segredo do cliente** (`password`) que utiliza como parâmetros de principal de serviço para implementação de cluster.


Confirme o principal de serviço ao abrir uma nova shell e execute os seguintes comandos, substituindo no `appId`, `password`, e `tenant`:

```azurecli 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Considerações adicionais


* Quando especificar o principal de serviço **ID de Cliente**, pode utilizar o valor do `appId` (como mostrado neste artigo) ou o principal de serviço correspondente `name` (por exemplo, `https://www.contoso.org/example`).

* Se utilizar o comando `az acs create` para gerar automaticamente o principal de serviço, as credenciais do principal de serviço são escritas no ficheiro ~/.azure/acsServicePrincipal.json na máquina utilizada para executar o comando.

* Em VMs do nó e mestras no cluster de Kubernetes, as credenciais do principal de serviço são armazenadas no ficheiro /etc/kubernetes/azure.json.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Kubernetes](container-service-kubernetes-walkthrough.md) no cluster de serviço do contentor.


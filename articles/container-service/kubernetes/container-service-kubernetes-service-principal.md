---
title: "Principal de serviço do cluster de Kubernetes do Azure | Microsoft Docs"
description: "Criar e gerir um principal de serviço do Azure Active Directory para um cluster de Kubernetes no Azure Container Service"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 14975454cbc0afcfbdbd3aa6b52983be4d4b1785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>Configurar o principal de serviço do Azure AD para um cluster de Kubernetes no Container Service


No Azure Container Service, um cluster de Kubernetes requer um [principal de serviço do Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md) para interagir com APIs do Azure. O principal de serviço tem de gerir dinamicamente recursos, como [rotas definidas pelo utilizador](../../virtual-network/virtual-networks-udr-overview.md) e o [Balanceador de Carga do Azure de Camada 4](../../load-balancer/load-balancer-overview.md).


Este artigo mostra as diferentes opções para configurar um principal de serviço para o cluster de Kubernetes. Por exemplo, se instalou e configurou o [CLI do Azure 2.0](/cli/azure/install-az-cli2), pode executar o comando [`az acs create`](/cli/azure/acs#create), para criar o cluster de Kubernetes e o principal de serviço ao mesmo tempo.


## <a name="requirements-for-the-service-principal"></a>Requisitos para o principal de serviço

Pode utilizar um principal de serviço do Azure AD existente que cumpre os seguintes requisitos ou criar um novo.

* **Âmbito**: a subscrição utilizada para implementar o cluster.

* **Função**: **Contribuinte**

* **Segredo do cliente**: tem de ser uma palavra-passe. Atualmente, não pode utilizar um principal de serviço configurado para autenticação de certificados.

> [!IMPORTANT]
> Para criar um principal de serviço, tem de ter permissões para registar uma aplicação no seu inquilino do Azure AD e para atribuir a aplicação a uma função na sua subscrição. Para ver se tem as permissões necessárias, [verifique no Portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Opção 1: Criar um principal de serviço no Azure AD

Se pretender criar um principal de serviço no Azure AD antes de implementar um cluster de Kubernetes, o Azure apresenta vários métodos.

Os comandos de exemplo seguintes mostram-lhe como fazê-lo com o [CLI do Azure 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). Em alternativa pode criar um principal de serviço através do [Azure PowerShell](../../azure-resource-manager/resource-group-authenticate-service-principal.md), do [portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md) ou de outros métodos.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

O resultado é semelhante ao seguinte (mostrado aqui):

![Criar um principal de serviço](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

São realçados o **ID de cliente** (`appId`) e o **segredo do cliente** (`password`) que utiliza como parâmetros de principal de serviço para implementação de cluster.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Especifique o principal de serviço ao criar o cluster do Kubernetes

Forneça o **ID de cliente** (também designado pelo `appId`, para o ID da Aplicação) e **segredo do cliente** (`password`) de um principal de serviço existente como parâmetros ao criar o cluster de Kubernetes. Certifique-se de que o principal de serviço cumpre os requisitos no início deste artigo.

Pode especificar estes parâmetros quando implementar o cluster de Kubernetes com a [Interface de Linha de Comandos (CLI) do Azure 2.0](container-service-kubernetes-walkthrough.md), o [portal do Azure](../dcos-swarm/container-service-deployment.md) ou outros métodos.

>[!TIP]
>Quando especificar a **ID de cliente**, certifique-se de que utiliza o `appId`, e não o `ObjectId`, do principal de serviço.
>

O exemplo seguinte mostra uma forma de passar os parâmetros com a CLI do Azure 2.0. Este exemplo utiliza o [modelo de início rápido de Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

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


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Opção 2: gerar um principal de serviço ao criar o cluster com `az acs create`

Se executar o comando [`az acs create`](/cli/azure/acs#create) para criar o cluster de Kubernetes, tem a opção de gerar automaticamente um principal de serviço.

Tal como com outras opções de criação de cluster de Kubernetes, pode especificar parâmetros para um principal de serviço existente quando executa `az acs create`. No entanto, quando omitir estes parâmetros, a CLI do Azure cria um automaticamente para utilização com o Container Service. Isto é efetuado de forma transparente durante a implementação.

O seguinte comando cria um cluster de Kubernetes e gera chaves SSH e credenciais do principal de serviço:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Se a sua conta não tiver o Azure AD e as permissões de subscrição para criar um principal de serviço, o comando gera um erro semelhante a `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Considerações adicionais

* Se não tiver permissões para criar um principal de serviço na sua subscrição, poderá ter de pedir ao administrador do Azure AD ou subscrição para atribuir as permissões necessárias, ou solicite um principal de serviço para utilizar o Azure Container Service.

* O principal de serviço para Kubernetes faz parte da configuração do cluster. No entanto, não utilize a identidade para implementar o cluster.

* Cada principal de serviço está associado a uma aplicação do Azure AD. O principal de serviço para um cluster de Kubernetes pode ser associado a qualquer nome de aplicação do Azure AD válido (por exemplo: `https://www.contoso.org/example`). O URL para a aplicação não tem de ser um ponto final real.

* Quando especificar o principal de serviço **ID de Cliente**, pode utilizar o valor do `appId` (como mostrado neste artigo) ou o principal de serviço correspondente `name` (por exemplo, `https://www.contoso.org/example`).

* Em VMs do agente e mestras no cluster de Kubernetes, as credenciais do principal de serviço são armazenadas no ficheiro /etc/kubernetes/azure.json.

* Quando utilizar o comando `az acs create` para gerar automaticamente o principal de serviço, as credenciais do principal de serviço são escritas no ficheiro ~/.azure/acsServicePrincipal.json na máquina utilizada para executar o comando.

* Quando utiliza o comando `az acs create` para gerar automaticamente o principal de serviço, o principal de serviço também pode autenticar com um [Azure container registry](../../container-registry/container-registry-intro.md) criado na mesma subscrição.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Kubernetes](container-service-kubernetes-walkthrough.md) no cluster de serviço do contentor.

* Para resolver problemas relacionados com o principal de serviço do Kubernetes, consulte [ACS Engine documentation](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting) (Documentação do Motor de ACS).
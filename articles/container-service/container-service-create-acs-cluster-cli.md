---
title: Implementar um cluster de contentor Docker no Azure CLI | Microsoft Docs
description: "Implementar um cluster do Azure Container Service com a Pré-visualização do Azure CLI 2.0"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: df916670743158d6a22b3f17343630114584fa08
ms.openlocfilehash: 65f1c812472f4a3b6d4a4e6fb7666a2c022af102


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>Utilizar o Azure CLI 2.0 (Pré-visualização) para criar um cluster do Azure Container Service

Utilize os comandos `az acs` no Azure CLI 2.0 (Pré-visualização), para criar e gerir clusters no Azure Container Service. Também pode implementar um cluster do Azure Container Service com o [portal do Azure](container-service-deployment.md) ou as APIs do Azure Container Service.

Para obter ajuda com os comandos `az acs`, passe o parâmetro `-h` para qualquer comando. Por exemplo: `az acs create -h`.



## <a name="prerequisites"></a>Pré-requisitos
Para criar um cluster do Azure Container Service com o Azure CLI 2.0 (Pré-visualização), tem de:
* ter uma conta do Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* ter instalado e configurado o [Azure CLI v. 2.0 (Pré-visualização)](/cli/azure/install-az-cli2)

## <a name="get-started"></a>Introdução 
### <a name="log-in-to-your-account"></a>Iniciar sessão na sua conta
```azurecli
az login 
```

Siga os pedidos para iniciar sessão interativamente. Para outros métodos para iniciar sessão, consulte [Get started with Azure CLI 2.0 (Preview) (Introdução ao Azure CLI 2.0 (Pré-visualização))](/cli/azure/get-started-with-az-cli2).

### <a name="set-your-azure-subscription"></a>Defina a sua subscrição do Azure

Se tiver mais do que uma subscrição do Azure, defina a subscrição predefinida. Por exemplo:

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Recomendamos que crie um grupo de recursos para cada cluster. Especifique uma região do Azure em que o Azure Container Service esteja [disponível](https://azure.microsoft.com/en-us/regions/services/). Por exemplo:

```azurecli
az group create -n acsrg1 -l "westus"
```
O resultado é semelhante ao seguinte:

![Criar um grupo de recursos](media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Crie um cluster do Azure Container Service

Para criar um cluster, utilize `az acs create`.
Um nome para o cluster e nome do grupo de recursos criado no passo anterior são parâmetros obrigatórios. 

As outras entradas estão definidas para os valores predefinidos (veja o ecrã seguinte), a menos que sejam substituídas pelos respetivos comutadores. Por exemplo, o orquestrador é definido por predefinição para DC/SO. E se não especificar nenhum, é criado um prefixo de nome DNS com base no nome do cluster.

![utilização de criação de az acs](media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>Rápido `acs create` com predefinições
Se tiver um ficheiro de chave pública SSH `id_rsa.pub` na localização predefinida (ou tiver criado um para [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md)), utilize um comando como o seguinte:

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
Se não tiver uma chave pública SSH, utilize este segundo comando. Este comando com o comutador `--generate-ssh-keys` cria uma por si.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

Depois de introduzir o comando, aguarde cerca de 10 minutos para o cluster ser criado. A saída do comando inclui nomes de domínio completamente qualificados (FQDN) dos nós de agente e mestre, e um comando SSH para ligar ao primeiro mestre. Eis a saída abreviada:

![Imagem de criação de ACS](media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> As [instruções da Kubernetes ](container-service-kubernetes-walkthrough.md) mostram como utilizar `az acs create` com valores predefinidos, para criar um cluster de Kubernetes.
>

## <a name="manage-acs-clusters"></a>Gerir clusters ACS

Utilize comandos `az acs` adicionais para gerir o cluster. Eis alguns exemplos.

### <a name="list-clusters-under-a-subscription"></a>Listar clusters numa subscrição

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>Listar clusters num grupo de recursos

```azurecli
az acs list -g acsrg1 --output table
```

![listar acs](media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>Apresentar detalhes de um cluster de serviço de contentor

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![apresentação de acs](media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>Dimensionar o cluster
É permitido aumentar vertical e horizontalmente nós de agente. O parâmetro `new-agent-count` é o novo número de agentes no cluster ACS.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![dimensionamento de acs](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Eliminar um cluster do serviço de contentor
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
Este comando de eliminação não elimina todos os recursos (rede e armazenamento) criados durante a criação do serviço de contentor. Para eliminar todos os recursos facilmente, recomenda-se que implemente cada cluster num grupo de recursos distintos. Em seguida, elimine o grupo de recursos quando o cluster já não for necessário.

## <a name="next-steps"></a>Passos seguintes
Agora que tem um cluster a funcionar, veja estes documentos para obter os detalhes de ligação e de gestão:

* [Ligar a um cluster do Azure Container Service](container-service-connect.md)
* [Trabalhar com o Azure Container Service e o DC/OS](container-service-mesos-marathon-rest.md)
* [Trabalhar com o Azure Container Service e o Docker Swarm](container-service-docker-swarm.md)
* [Trabalhar com o Azure Container Service e o Kubernetes](container-service-kubernetes-walkthrough.md)


<!--HONumber=Feb17_HO1-->



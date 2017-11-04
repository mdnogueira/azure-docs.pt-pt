---
title: Utilize ficheiros do Azure com AKS | Microsoft Docs
description: Utilizar os discos do Azure com AKS
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6e88c590e11aa8d2f4ae17e8b5e164483f0a6820
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Utilizar os ficheiros do Azure com Kubernetes

Aplicações de contentor com base em muitas vezes necessitam de acesso e manter os dados de um volume de dados externas. Ficheiros do Azure podem ser utilizados como este arquivo de dados externas. Este detalhes de artigo com ficheiros do Azure como um volume Kubernetes no serviço de contentor do Azure.

Para obter mais informações sobre Kubernetes volumes, consulte [Kubernetes volumes][kubernetes-volumes].

## <a name="creating-a-file-share"></a>Criar uma partilha de ficheiros

Uma partilha de ficheiros do Azure existente, pode ser utilizada com o serviço de contentor do Azure. Se precisar de criar um, utilize o seguinte conjunto de comandos.

Criar um grupo de recursos para a partilha de ficheiros do Azure utilizando o [criar grupo az] [ az-group-create] comando. O grupo de recursos da conta de armazenamento e Kubernetes cluster tem de estar localizado na mesma região.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Utilize o [criar conta de armazenamento az] [ az-storage-create] comando para criar uma conta de armazenamento do Azure. O nome da conta de armazenamento tem de ser exclusivo. Atualize o valor da `--name` argumento com um valor exclusivo.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

Utilize o [lista de chaves de conta de armazenamento az ] [ az-storage-key-list] comando para devolver a chave de armazenamento. Atualize o valor da `--account-name` argumento com o nome de conta de armazenamento exclusivas.

Tome nota de um dos valores da chave, esta informação é utilizada nos passos subsequentes.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

Utilize o [criar partilha de armazenamento az] [ az-storage-share-create] comando para criar a partilha de ficheiros do Azure. Atualização do `--account-key` valor com o valor recolhidos no último passo.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Criar Kubernetes segredo

Kubernetes necessita de credenciais para aceder à partilha de ficheiros. Em vez de armazenar o nome da conta de armazenamento do Azure e a chave com cada pod, são armazenados uma vez num [Kubernetes segredo] [ kubernetes-secret] e referenciado por cada volume de ficheiros do Azure. 

Os valores existentes num manifesto secreto Kubernetes tem de ser codificado em base64. Utilize os seguintes comandos para devolver valores codificados.

Em primeiro lugar, codificar o nome da conta de armazenamento. Substitua `storage-account` com o nome da sua conta de armazenamento do Azure.

```azurecli-interactive
echo -n <storage-account> | base64
```

Em seguida, é necessária a chave de acesso da conta de armazenamento. Execute o seguinte comando para devolver a chave codificada. Substitua `storage-key` com a chave recolhida no passo anterior

```azurecli-interactive
echo -n <storage-key> | base64
```

Crie um ficheiro denominado `azure-secret.yml` e copie o seguinte YAML. Atualização do `azurestorageaccountname` e `azurestorageaccountkey` valores com o base64 codificado valores obtidos no passo anterior.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

Utilize o [kubectl aplicar] [ kubectl-apply] comando para criar o segredo.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Montar a partilha de ficheiros como volume

É possível montar a partilha de ficheiros do Azure no seu pod configurando o volume na respetiva especificação. Criar um novo ficheiro designado `azure-files-pod.yml` com o seguinte conteúdo. Atualização `share-name` com o nome atribuído ao Azure Files partilhar.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: <share-name>
      readOnly: false
```

Utilize kubectl para criar um pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

Tem agora um contentor em execução com a partilha de ficheiros do Azure montada no `/mnt/azure` diretório. Pode ver o quando inspecionar o pod através de montagem do volume `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os volumes de Kubernetes utilizando ficheiros do Azure.

> [!div class="nextstepaction"]
> [Plug-in do Kubernetes para ficheiros do Azure](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create
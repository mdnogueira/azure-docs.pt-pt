---
title: "Montar um volume de ficheiros do Azure em instâncias de contentor do Azure"
description: "Saiba como montar um volume de ficheiros do Azure para manter o estado com instâncias de contentor do Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 41c3a449b39d6ef77e1dd0cf10699f8debcad475
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="mounting-an-azure-file-share-with-azure-container-instances"></a>Montar uma partilha de ficheiros do Azure com instâncias de contentor do Azure

Por predefinição, as instâncias de contentor do Azure são sem monitorização de estado. Se o contentor de falhas de ou para, todos os respetivo estado é perdido. Para manter o estado para além da duração do contentor, tem de montar um volume de um arquivo de externo. Este artigo mostra como montar uma partilha de ficheiros do Azure para utilização com instâncias de contentor do Azure.

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Antes de utilizar uma partilha de ficheiros do Azure com instâncias de contentor do Azure, tem de criá-la. Execute o script seguinte para criar uma conta de armazenamento para alojar a partilha de ficheiros e a partilha de si próprio. O nome da conta de armazenamento tem de ser globalmente exclusivo para que o script adiciona um valor aleatório para a cadeia de base.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Adquirir detalhes de acesso da conta de armazenamento

Para montar uma partilha de ficheiros do Azure como um volume em instâncias de contentor do Azure, terá três valores: o nome da conta de armazenamento, o nome da partilha e a chave de acesso de armazenamento.

Se utilizou o script acima, o nome da conta de armazenamento foi criado com um valor aleatório no final. Para consultar a cadeia final (incluindo a parte aleatória), utilize os seguintes comandos:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

O nome da partilha já é conhecido (é *acishare* no script acima), por isso, todas as que permanece é a chave de conta de armazenamento, que pode ser encontrada utilizando o seguinte comando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Armazene os detalhes de acesso da conta de armazenamento com o Cofre de chaves do Azure

Chaves de conta de armazenamento proteger o acesso aos seus dados, pelo que recomendamos que armazene-os num cofre de chaves do Azure.

Crie um cofre de chaves com a CLI do Azure:

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

O `enabled-for-template-deployment` comutador permite ao Azure Resource Manager segredos de solicitação do seu Cofre de chaves no momento da implementação.

Armazene a chave de conta de armazenamento como um novo segredo no Cofre de chaves:

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>Montar o volume

Montar uma partilha de ficheiros do Azure como um volume num contentor é um processo de dois passos. Em primeiro lugar, forneça os detalhes da partilha como parte de definir o grupo de contentor, em seguida, especificar como pretende que o volume montado dentro de um ou mais dos contentores no grupo de.

Para definir os volumes que pretende tornar disponível para a montagem, adicione um `volumes` de matriz para a definição de grupo do contentor no modelo Azure Resource Manager, em seguida, referenciar a definição dos contentores individuais.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

O modelo inclui o nome da conta de armazenamento e a chave como parâmetros, o que podem ser fornecidos num ficheiro separado de parâmetros. Para preencher o ficheiro de parâmetros, terá três valores: o nome da conta de armazenamento, o ID de recurso do seu Cofre de chaves do Azure e o nome secreto do Cofre de chaves que utilizou para armazenar a chave de armazenamento. Se seguiu os passos anteriores, pode obter estes valores com o seguinte script:

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

Inserir os valores no ficheiro de parâmetros:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>Implementar o contentor e gerir os ficheiros

Com o modelo definido, pode criar o contentor e montar o volume utilizando a CLI do Azure. Partindo do princípio de que o ficheiro de modelo é denominado *azuredeploy. JSON* e de que o ficheiro de parâmetros é denominado *azuredeploy.parameters.json*, em seguida, a linha de comando é:

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group myResourceGroup
```

Depois do contentor é iniciado, pode utilizar a aplicação web simples implementada através de **aci/seanmckenna-hellofiles** imagem, para gerir os ficheiros na partilha de ficheiros do Azure no caminho de montagem que especificou. Obter o endereço IP para a aplicação web através do seguinte:

```azurecli-interactive
az container show --resource-group myResourceGroup --name hellofiles -o table
```

Pode utilizar uma ferramenta como o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) para obter e Inspecione o ficheiro de escrita à partilha de ficheiros.

>[!NOTE]
> Para saber mais sobre como utilizar os modelos Azure Resource Manager, consulte os ficheiros de parâmetro e a implementação com a CLI do Azure, [implementar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

## <a name="next-steps"></a>Passos seguintes

- Implementar para o seu primeiro contentor utilizando as instâncias de contentor do Azure [início rápido](container-instances-quickstart.md)
- Saiba mais sobre o [relação entre instâncias de contentor do Azure e orchestrators de contentor](container-instances-orchestrator-relationship.md)

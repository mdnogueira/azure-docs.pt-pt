---
title: Partilha de ficheiros para o cluster DC/OS do Azure | Microsoft Docs
description: "Criar e montar uma partilha de ficheiros para um cluster DC/SO no serviço de contentor do Azure"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: "Docker, contentores, microserviços, Mesos, Azure, partilha de ficheiros, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: a5905cac12f52f94a5722cc01495d5c1168634f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Criar e montar uma partilha de ficheiros para um cluster DC/OS
Este tutorial fornece detalhes sobre como criar uma partilha de ficheiros no Azure e montá-la em cada agente e o mestre do cluster DC/OS. Configurar uma partilha de ficheiros torna mais fácil partilhar ficheiros em cluster, tais como a configuração, acesso, os registos e muito mais. As seguintes tarefas são concluídas neste tutorial:

> [!div class="checklist"]
> * Criar uma conta do Storage do Azure
> * Criar uma partilha de ficheiros
> * Montar a partilha no cluster DC/OS

Precisa de um cluster de ACS DC/OS para concluir os passos neste tutorial. Se for necessário, [este script de exemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar uma por si.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Criar uma partilha de ficheiros no Microsoft Azure

Antes de utilizar uma partilha de ficheiros do Azure com um cluster DC/SO de ACS, tem de criar a partilha de ficheiros e de conta de armazenamento. Execute o script seguinte para criar a partilha de ficheiros e armazenamento. Atualize os parâmetros com thoes do seu ambiente.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Montar a partilha do cluster

Em seguida, a partilha de ficheiros tem de ser montado em cada máquina virtual no interior do seu cluster. Esta tarefa é concluída com o ferramenta/protocolo cifs. A operação de montagem pode ser concluída manualmente em cada nó do cluster, ou executando um script em relação a cada nó do cluster.

Neste exemplo, dois scripts são executados, um para montar a partilha de ficheiros do Azure e um segundo para executar este script em cada nó do cluster DC/OS.

Em primeiro lugar, o nome da conta de armazenamento do Azure e a chave de acesso são necessárias. Execute os seguintes comandos para obter estas informações. Tome nota da cada, estes valores são utilizados num passo posterior.

Nome da conta de armazenamento:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Chave de acesso da conta de armazenamento:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Em seguida, obter o FQDN do mestre de DC/OS e armazene-o numa variável.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Copie a chave privada para o nó mestre. Esta chave é necessária para criar um ssh ligação com todos os nós do cluster. Se um valor predefinido não foi utilizado ao criar o cluster, atualize o nome de utilizador. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Crie uma ligação SSH com o mestre (ou o mestre de primeiro) do cluster baseado no DC/SO. Se um valor predefinido não foi utilizado ao criar o cluster, atualize o nome de utilizador.

```azurecli-interactive
ssh azureuser@$FQDN
```

Crie um ficheiro denominado **cifsMount.sh**e copie o seguinte conteúdo para a mesma. 

Este script é utilizado para montar a partilha de ficheiros do Azure. Atualização do `STORAGE_ACCT_NAME` e `ACCESS_KEY` variáveis com as informações anteriormente recolhidas.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Crie um segundo ficheiro denominado **getNodesRunScript.sh** e copie o seguinte conteúdo para o ficheiro. 

Este script Deteta todos os nós de cluster e, em seguida, executa a **cifsMount.sh** script para montar a partilha de ficheiros em cada um.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Execute o script para montar a partilha de ficheiros do Azure em todos os nós do cluster.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

A partilha de ficheiros está agora acessível em `/mnt/share/dcosshare` em cada nó do cluster.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial um Azure partilha de ficheiros foi disponibilizada para um cluster DC/OS, utilizando os passos:

> [!div class="checklist"]
> * Criar uma conta do Storage do Azure
> * Criar uma partilha de ficheiros
> * Montar a partilha no cluster DC/OS

Avançar para o próximo tutorial para saber mais sobre a integração de um registo de contentor do Azure com o DC/OS, no Azure.  

> [!div class="nextstepaction"]
> [Fazer o balanceamento de carga de aplicações](container-service-dcos-acr.md)

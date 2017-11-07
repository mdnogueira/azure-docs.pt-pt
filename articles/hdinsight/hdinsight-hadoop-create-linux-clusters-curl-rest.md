---
title: "Criar clusters do Hadoop através da API REST da Azure - Azure | Microsoft Docs"
description: Saiba como criar clusters do HDInsight ao submeter os modelos Azure Resource Manager para a API de REST do Azure.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: larryfr
ms.openlocfilehash: f35c50a3b05bfeb06556f5eb9ba70e24b80de6e9
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="create-hadoop-clusters-using-the-azure-rest-api"></a>Criar clusters do Hadoop, utilizando a API de REST do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Saiba como criar um cluster do HDInsight utilizando um modelo Azure Resource Manager e a API de REST do Azure.

API REST da Azure permite-lhe efetuar operações de gestão em serviços alojados na plataforma do Azure, incluindo a criação de novos recursos, tais como clusters do HDInsight.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

> [!NOTE]
> Os passos neste documento utilize o [curl (https://curl.haxx.se/)](https://curl.haxx.se/) utilitário para comunicar com a API de REST do Azure.

## <a name="create-a-template"></a>Criar um modelo

Modelos Azure Resource Manager são documentos JSON que descrevem um **grupo de recursos** e todos os recursos nele (por exemplo, o HDInsight.) Esta abordagem baseada em modelo permite-lhe definir os recursos que necessita para o HDInsight num modelo.

O documento JSON seguinte é uma fusão dos ficheiros de modelo e os parâmetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que cria um cluster baseado em Linux utilizando uma palavra-passe para proteger a conta de utilizador SSH.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Neste exemplo é utilizado nos passos neste documento. Substituir o exemplo *valores* no **parâmetros** secção com os valores para o cluster.

> [!IMPORTANT]
> O modelo utiliza o número predefinido de nós de trabalho (4) para um cluster do HDInsight. Se pretender mais do que 32 nós de trabalho, tem de selecionar um tamanho de nó principal com pelo menos 8 núcleos e 14 GB de ram.
>
> Para obter mais informações sobre os tamanhos de nós e os custos associados, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="log-in-to-your-azure-subscription"></a>Inicie sessão na subscrição do Azure

Siga os passos documentados no [introdução ao Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) e ligue à sua subscrição utilizando o `az login` comando.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

> [!NOTE]
> Estes passos são uma resumidas da versão do *criar serviço principal com palavra-passe* secção o [CLI do Azure de utilização para criar um principal de serviço para aceder a recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md) documento. Estes passos criar um principal de serviço que é utilizado para autenticar para a API de REST do Azure.

1. A partir de uma linha de comandos, utilize o seguinte comando para listar as suas subscrições do Azure.

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    Na lista, selecione a subscrição que pretende utilizar e tenha em atenção o **Subscription_ID** e __Tenant_ID__ colunas. Guarde estes valores.

2. Utilize o seguinte comando para criar uma aplicação no Azure Active Directory.

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Substitua os valores para o `--display-name`, `--homepage`, e `--identifier-uris` com os seus próprios valores. Forneça uma palavra-passe para a nova entrada de Active Directory.

   > [!NOTE]
   > O `--home-page` e `--identifier-uris` valores não precisam de fazer referência a uma página web real alojados na internet. Têm de ser URIs exclusivo.

   O valor devolvido a partir deste comando é o __ID da aplicação__ para a nova aplicação. Guarde este valor.

3. Utilize o seguinte comando para criar um principal de serviço com o **ID da aplicação**.

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     O valor devolvido a partir deste comando é o __ID de objeto__. Guarde este valor.

4. Atribuir o **proprietário** função para o serviço principal a utilizar o **ID de objeto** valor. Utilize o **ID de subscrição** que obteve anteriormente.

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Obter um token de autenticação

Utilize o seguinte comando para obter um token de autenticação:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

Definir `$TENANTID`, `$APPID`, e `$PASSWORD` para os valores obtidos ou utilizada anteriormente.

Se este pedido é efetuada com êxito, receberá uma resposta de 200 série e o corpo da resposta contém um documento JSON.

O documento JSON devolvido por este pedido contém um elemento com o nome **access_token**. O valor de **access_token** é utilizado para pedidos de autenticação para a API REST.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Utilize o seguinte para criar um grupo de recursos.

* Definir `$SUBSCRIPTIONID` à subscrição do ID de recebido ao criar o principal de serviço.
* Definir `$ACCESSTOKEN` para o token de acesso recebido no passo anterior.
* Substitua `DATACENTERLOCATION` com o Centro de dados que pretende criar o grupo de recursos e recursos, no. Por exemplo, 'Sul Central nos'.
* Definir `$RESOURCEGROUPNAME` para o nome que pretende utilizar para este grupo:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Se este pedido é efetuada com êxito, receberá uma resposta de 200 série e o corpo da resposta contém um documento JSON que contém informações sobre o grupo. O `"provisioningState"` elemento contém um valor de `"Succeeded"`.

## <a name="create-a-deployment"></a>Criar uma implementação

Utilize o seguinte comando para implementar o modelo para o grupo de recursos.

* Definir `$DEPLOYMENTNAME` para o nome que pretende utilizar para esta implementação.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> Se guardar o modelo para um ficheiro, pode utilizar o seguinte comando, em vez de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se este pedido é efetuada com êxito, receberá uma resposta de 200 série e o corpo da resposta contém um documento JSON que contém informações sobre a operação de implementação.

> [!IMPORTANT]
> A implementação foi submetida, mas não foi concluído. Pode demorar vários minutos, normalmente, cerca de 15, para a implementação concluir.

## <a name="check-the-status-of-a-deployment"></a>Verificar o estado de uma implementação

Para verificar o estado da implementação, utilize o seguinte comando:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Este comando devolve um documento JSON que contém informações sobre a operação de implementação. O `"provisioningState"` elemento contém o estado da implementação. Se este elemento contém um valor de `"Succeeded"`, em seguida, a implementação foi concluída com êxito.

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos seguintes

Agora que criou com êxito um cluster do HDInsight, utilize o seguinte para saber como trabalhar com o cluster.

### <a name="hadoop-clusters"></a>Clusters do Hadoop

* [Utilizar o Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters de HBase

* [Introdução ao HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para o HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters de Storm

* [Desenvolver topologias de Java de Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilize os componentes de Python Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar as topologias Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

---
title: "Utilize o Gestor de recursos de modelos na fábrica de dados | Microsoft Docs"
description: Saiba como criar e utilizar modelos Azure Resource Manager para criar entidades do Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: c80281a520159965066c6cd166f47ecac66e552b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Utilizar modelos para criar entidades do Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). 

## <a name="overview"></a>Descrição geral
Ao utilizar o Azure Data Factory para as suas necessidades de integração de dados, poderá encontrar-se reutilizar o mesmo padrão entre ambientes diferentes ou implementar a mesma tarefa repetitively dentro da mesma solução. Os modelos ajudam-na implementar e gerir estes cenários de forma mais fácil. Modelos no Azure Data Factory são ideais para cenários que envolvem reusability e repetição.

Considere a situação em que uma organização tem 10 fábricas de produção por todo o mundo. Os registos de cada maquinaria são armazenados numa base de dados do SQL Server no local independente. A empresa pretende compilar um armazém de dados único na nuvem para análise de ad-hoc. Também pretende ter a mesma lógica mas diferentes configurações para ambientes de desenvolvimento, teste e produção.

Neste caso, uma tarefa tem de ser repetido dentro do mesmo ambiente, mas com valores diferentes entre as fábricas de dados de 10 para cada maquinaria de fabrico. Em vigor, **repetição** está presente. Templating permite a abstração deste fluxo genérico (ou seja, pipelines de ter as mesmas atividades cada fábrica de dados), mas utilizam um ficheiro de parâmetros separado para cada maquinaria de fabrico.

Além disso, como a organização pretende implementar estas fábricas de dados de 10 várias vezes entre ambientes diferentes, modelos de podem utilizar esta **reusability** através da utilização de ficheiros de parâmetro separado para ambientes de desenvolvimento, teste e produção.

## <a name="templating-with-azure-resource-manager"></a>Templating com o Azure Resource Manager
[Modelos Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md#template-deployment) são uma excelente forma de alcançar templating no Azure Data Factory. Modelos do Resource Manager definem a infraestrutura e a configuração da sua solução do Azure através de um ficheiro JSON. Porque os modelos Azure Resource Manager trabalham com serviços do Azure all/maioria, pode ser utilizado de amplamente gerir facilmente todos os recursos dos seus recursos do Azure. Consulte [modelos Authoring Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para saber mais sobre os modelos do Resource Manager em geral.

## <a name="tutorials"></a>Tutoriais
Consulte os tutoriais seguintes para obter instruções passo a passo Criar entidades do Data Factory com modelos do Resource Manager:

* [Tutorial: Criar um pipeline para copiar dados utilizando o modelo Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Tutorial: Criar um pipeline para processar dados utilizando o modelo Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelos de fábrica de dados no GitHub
Consulte os seguintes modelos de início rápido do Azure no GitHub:

* [Criar uma fábrica de dados ao copiar dados do Blob Storage do Azure para a SQL Database do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Criar uma fábrica de dados com a atividade do ramo de registo num cluster do Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Criar uma fábrica de dados para copiar dados do Salesforce para Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Crie uma fábrica de dados que encadeia atividades: copia dados a partir de um servidor FTP para Blobs do Azure, invoca um script de ramo de registo num cluster do HDInsight a pedido para transformar os dados e copia o resultado na SQL Database do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

À vontade partilhar os seus modelos do Azure Data Factory no [início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Consulte o [guia contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) ao desenvolver modelos que podem ser partilhados através deste repositório.

As secções seguintes fornecem detalhes sobre como definir os recursos de fábrica de dados num modelo do Resource Manager.

## <a name="defining-data-factory-resources-in-templates"></a>Definir os recursos de fábrica de dados nos modelos
O modelo de nível superior para definir uma fábrica de dados é:

```JSON
"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definir fábrica de dados
Defina uma fábrica de dados no modelo do Resource Manager, conforme mostrado no exemplo seguinte:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
O dataFactoryName está definida em "variáveis" como:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definir os serviços ligados

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Consulte [serviço ligado do Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [serviços ligados de computação](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON para o serviço ligado específico que pretende implementar. O parâmetro de "dependsOn" Especifica o nome da fábrica de dados correspondente. Um exemplo de definir um serviço ligado de armazenamento do Azure é apresentado na definição do JSON seguinte:

### <a name="define-datasets"></a>Definir os conjuntos de dados

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Consulte [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter detalhes sobre as propriedades JSON para o tipo de conjunto de dados específicos que pretende implementar. Tenha em atenção que o parâmetro de "dependsOn" Especifica o nome da fábrica de dados correspondentes e de armazenamento de serviço ligado. Um exemplo de definir o tipo de conjunto de dados de armazenamento de Blobs do Azure é apresentado na definição do JSON seguinte:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definir pipelines

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Consulte [definir pipelines](data-factory-create-pipelines.md#pipeline-json) para obter detalhes sobre as propriedades JSON para definir o pipeline específico e as atividades que pretende implementar. Tenha em atenção que o parâmetro de "dependsOn" Especifica o nome da fábrica de dados e qualquer correspondente ligadas serviços ou os conjuntos de dados. Um exemplo de um pipeline que copia dados a partir do Blob Storage do Azure a SQL Database do Azure é apresentado no fragmento JSON seguinte:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Modelo parameterizing do Data Factory
Para melhores práticas em parameterizing, consulte [melhores práticas para a criação de modelos Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md#parameters) artigo. Em geral, utilização de parâmetros deve ser minimizada, especialmente se variáveis podem ser utilizadas em vez disso. Fornece apenas parâmetros nos seguintes cenários:

* Definições variam consoante o ambiente (exemplo: desenvolvimento, teste e produção)
* Segredos (tal como as palavras-passe)

Se precisar de solicitação segredos de [Cofre de chaves do Azure](../../key-vault/key-vault-get-started.md) quando implementar entidades do Azure Data Factory através de modelos, especifique o **Cofre de chaves** e **nome secreto** conforme mostrado no exemplo seguinte:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Ao exportar modelos para fábricas de dados existentes atualmente ainda não é suportado, faz-se do funciona.
>
>

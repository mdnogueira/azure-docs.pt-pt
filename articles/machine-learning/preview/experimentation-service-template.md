---
title: "Criar o Azure Machine Learning experimentação com um modelo Azure Resource Manager | Microsoft Docs"
description: "Este artigo fornece um exemplo para criar uma conta do Azure Machine Learning experimentação através de um modelo Azure Resource Manager."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 2efffd14c748a070fff59bcf9dd21c02eb9f6dcd
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Configurar o serviço de experimentação do Azure Machine Learning

## <a name="overview"></a>Descrição geral
Conta de serviço de experimentação do Machine Learning do Azure, área e projeto são recursos do Azure. Como tal, que podem ser implementados através de modelos do Gestor de recursos. Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para compreender os conceitos associados à implementação e gestão das suas soluções do Azure, veja [Descrição geral do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Implementar um modelo
A implementação de um modelo requer apenas alguns passos na Interface de linha de comandos do Azure ou no portal do Azure.

### <a name="deploy-a-template-from-the-command-line"></a>Implementar um modelo na linha de comandos
Utilizar a interface de linha de comandos, um único comando pode implementar um modelo a um grupo de recursos existente.
Consulte seguinte para obter informações sobre como criar um modelo.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Implementar um modelo a partir do portal do Azure
Se preferir, também pode utilizar o portal do Azure para criar e implementar um modelo. Proceda do seguinte modo:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** e procure "modelos."
3. Selecione **modelos**.
4. Clique em **+ adicionar** e copie as suas informações de modelo. 
5. Selecione o modelo que criou no passo #4 e clique em **implementar**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Criar um modelo a partir de um recurso do Azure existente no portal do Azure
Se já tiver uma experimentação de máquina do Azure de conta disponíveis, em [portal do Azure](https://portal.azure.com), pode gerar um modelo a partir desse recurso. 

1. Navegue para uma conta de experimentação do Azure no [portal do Azure](https://portal.azure.com).
2. Em **definições**, clique em **scripts de automatização**.
3. Transferir o modelo. 

Em alternativa, pode editar manualmente os ficheiros de modelo. Consulte o seguinte para obter um exemplo de um modelo e os parâmetros de ficheiros. 

### <a name="template-file-example"></a>Exemplo de ficheiro de modelo
Crie um ficheiro chamado "modelo file.json" com abaixo conteúdo. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>Parâmetros 
Crie um ficheiro com abaixo conteúdo e guarde-o como < Parameters. JSON >. 

Existem três valores que podem ser alteradas. 
* AccountName: O nome da conta de experimentação.
* Localização: Uma das regiões do Azure suportadas.
* Conta de armazenamento SKU: Azure ML só suporta o armazenamento standard, premium não. Para obter mais informações sobre o armazenamento, consulte [introdução de armazenamento](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Passos seguintes
* [Criar e instalar o Azure Machine Learning](quickstart-installation.md)

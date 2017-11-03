---
title: Segredo do Cofre de chaves com o modelo do Resource Manager | Microsoft Docs
description: "Mostra como passar um segredo a partir de um cofre de chaves como um parâmetro durante a implementação."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 1ca72599e67e79d42a3d430dbb13e89ea7265334
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Utilizar o Cofre de chaves para passar o valor do parâmetro seguro durante a implementação

Quando precisar de transferir um valor seguro (como uma palavra-passe) como um parâmetro durante a implementação, pode obter o valor de um [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md). Obter o valor ao consultar o Cofre de chaves e um segredo no seu ficheiro de parâmetros. O valor nunca está exposto porque tem só fazem referência a um ID de Cofre de chaves. Não é necessário introduzir manualmente o valor para o segredo sempre que implementar os recursos. O Cofre da chave pode existir numa subscrição diferente do grupo de recursos que está a implementar. Quando referenciar o Cofre de chaves, incluem o ID da subscrição.

Quando criar o Cofre de chaves, defina o *enabledForTemplateDeployment* propriedade *verdadeiro*. Por definição deste valor como VERDADEIRO, é permitir o acesso a partir de modelos do Resource Manager durante a implementação.  

## <a name="deploy-a-key-vault-and-secret"></a>Implementar um cofre de chaves e um segredo

Para criar um cofre de chaves e o segredo, utilize o CLI do Azure ou o PowerShell. Tenha em atenção que o Cofre de chaves está ativado para implementação do modelo. 

Para a CLI do Azure, utilize:

```azurecli
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create --name $vaultname --resource-group examplegroup --location 'South Central US' --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Para o PowerShell, utilize:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault -VaultName $vaultname -ResourceGroupName examplegroup -Location "South Central US" -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>Ativar o acesso para o segredo

Se estiver a utilizar um cofre de chaves novo ou existente, certifique-se de que o utilizador a implementar o modelo possa aceder o segredo. O utilizador ao implementar um modelo que referencia um segredo tem de ter o `Microsoft.KeyVault/vaults/deploy/action` permissão para o Cofre de chaves. O [proprietário](../active-directory/role-based-access-built-in-roles.md#owner) e [contribuinte](../active-directory/role-based-access-built-in-roles.md#contributor) funções ambos os conceder o acesso. Também pode criar um [função personalizada](../active-directory/role-based-access-control-custom-roles.md) que concede esta permissão e adicione o utilizador a essa função. Para obter informações sobre como adicionar um utilizador a uma função, consulte [atribuir um utilizador a funções de administrador no Azure Active Directory](../active-directory/active-directory-users-assign-role-azure-portal.md).


## <a name="reference-a-secret-with-static-id"></a>Referenciar um segredo com o ID estático

O modelo que recebe um segredo do Cofre de chaves é como qualquer modelo. Isto acontece porque **referenciar o Cofre de chaves no ficheiro de parâmetros, não o modelo.** Por exemplo, o modelo seguinte implementa uma base de dados do SQL Server que inclui uma palavra-passe de administrador. O parâmetro de palavra-passe está definido como uma cadeia segura. No entanto, o modelo não especifica onde vêm esse valor.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "type": "string"
        },
        "administratorLoginPassword": {
            "type": "securestring"
        },
        "collation": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        },
        "edition": {
            "type": "string"
        },
        "requestedServiceObjectiveId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "maxSizeBytes": {
            "type": "string"
        },
        "serverName": {
            "type": "string"
        },
        "sampleName": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "name": "[parameters('serverName')]",
            "properties": {
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "version": "12.0"
            },
            "resources": [
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "[parameters('databaseName')]",
                    "properties": {
                        "collation": "[parameters('collation')]",
                        "edition": "[parameters('edition')]",
                        "maxSizeBytes": "[parameters('maxSizeBytes')]",
                        "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]",
                        "sampleName": "[parameters('sampleName')]"
                    },
                    "type": "databases"
                },
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "AllowAllWindowsAzureIps",
                    "properties": {
                        "endIpAddress": "0.0.0.0",
                        "startIpAddress": "0.0.0.0"
                    },
                    "type": "firewallrules"
                }
            ],
            "type": "Microsoft.Sql/servers"
        }
    ]
}
```

Agora, crie um ficheiro de parâmetros para o modelo anterior. No ficheiro de parâmetros, especifique um parâmetro que corresponda ao nome do parâmetro no modelo. Para o valor do parâmetro, consulte o segredo do Cofre de chaves. Referenciar o segredo transferindo o identificador de recurso do Cofre de chaves e o nome do segredo. No exemplo seguinte, o segredo do Cofre de chaves tem de existir e é fornecer um valor estático para o ID de recurso.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "value": "exampleadmin"
        },
        "administratorLoginPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/{guid}/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/{vault-name}"
              },
              "secretName": "examplesecret"
            }
        },
        "collation": {
            "value": "SQL_Latin1_General_CP1_CI_AS"
        },
        "databaseName": {
            "value": "exampledb"
        },
        "edition": {
            "value": "Standard"
        },
        "location": {
            "value": "southcentralus"
        },
        "maxSizeBytes": {
            "value": "268435456000"
        },
        "requestedServiceObjectiveId": {
            "value": "455330e1-00cd-488b-b5fa-177c226f28b7"
        },
        "sampleName": {
            "value": ""
        },
        "serverName": {
            "value": "exampleserver"
        }
    }
}
```

## <a name="reference-a-secret-with-dynamic-id"></a>Referenciar um segredo com o dynamic ID

A secção anterior mostrou como transmitir um ID de recurso estático para o segredo do Cofre de chaves. No entanto, em alguns cenários, tem de referenciar um segredo do Cofre de chaves que varia com base na implementação atual. Nesse caso, não é possível codificar o ID de recurso no ficheiro de parâmetros. Infelizmente, dinamicamente não é possível gerar o ID de recurso no ficheiro de parâmetros porque expressões de modelo não são permitidas no ficheiro de parâmetros.

Para gerar dinamicamente o ID de recurso para um segredo do Cofre de chaves, tem de mover os recursos que tem o segredo a um modelo de aninhada. No modelo principal, adicionar o modelo de aninhado e passar um parâmetro que contém o ID de recurso gerado dinamicamente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>Passos seguintes
* Para obter informações gerais sobre cofres de chaves, consulte [introdução ao Cofre de chaves do Azure](../key-vault/key-vault-get-started.md).
* Para obter exemplos completos de referenciar chaves segredos, consulte [exemplos do Cofre de chaves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).


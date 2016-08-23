<properties
    pageTitle="Personalizar um modelo exportado do Resource Manager | Microsoft Azure"
    description="Adicione parâmetros a um modelo exportado do Azure Resource Manager e volte a implementá-lo através do Azure PowerShell ou da CLI do Azure."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/01/2016"
    ms.author="tomfitz"/>

# Personalizar um modelo exportado do Azure Resource Manager

Este artigo mostra como modificar um modelo exportado para que possa passar valores adicionais como parâmetros. Apesar de ter como base os passos executados no artigo [Exportar um modelo do Resource Manager](resource-manager-export-template.md), não é essencial que conclua primeiro esse artigo. Pode encontrar o modelo e os scripts necessários neste artigo.

## Ver um modelo exportado

Se tiver concluído os passos do artigo [Exportar um modelo do Resource Manager](resource-manager-export-template.md), abra o modelo que transferiu. O modelo é denominado **template.json**. Se tiver o Visual Studio ou o Visual Code, pode utilizar um deles para editar o modelo. Caso contrário, pode utilizar qualquer editor de JSON ou editor de texto.

Se não tiver concluído as instruções anteriores, crie um ficheiro denominado **template.json** e adicione o seguinte conteúdo do modelo exportado ao ficheiro.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

O modelo template.json é apropriado se pretender criar o mesmo tipo de conta do Storage na mesma região com uma rede virtual que utiliza o mesmo prefixo de endereço e o mesmo prefixo de sub-rede para todas as implementações. No entanto, o Resource Manager fornece opções para que possa implementar modelos com uma flexibilidade muito maior. Por exemplo, durante a implementação, pode pretender especificar o tipo de conta do Storage a criar ou os valores a utilizar para o prefixo do endereço de rede virtual e o prefixo de sub-rede.

## Personalizar o modelo

Nesta secção, irá adicionar parâmetros ao modelo exportado para que possa reutilizar o modelo ao implementar estes recursos noutros ambientes. Também irá adicionar algumas funcionalidades ao modelo para diminuir a probabilidade de encontrar um erro ao implementar o modelo. Já não terá de adivinhar um nome exclusivo para a sua conta do Storage. Em vez disso, o modelo irá criar um nome exclusivo. Irá restringir os valores que podem ser especificados para o tipo de conta do Storage a apenas opções válidas.

1. Para poder passar os valores que pode pretender especificar durante a implementação, substitua a secção **parameters** pelas seguintes definições de parâmetros. Repare nos valores de **allowedValues** para **storageAccount_accountType**. Se fornecer acidentalmente um valor inválido, esse erro é reconhecido antes do início da implementação. Além disso, tenha em atenção que está a fornecer apenas um prefixo para o nome da conta do Storage e o prefixo está limitado a 11 carateres. Ao limitar o prefixo a 11 carateres, assegura que o nome completo não irá exceder o número máximo de carateres para uma conta do Storage. O prefixo permite aplicar uma convenção de nomenclatura às contas do Storage. Poderá ver como criar um nome exclusivo no próximo passo.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. A secção **variables** do seu modelo está atualmente vazia. Substitua esta secção pelo código seguinte. Na secção **variables**, na qualidade de autor do modelo, pode criar valores que simplificam a sintaxe para o resto do seu modelo. A variável **storageAccount_name** concatena o prefixo do parâmetro a uma cadeia exclusiva que é gerada com base no identificador do grupo de recursos.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Para utilizar os parâmetros e a variável nas definições de recursos, substitua a secção **resources** pelas seguintes definições. Repare que, na verdade, muito pouco mudou nas definições de recursos, além do valor que é atribuído à propriedade de recursos. As propriedades são exatamente as mesmas do modelo exportado. Está simplesmente a atribuir propriedades a valores de parâmetros em vez de valores impostos. A localização dos recursos está definida para utilizar a mesma localização que o grupo de recursos através da expressão **resourceGroup().location**. A variável que criou para o nome da conta do Storage é referenciada através da expressão **variables**.

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## Corrigir o ficheiro de parâmetros

O ficheiro de parâmetros transferido já não corresponde aos parâmetros do seu modelo. Não é obrigatório utilizar um ficheiro de parâmetros, mas tal pode simplificar o processo quando volta a implementar um ambiente. Irá utilizar os valores predefinidos que estão definidos no modelo para muitos dos parâmetros, de modo a que o ficheiro de parâmetros apenas necessite de dois valores.

Substitua o conteúdo do ficheiro parameters.json por:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

O ficheiro de parâmetros atualizado fornece valores apenas para os parâmetros que não tenham um valor predefinido. Pode fornecer valores para os outros parâmetros quando pretender utilizar um valor que seja diferente do valor predefinido.

## Implementar o modelo

Pode utilizar o Azure PowerShell ou a interface de linha de comandos do Azure (CLI) para implementar o modelo personalizado e os ficheiros de parâmetros. Se necessário, instale o [Azure PowerShell](powershell-install-configure.md) ou a [CLI do Azure](xplat-cli-install.md). Pode utilizar os scripts que transferiu com o modelo quando exportou o modelo original ou pode escrever o seu próprio script para implementar o modelo.
Ambas as opções são apresentadas neste artigo.

2. Para implementar utilizando o seu próprio script, utilize um dos seguintes procedimentos.

     Para o PowerShell, execute:

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     Para a CLI do Azure, execute:

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. Se tiver transferido o modelo e os scripts exportados, localize o ficheiro **deploy.ps1** (para o PowerShell) ou o ficheiro **deploy.sh** (para a CLI do Azure).

     Para o PowerShell, execute:

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     Para a CLI do Azure, execute:

        .\deploy.sh

## Passos seguintes

- As [Instruções do Modelo do Resource Manager](resource-manager-template-walkthrough.md) têm como base o que aprendeu neste artigo ao criar um modelo para uma solução mais complicada. Ajudam-no a compreender melhor os recursos que estão disponíveis e como determinar os valores a fornecer.
- Para ver como exportar um modelo através do PowerShell, consulte [Utilizar o Azure PowerShell com o Azure Resource Manager](powershell-azure-resource-manager.md).
- Para ver como exportar um modelo através da CLI do Azure, consulte [Utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](xplat-cli-azure-resource-manager.md).
- Para saber mais sobre como são estruturados modelos, consulte [Criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).



<!--HONumber=Aug16_HO1-->



---
title: "Implementar um modelo Azure Resource Manager de um runbook de automatização do Azure | Microsoft Docs"
description: Como implementar um modelo Azure Resource Manager armazenado no Storage do Azure a partir de um runbook
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "json do runbook do PowerShell, automatização do azure"
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 07/09/2017
ms.author: eslesar
ms.openlocfilehash: e511eee2f9eac3969b15ad3d45558dc7034f330a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Implementar um modelo Azure Resource Manager num runbook do PowerShell de automatização do Azure

Pode escrever um [runbook do PowerShell de automatização do Azure](automation-first-runbook-textual-powershell.md) que implementa um recurso do Azure utilizando um [modelo de gestão de recursos do Azure](../azure-resource-manager/resource-manager-create-first-template.md).

Ao fazê-lo, pode automatizar a implementação de recursos do Azure. Pode manter os modelos do Resource Manager numa localização central, segura, como o armazenamento do Azure.

Neste tópico, vamos criar um runbook do PowerShell que utiliza um modelo do Resource Manager armazenado no [Storage do Azure](../storage/common/storage-introduction.md) para implementar uma nova conta de armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[inscrever-se numa conta gratuita](https://azure.microsoft.com/free/).
* [Conta de automatização](automation-sec-configure-azure-runas-account.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) onde pretende armazenar o modelo do Resource Manager
* O Azure Powershell instalado num computador local. Consulte [instalar e configurar o Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) para obter informações sobre como obter o Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

Neste exemplo, vamos utilizar um modelo do Resource Manager que implementa uma nova conta de armazenamento do Azure.

Num editor de texto, copie o seguinte texto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Guarde o ficheiro localmente como `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Guardar o modelo do Resource Manager no armazenamento do Azure

Agora iremos utilizar o PowerShell para criar uma partilha de ficheiros de armazenamento do Azure e carregar o `TemplateTest.json` ficheiro.
Para obter instruções sobre como criar um ficheiro de partilharem e carregar um ficheiro no portal do Azure, consulte [introdução ao File storage do Azure no Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Inicie o PowerShell no seu computador local e execute os seguintes comandos para criar uma partilha de ficheiros e carregar o modelo do Resource Manager para que a partilha de ficheiros.

```powershell
# Login to Azure
Login-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Criar o script de runbook do PowerShell

Agora vamos criar um script do PowerShell que obtém a `TemplateTest.json` file Storage do Azure e implementa o modelo para criar uma nova conta de armazenamento do Azure.

Num editor de texto, cole o seguinte texto:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Guarde o ficheiro localmente como `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importar e publicar o runbook na sua conta de automatização do Azure

Agora iremos utilizar o PowerShell para importar o runbook para a sua conta de automatização do Azure e, em seguida, publicar o runbook.
Para obter informações sobre como importar e publicar um runbook no portal do Azure, consulte [criar ou importar um runbook na automatização do Azure](automation-creating-importing-runbook.md).

Para importar `DeployTemplate.ps1` na sua conta de automatização como um runbook do PowerShell, execute os seguintes comandos do PowerShell:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Iniciar o runbook

Agora vamos iniciar o runbook ao chamar o [início AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0) cmdlet.

Para obter informações sobre como iniciar um runbook no portal do Azure, consulte [iniciar um runbook na automatização do Azure](automation-starting-a-runbook.md).

Execute os comandos seguintes na consola do PowerShell:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

O runbook é executado, e verifique o respetivo estado, executando `$job.Status`.

O runbook obtém o modelo do Resource Manager e utiliza-o para implementar uma nova conta de armazenamento do Azure.
Pode ver que a nova conta de armazenamento criada executando o seguinte comando:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Resumo

Já está! Agora pode utilizar modelos de automatização do Azure e o armazenamento do Azure e o Resource Manager para implementar todos os seus recursos do Azure.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre modelos do Resource Manager, consulte o artigo [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* Para começar a utilizar com o Storage do Azure, consulte o artigo [introdução ao Storage do Azure](../storage/common/storage-introduction.md).
* Para localizar outros útil runbooks de automatização do Azure, consulte [galleries módulos e Runbooks de automatização do Azure](automation-runbook-gallery.md).
* Para localizar outros útil modelos do Resource Manager, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/)


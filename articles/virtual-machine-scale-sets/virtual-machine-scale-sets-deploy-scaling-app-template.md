---
title: "Implementar uma aplicação num conjunto de dimensionamento de máquinas virtuais do Azure | Documentos Microsoft"
description: "Saiba como implementar uma aplicação de dimensionamento automático simples numa escala de máquina virtual definida, usando um modelo do Azure Resource Manager."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: ryanwi
ms.openlocfilehash: 07883a33382cc660b043c99872312a9e77228253
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-autoscaling-app-using-a-template"></a>Implementar uma aplicação de dimensionamento automático através de um modelo

Os [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) são uma ótima maneira de implementar grupos de recursos relacionados. Este tutorial baseia-se na [implementação de um conjunto de escala simples](virtual-machine-scale-sets-mvss-start.md) e descreve como implementar uma aplicação de dimensionamento automático simples numa escala definida usando um modelo do Azure Resource Manager.  Também pode configurar dimensionamento automático com o PowerShell, CLI ou o portal. Para obter mais informações, consulte [Descrição geral do dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md).

## <a name="two-quickstart-templates"></a>Dois modelos de início rápido
Quando implementa um conjunto de dimensionamento, instala o novo software numa plataforma de imagens que usa uma [extensão de VM](../virtual-machines/virtual-machines-windows-extensions-features.md). Uma extensão de VM é uma pequena aplicação que fornece tarefas de configuração e automação de pós-implementação em máquinas virtuais do Azure, tais como implementar uma aplicação. Dois modelos de exemplo diferentes são fornecidos nos [modelos Azure/azure-quickstart](https://github.com/Azure/azure-quickstart-templates) que mostram como implementar uma aplicação de dimensionamento automático numa escala definida usando extensões de VM.

### <a name="python-http-server-on-linux"></a>Servidor de HTTP do Python no Linux
O modelo de exemplo do [servidor HTTP do Python no Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) implementa uma aplicação de dimensionamento automático simples em execução num conjunto de dimensionamento do Linux.  [Bottle](http://bottlepy.org/docs/dev/), uma estrutura da Web do Python e um servidor HTTP simples são implementados em cada VM à escala definida através de um script personalizado de extensão de VM. O conjunto de dimensionamento é aumentado verticalmente quando a utilização média da CPU em todas as VMs é superior a 60% e é reduzido verticalmente quando a utilização média da CPU é inferior a 30%.

Para além do recurso do conjunto de dimensionamento, o modelo de exemplo *azuredeploy.json* também declara a rede virtual, o endereço IP público, o balanceador de carga e os recursos das definições de dimensionamento automático.  Para obter mais informações sobre como criar esses recursos num modelo, consulte [Conjunto de dimensionamento do Linux escala automática](virtual-machine-scale-sets-linux-autoscale.md).

No modelo *azuredeploy.json*, `extensionProfile` a propriedade do `Microsoft.Compute/virtualMachineScaleSets` recurso especifica uma extensão de script personalizada. `fileUris` especifica o local do(s) script(s). Neste caso, dois ficheiros: *workserver.py* que define um servidor HTTP simples, e *installserver.sh* que instala o Bottle e inicia o servidor HTTP. `commandToExecute` especifica o comando a ser executado após a implementação do conjunto de dimensionamento.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>Aplicação ASP.NET MVC no Windows
O modelo de exemplo da [aplicação ASP.NET MVC no Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) implementa uma aplicação ASP.NET MVC simples em execução no IIS no conjunto de dimensionamento do Windows.  O IIS e a aplicação MVC são implementados usando a extensão da VM [Configuração de estado desejado do PowerShell (DSC)](virtual-machine-scale-sets-dsc.md).  O conjunto de dimensionamento aumenta (na instância da VM de uma só vez) quando a utilização da CPU for superior a 50% durante 5 minutos. 

Para além do recurso do conjunto de dimensionamento, o modelo de exemplo *azuredeploy.json* também declara a rede virtual, o endereço IP público, o balanceador de carga e os recursos das definições de dimensionamento automático. Este modelo também demonstra a atualização da aplicação.  Para obter mais informações sobre como criar esses recursos num modelo, consulte [Conjunto de dimensionamento do Windows com escala automática](virtual-machine-scale-sets-windows-autoscale.md).

No modelo *azuredeploy.json*, a `extensionProfile` propriedade do `Microsoft.Compute/virtualMachineScaleSets` recurso especifica uma extensão da [configuração de estado desejada (DSC)](virtual-machine-scale-sets-dsc.md) que instala o IIS e uma aplicação Web predefinida de um pacote WebDeploy.  O script *IISInstall.ps1* instala o IIS na máquina virtual e está localizado na pasta *DSC*.  A aplicação Web MVC está localizada na pasta *WebDeploy*.  Os caminhos para o script de instalação e para a aplicação Web são definidos nos parâmetros `powershelldscZip` e `webDeployPackage` no ficheiro *azuredeploy.parameters.json*. 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>Implementar o modelo
A forma mais simples de implementação do modelo do [servidor HTTP Python no Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) ou da [aplicação ASP.NET MVC no Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) é a utilização do botão **Implementar no Azure** localizado nos ficheiros Leiame no GitHub.  Também pode utilizar o PowerShell ou o Azure CLI para implementar os modelos de exemplo.

### <a name="powershell"></a>PowerShell
Copie os ficheiros [Servidor HTTP Python no Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) ou [Aplicação ASP.NET MVC no Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) do repositório GitHub para uma pasta no computador local.  Abra o ficheiro *azuredeploy.parameters.json* e atualizar os valores predefinidos dos parâmetros `vmssName`, `adminUsername` e `adminPassword`. Guarde o seguinte script do PowerShell para *deploy.ps1* na pasta de exemplo como o modelo *azuredeploy.json*. Para implementar o modelo de exemplo, execute o script *deploy.ps1* a partir de uma janela de comando do PowerShell.

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.compute","microsoft.insights","microsoft.network");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

### <a name="azure-cli"></a>CLI do Azure
```azurecli
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'

# -e: immediately exit if any command has a non-zero exit status
# -o: prevents errors in a pipeline from being masked
# IFS new value is less likely to cause confusing bugs when looping arrays or arguments (e.g. $@)

usage() { echo "Usage: $0 -i <subscriptionId> -g <resourceGroupName> -n <deploymentName> -l <resourceGroupLocation>" 1>&2; exit 1; }

declare subscriptionId=""
declare resourceGroupName=""
declare deploymentName=""
declare resourceGroupLocation=""

# Initialize parameters specified from command line
while getopts ":i:g:n:l:" arg; do
    case "${arg}" in
        i)
            subscriptionId=${OPTARG}
            ;;
        g)
            resourceGroupName=${OPTARG}
            ;;
        n)
            deploymentName=${OPTARG}
            ;;
        l)
            resourceGroupLocation=${OPTARG}
            ;;
        esac
done
shift $((OPTIND-1))

#Prompt for parameters is some required parameters are missing
if [[ -z "$subscriptionId" ]]; then
    echo "Subscription Id:"
    read subscriptionId
    [[ "${subscriptionId:?}" ]]
fi

if [[ -z "$resourceGroupName" ]]; then
    echo "ResourceGroupName:"
    read resourceGroupName
    [[ "${resourceGroupName:?}" ]]
fi

if [[ -z "$deploymentName" ]]; then
    echo "DeploymentName:"
    read deploymentName
fi

if [[ -z "$resourceGroupLocation" ]]; then
    echo "Enter a location below to create a new resource group else skip this"
    echo "ResourceGroupLocation:"
    read resourceGroupLocation
fi

#templateFile Path - template file to be used
templateFilePath="template.json"

if [ ! -f "$templateFilePath" ]; then
    echo "$templateFilePath not found"
    exit 1
fi

#parameter file path
parametersFilePath="parameters.json"

if [ ! -f "$parametersFilePath" ]; then
    echo "$parametersFilePath not found"
    exit 1
fi

if [ -z "$subscriptionId" ] || [ -z "$resourceGroupName" ] || [ -z "$deploymentName" ]; then
    echo "Either one of subscriptionId, resourceGroupName, deploymentName is empty"
    usage
fi

#login to azure using your credentials
az account show 1> /dev/null

if [ $? != 0 ];
then
    az login
fi

#set the default subscription id
az account set --name $subscriptionId

set +e

#Check for existing RG
az group show $resourceGroupName 1> /dev/null

if [ $? != 0 ]; then
    echo "Resource group with name" $resourceGroupName "could not be found. Creating new resource group.."
    set -e
    (
        set -x
        az group create --name $resourceGroupName --location $resourceGroupLocation 1> /dev/null
    )
    else
    echo "Using existing resource group..."
fi

#Start deployment
echo "Starting deployment..."
(
    set -x
    az group deployment create --name $deploymentName --resource-group $resourceGroupName --template-file $templateFilePath --parameters $parametersFilePath
)

if [ $?  == 0 ];
 then
    echo "Template has been successfully deployed"
fi
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

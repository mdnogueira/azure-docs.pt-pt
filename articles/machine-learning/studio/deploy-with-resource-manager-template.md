---
title: "Implementar uma área de trabalho do Machine Learning com o Azure Resource Manager | Microsoft Docs"
description: "Como implementar uma área de trabalho do Azure Machine Learning utilizando o modelo Azure Resource Manager"
services: machine-learning
documentationcenter: 
author: ahgyger
manager: haining
editor: garye
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/15/2017
ms.author: ahgyger
ms.openlocfilehash: e3cbcb8118aa05e554b2493506280d0e24706059
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Implementar uma Área de Trabalho do Machine Learning com o Azure Resource Manager
## <a name="introduction"></a>Introdução
Utilizar um modelo de implementação poupa tempo, concedendo uma forma escalável do Azure Resource Manager implemente interligados componentes com uma validação e repita o mecanismo de. Para configurar a áreas de trabalho do Azure Machine Learning, por exemplo, terá primeiro de configurar uma conta de armazenamento do Azure e, em seguida, implementar a sua área de trabalho. Imagine fazê-lo manualmente para centenas de áreas de trabalho. É uma alternativa mais fácil para utilizar um modelo Azure Resource Manager para implementar uma área de trabalho do Azure Machine Learning e as respetivas dependências. Este artigo orienta-este passo a passo do processo. Para uma excelente descrição geral do Gestor de recursos do Azure, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Passo a passo: criar uma área de trabalho do Machine Learning
Iremos irá criar um grupo de recursos do Azure, em seguida, implementar uma nova conta de armazenamento do Azure e um novo Azure Machine Learning área de trabalho com um modelo do Resource Manager. Assim que a implementação estiver concluída, iremos irá imprimir informações importantes sobre as áreas de trabalho que foram criadas (a chave primária, o workspaceID e o URL para a área de trabalho).

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo Azure Resource Manager
Uma área de trabalho do Machine Learning requer uma conta de armazenamento do Azure para armazenar o conjunto de dados ligado ao mesmo.
O modelo seguinte utiliza o nome do grupo de recursos para gerar o nome da conta de armazenamento e o nome da área de trabalho.  Também utiliza o nome da conta de armazenamento como uma propriedade ao criar a área de trabalho.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Guarde este modelo como ficheiro mlworkspace.json em c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implementar o grupo de recursos, com base no modelo
* Abra PowerShell
* Instalar módulos para o Azure Resource Manager e gestão de serviço do Azure  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Estes passos transferir e instalar os módulos necessários para concluir os passos restantes. Isto só deverá ser efetuada uma vez num ambiente onde estão a executar os comandos do PowerShell.   

* Autenticar para o Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Este passo tem de ser repetido para cada sessão. Uma vez autenticado, deverá ser apresentadas informações da sua subscrição.

![Conta do Azure][1]

Agora que temos o acesso ao Azure, podemos criar o grupo de recursos.

* Criar um grupo de recursos

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Certifique-se de que o grupo de recursos é corretamente aprovisionado. **ProvisioningState** deve ser "foi concluída com êxito."
O nome do grupo de recursos é utilizado pelo modelo para gerar o nome da conta de armazenamento. O nome da conta de armazenamento tem de ter entre 3 e 24 carateres de comprimento e utilizar apenas letras minúsculas e números.

![Grupo de Recursos][2]

* Utilizar a implementação do grupo de recursos, implemente uma nova área de trabalho do Machine Learning.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Depois de concluída a implementação, é fácil de propriedades de acesso da área de trabalho que implementou. Por exemplo, pode aceder ao Token de chave primária.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Outra forma de obter os tokens de área de trabalho existente é utilizar o comando Invoke-AzureRmResourceAction. Por exemplo, pode listar os tokens primários e secundários de todas as áreas de trabalho.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Depois da área de trabalho é aprovisionada, também pode automatizar muitas tarefas do Azure Machine Learning Studio utilizando o [módulo do PowerShell do Azure Machine Learning](http://aka.ms/amlps).

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [criação de modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Veja o [repositório de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates). 
* Veja este vídeo sobre [do Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->

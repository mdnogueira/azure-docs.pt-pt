---
title: "Automatizar a implementação de recursos para uma aplicação de função das funções do Azure | Microsoft Docs"
description: "Saiba como criar um modelo Azure Resource Manager que implementa a aplicação de função."
services: Functions
documtationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure funções, funções, arquitetura sem servidor, infraestrutura como o Gestor de recursos de código, do azure"
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: e6b3deb9353ba07d693d71822d37a1761dd70d67
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implementação de recursos para a sua aplicação de função das funções do Azure

Pode utilizar um modelo Azure Resource Manager para implementar uma aplicação de função. Este artigo descreve os recursos necessários e parâmetros para fazê-lo. Poderá ter de implementar recursos adicionais, consoante o [acionadores e enlaces](functions-triggers-bindings.md) na sua aplicação de função.

Para obter mais informações sobre a criação de modelos, consulte [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para modelos de exemplo, consulte:
- [aplicação de função no plano de consumo]
- [aplicação de função no plano do App Service do Azure]

## <a name="required-resources"></a>Recursos necessários

Uma aplicação de função requer estes recursos:

* Um [Storage do Azure](../storage/index.yml) conta
* Um plano de alojamento (plano do App Service ou plano de consumo)
* Uma aplicação de função 

### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é necessária para uma aplicação de função. Necessita de uma conta de objetivo geral que suporta blobs, tabelas, filas e os ficheiros. Para obter mais informações, consulte [requisitos de conta de armazenamento das funções do Azure](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Além disso, as propriedades `AzureWebJobsStorage` e `AzureWebJobsDashboard` tem de ser especificado como definições de aplicação na configuração do site. O tempo de execução das funções do Azure utiliza o `AzureWebJobsStorage` cadeia de ligação para criar filas internas. A cadeia de ligação `AzureWebJobsDashboard` é utilizado para iniciar sessão Table storage do Azure e a desativação de **Monitor** separador no portal.

Estas propriedades são especificadas no `appSettings` coleção no `siteConfig` objeto:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>Plano de alojamento

A definição do plano de alojamento varia consoante utilize ou não um plano de consumo ou o serviço de aplicações. Consulte [implementar uma aplicação de função no plano de consumo](#consumption) e [implementar uma aplicação de função no plano do App Service](#app-service-plan).

### <a name="function-app"></a>Aplicação de função

O recurso de aplicação de função é definido através da utilização de um recurso do tipo **Microsoft.Web/Site** e tipo **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Implementar uma aplicação de função no plano de consumo

Pode executar uma aplicação de função em dois modos diferentes: plano de consumo e o plano de serviço de aplicações. O plano de consumo atribui automaticamente a capacidade de computação quando o código está em execução, aumenta horizontalmente de forma conforme necessário para processar carga e, em seguida, dimensiona quando o código não está em execução. Por isso, não tem de pagar VMs Inativas, e não tiver a capacidade de reserva antecipadamente. Para saber mais sobre planos de alojamento, consulte o artigo [planos de consumo de funções do Azure e do serviço de aplicações](functions-scale.md).

Para um modelo de Gestor de recursos do Azure de exemplo, consulte [aplicação de função no plano de consumo].

### <a name="create-a-consumption-plan"></a>Criar um plano de consumo

Um plano de consumo é um tipo especial de "serverfarm" recursos. Especificou utilizando o `Dynamic` valor para o `computeMode` e `sku` propriedades:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

### <a name="create-a-function-app"></a>Criar uma aplicação de função

Além disso, um plano de consumo requer duas definições adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Estas propriedades de configurar o caminho de ficheiro e a conta de armazenamento onde o código de aplicação de função e configuração são armazenados.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Implementar uma aplicação de função no plano de serviço de aplicações

O plano de serviço de aplicações, a aplicação de função é executada em VMs dedicadas em básicas, Standard e Premium SKUs, semelhante às web apps. Para obter mais informações sobre como funciona o plano do App Service, consulte o [descrição geral dos planos do App Service do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Para um modelo de Gestor de recursos do Azure de exemplo, consulte [aplicação de função no plano do App Service do Azure].

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Criar uma aplicação de função 

Depois de selecionar uma opção de dimensionamento, crie uma aplicação de função. A aplicação é o contentor que retém todas as suas funções.

Uma aplicação de função tem muitos recursos subordinados que pode utilizar na sua implementação, incluindo as definições de aplicação e as opções de controlo de origem. Também pode optar por remover o **sourcecontrols** recursos subordinados e utilize outro [a opção de implementação](functions-continuous-deployment.md) em vez disso.

> [!IMPORTANT]
> Para implementar com êxito a aplicação utilizando o Gestor de recursos do Azure, é importante compreender a forma como os recursos são implementados no Azure. No exemplo seguinte, as configurações de nível superior são aplicadas utilizando **siteConfig**. É importante definir estas configurações num nível superior, porque estes transmitir informações para o motor de execução e a implementação de funções. São necessárias informações de nível superior antes do subordinado **sourcecontrols/web** recurso é aplicado. Embora seja possível configurar estas definições no nível subordinado **configuração/appSettings** recurso, em alguns casos, a aplicação de função tem de ser implementada *antes* **configuração/appSettings**  é aplicada. Por exemplo, quando estiver a utilizar as funções com [Logic Apps](../logic-apps/index.md), as suas funções são uma dependência de outro recurso.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Este modelo utiliza a [projeto](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) o valor de definições de aplicação, que define o diretório de base na qual o motor de implementação de funções (Kudu) procura código implementável. No nosso repositório nosso funções estão numa subpasta do **src** pasta. Por isso, no exemplo anterior, iremos definir o valor de definições de aplicação `src`. Se as suas funções estão na raiz do repositório, ou se não estiver a implementar do controlo de origem, pode remover este valor de definições de aplicação.

## <a name="deploy-your-template"></a>Implementar o modelo

Pode utilizar qualquer uma das seguintes formas para implementar o modelo:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Implementar para botão do Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` com um [com codificação URL](https://www.bing.com/search?q=url+encode) versão do caminho não processado do seu `azuredeploy.json` ficheiro no GitHub.

Eis um exemplo que utiliza o markdown:

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Eis um exemplo que utiliza HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como desenvolver e configurar as funções do Azure.

* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Como configurar as definições de aplicação de função do Azure](functions-how-to-use-azure-function-app-settings.md)
* [Criar a sua primeira função do Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[aplicação de função no plano de consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[aplicação de função no plano do App Service do Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

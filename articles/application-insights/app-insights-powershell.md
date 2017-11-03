---
title: "Automatizar Insights da aplicação do Azure com o PowerShell | Microsoft Docs"
description: Automatizar criar testes de recursos, o alerta e disponibilidade no PowerShell, utilizando um modelo Azure Resource Manager.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: f4f9d1558d2ef9dc5e1b7b248ad5bc8753f59cf9
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
#  <a name="create-application-insights-resources-using-powershell"></a>Criar recursos do Application Insights com o PowerShell
Este artigo mostra como automatizar a criação e a atualização de [Application Insights](app-insights-overview.md) recursos automaticamente utilizando a gestão de recursos do Azure. Fazê-lo pode, por exemplo, como parte de um processo de compilação. Juntamente com o recurso do Application Insights básico, pode criar [testes web de disponibilidade](app-insights-monitor-web-app-availability.md), configure [alertas](app-insights-alerts.md), defina o [preços esquema](app-insights-pricing.md)e criar outros recursos do Azure.

A chave para a criação destes recursos é modelos JSON para [do Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md). Um nutshell, o procedimento é: transferir as definições de JSON dos recursos existentes; parametrizar determinados valores, tais como nomes; e, em seguida, execute o modelo sempre que pretender criar um novo recurso. Pode incluir vários recursos em conjunto, para criá-los todos de uma aceda - por exemplo, um monitor de aplicação com os testes de disponibilidade, alertas e armazenamento para a exportação contínua. Existem algumas subtleties para algumas das parameterizations, o qual vamos explicar aqui.

## <a name="one-time-setup"></a>Configuração de uso individual
Se ainda não utilizou o PowerShell com a sua subscrição do Azure antes de:

Instale o módulo Azure Powershell no computador onde pretende executar os scripts:

1. Instalar [instalador de plataforma Web da Microsoft (v5 ou superior)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Utilizá-la para instalar o Microsoft Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Criar um modelo Azure Resource Manager
Criar um novo ficheiro. JSON - vamos chamá-lo `template1.json` neste exemplo. Copie este conteúdo para a mesma:

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "HockeyAppBridge",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Basic, 2 = Enterprise"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Criar recursos do Application Insights
1. No PowerShell, inicie sessão no Azure:
   
    `Login-AzureRmAccount`
2. Execute um comando como esta:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`é o grupo de onde pretende criar novos recursos.
   * `-TemplateFile`tem de ocorrer antes dos parâmetros personalizados.
   * `-appName`O nome do recurso para criar.

Pode adicionar outros parâmetros - encontrará as suas descrições, na secção de parâmetros do modelo.

## <a name="to-get-the-instrumentation-key"></a>Para obter a chave de instrumentação
Depois de criar um recurso de aplicação, poderá ser útil a chave de instrumentação: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Definir o plano de preços

Pode definir o [plano de preços](app-insights-pricing.md).

Para criar um recurso de aplicação com o plano de preços do Enterprise, utilizando o modelo acima:

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|Planear|
|---|---|
|1|Básica|
|2|Enterprise|

* Se apenas pretender utilizar o plano de preços básico predefinido, pode omitir o recurso de CurrentBillingFeatures do modelo.
* Se pretender alterar o plano de preços depois de criar o recurso de componente, pode utilizar um modelo que omite o recurso "microsoft.insights/components". Além disso, omita o `dependsOn` nós do recurso de faturação. 

Para verificar o plano de preços atualizada, observe o "Funcionalidades + preços" painel no browser. **Atualize a vista de browser** para se certificar de que está a ver o estado mais recente.



## <a name="add-a-metric-alert"></a>Adicionar um alerta de métrico

Para configurar um alerta de métrico ao mesmo tempo, como o recurso de aplicação, intercale o código seguinte no ficheiro de modelo:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Quando invoca o modelo, opcionalmente, pode adicionar este parâmetro:

    `-responseTime 2`

Obviamente pode parametrizar outros campos. 

Para saber os detalhes de configuração de outras regras de alertas e os nomes de tipo, crie manualmente uma regra e, em seguida, analisá-lo no [do Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Adicionar um teste de disponibilidade

Neste exemplo é um teste de ping (testar uma única página).  

**Existem duas partes** num teste de disponibilidade: o teste de si próprio e o alerta que o notifica de falhas.

Intercale o código seguinte no ficheiro de modelo que cria a aplicação.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Para detetar os códigos para outras localizações de teste, ou para automatizar a criação de mais complexas testes web, crie um exemplo manualmente e, em seguida, parametrizar o código de [do Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Adicione mais recursos

Para automatizar a criação de quaisquer outros recursos de qualquer tipo, criar um exemplo manualmente e, em seguida, copiar e parametrizar o código de [do Azure Resource Manager](https://resources.azure.com/). 

1. Abra [o Azure Resource Manager](https://resources.azure.com/). Navegue para baixo através de `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, para o recurso de aplicação. 
   
    ![Navegação no Explorador de recursos do Azure](./media/app-insights-powershell/01.png)
   
    *Componentes* são os recursos do Application Insights básicos para apresentar as aplicações. Não existem recursos separados para as regras de alerta associadas e testes web de disponibilidade.
2. Copie o JSON do componente para o local adequado no `template1.json`.
3. Elimine estas propriedades:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Abra as secções webtests e alertrules e copie o JSON para itens individuais no seu modelo. (Não copiar a partir de nós webtests ou alertrules: vá para os itens abaixo deles.)
   
    Cada teste web tem uma regra de alerta associada, pelo que terá de copiar ambos os parâmetros.
   
    Também pode incluir alertas nas métricas. [Os nomes de métricos](app-insights-powershell-alerts.md#metric-names).
5. Esta linha de inserção na cada recurso:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizar o modelo
Agora, tem de substituir os nomes específicos com parâmetros. Para [parametrizar um modelo](../azure-resource-manager/resource-group-authoring-templates.md), escreve expressões com um [conjunto de funções de programa auxiliar](../azure-resource-manager/resource-group-template-functions.md). 

Não é possível parametrizar apenas parte de uma cadeia, por isso, utilize `concat()` para criar cadeias.

Seguem-se exemplos de substituições que pretende efetuar. Existem várias ocorrências de cada substituição. Poderá ter outros utilizadores no seu modelo. Estes exemplos utilizam os parâmetros e variáveis que definimos na parte superior do modelo.

| localizar | Substitua |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"`(minúsculas) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Eliminar Guid e o ID. |

### <a name="set-dependencies-between-the-resources"></a>Conjunto de dependências entre os recursos
Azure deve configurar os recursos na ordem rigorosa. Para se certificar antes da próxima começa a conclusão da configuração de um, adicione as linhas de dependência:

* No recurso de teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* No recurso de alerta para um teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Passos seguintes
Outros artigos de automatização:

* [Crie um recurso do Application Insights](app-insights-powershell-script-create-resource.md) -método rápido sem utilizar um modelo.
* [Configurar alertas](app-insights-powershell-alerts.md)
* [Criar testes web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Send Azure Diagnostics to Application Insights (Enviar o Diagnóstico do Azure para o Application Insights)](app-insights-powershell-azure-diagnostics.md)
* [Implementar no Azure a partir do GitHub](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Criar anotações de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)


---
title: Alojamento de alta densidade no App Service do Azure | Microsoft Docs
description: Alojamento de alta densidade no App Service do Azure
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: byvinyal
ms.openlocfilehash: e6595c9f49e3b6303ad96c37d4ee5ebea37ce829
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="high-density-hosting-on-azure-app-service"></a>Alojamento de alta densidade no App Service do Azure
Ao utilizar o serviço de aplicações, a aplicação é dissociada da capacidade alocada por dois conceitos:

* **A aplicação:** representa a aplicação e a respetiva configuração de tempo de execução. Por exemplo, inclui a versão do .NET que deve carregar o runtime, as definições da aplicação.
* **Plano do App Service:** define as características da capacidade, o conjunto de funcionalidades disponíveis e a Localidade da aplicação. Por exemplo, as características poderão máquina (quatro núcleos) grande, quatro instâncias, as funcionalidades Premium nos EUA Leste.

Uma aplicação está sempre ligada a um plano de serviço de aplicações, mas um plano de serviço aplicacional pode fornecer a capacidade para uma ou mais aplicações.

Como resultado, a plataforma disponibiliza a flexibilidade para isolar uma aplicação único ou tem várias aplicações partilhar recursos por partilhar um plano de serviço de aplicações.

No entanto, quando várias aplicações partilharem um plano do App Service, uma instância dessa aplicação é executada em todas as instâncias desse plano do App Service.

## <a name="per-app-scaling"></a>Por aplicações de dimensionamento
*Por aplicação dimensionamento* é uma funcionalidade que pode ser ativada ao nível do plano de serviço de aplicações e, em seguida, utilizada por aplicação.

Por aplicação dimensionamento dimensiona uma aplicação independentemente do plano de serviço de aplicações que aloja-lo. Desta forma, um plano do App Service pode ser escalado para 10 instâncias, mas uma aplicação pode ser definida para utilizar apenas cinco.

   >[!NOTE]
   >Por aplicação dimensionamento só está disponível para **Premium** planos SKU do App Service
   >

### <a name="per-app-scaling-using-powershell"></a>Por aplicação dimensionamento com o PowerShell

Pode criar um plano configurado como um *por aplicação dimensionamento* plano mediante a transmissão no ```-perSiteScaling $true``` atributo para o ```New-AzureRmAppServicePlan``` commandlet

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Se pretender atualizar um plano de serviço de aplicações existente para utilizar esta funcionalidade: 

- obter o plano de destino```Get-AzureRmAppServicePlan```
- modificar a propriedade localmente```$newASP.PerSiteScaling = $true```
- publicar as suas alterações para o azure```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Ao nível da aplicação, é necessário configurar o número de instâncias, que pode utilizar a aplicação no plano de serviço de aplicações.

No exemplo abaixo, a aplicação está limitada a duas instâncias, independentemente de quantas instâncias do plano do app service subjacente aumenta horizontalmente de forma a.

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp. SiteConfig.NumberOfWorkers é $newapp de forma diferente. MaxNumberOfWorkers. Por aplicação dimensionamento utiliza $newapp. SiteConfig.NumberOfWorkers para determinar as características de dimensionamento da aplicação.

### <a name="per-app-scaling-using-azure-resource-manager"></a>Por aplicação dimensionamento com o Azure Resource Manager

O seguinte *modelo Azure Resource Manager* cria:

- Um plano de serviço aplicacional ampliar a 10 instâncias
- uma aplicação que está configurada para dimensionar para um máximo de cinco instâncias.

O plano de serviço de aplicações é a definição de **PerSiteScaling** propriedade para verdadeiro ```"perSiteScaling": true```. A aplicação é a definição de **número de funcionários** a utilizar para 5 ```"properties": { "numberOfWorkers": "5" }```.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Configuração recomendada para o alojamento de alta densidade
Por aplicação da receção é uma funcionalidade que esteja ativada na global regiões do Azure e ambientes do App Service. No entanto, a estratégia de recomendada é utilizar ambientes do App Service para tirar partido das respetivas funcionalidades avançadas e os agrupamentos maiores da capacidade.  

Siga estes passos para configurar alta densidade de alojamento para as suas aplicações:

1. Configurar o ambiente de serviço de aplicações e escolha um conjunto de trabalho que se encontra dedicado para o cenário de alojamento de alta densidade.
1. Criar um único plano de serviço de aplicações e dimensione-a para utilizar a capacidade disponível no conjunto de trabalho.
1. Defina o sinalizador de PerSiteScaling como true no plano de serviço de aplicações.
1. Novas aplicações são criadas e atribuídas a essa plano de serviço de aplicações com o **numberOfWorkers** propriedade definida como **1**. Utilizando esta configuração gera a densidade mais elevada possível neste conjunto de trabalho.
1. O número de funcionários pode ser configurado de forma independente por aplicação para conceder recursos adicionais, conforme necessário. Por exemplo:
    - Pode definir uma aplicação de utilização é elevado **numberOfWorkers** para **3** com mais capacidade de processamento para essa aplicação. 
    - Baixa utilização aplicações iriam definir **numberOfWorkers** para **1**.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral dos planos do App Service do Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Introdução ao Ambiente do Serviço de Aplicações](app-service-app-service-environment-intro.md)
---
title: Fornecedores de recursos do Azure e os tipos de recursos | Microsoft Docs
description: "Descreve os fornecedores de recursos que suportam o Gestor de recursos, os respetivos esquemas e versões de API disponíveis e as regiões que podem alojar os recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 6a9128f45d4199404019cee594842d59c7f1aaf3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resource-providers-and-types"></a>Tipos e fornecedores de recursos

Quando implementar recursos, com frequência tem de obter informações sobre os fornecedores de recursos e os tipos. Neste artigo, saiba como:

* Ver todos os fornecedores de recursos no Azure
* Verifique o estado de registo de um fornecedor de recursos
* Registar um fornecedor de recursos
* Vista de tipos de recursos para um fornecedor de recursos
* Ver localizações válidas para um tipo de recurso
* Ver as versões de API válidas para um tipo de recurso

Pode executar estes passos através do portal, o PowerShell ou a CLI do Azure.

## <a name="powershell"></a>PowerShell

Para ver todos os fornecedores de recursos no Azure e o estado do registo para a sua subscrição, utilize:

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Que devolve resultados semelhantes aos:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registar um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. O âmbito de registo é sempre a subscrição. Por predefinição, muitos fornecedores de recursos são registados automaticamente. No entanto, terá de registar manualmente alguns fornecedores de recursos. Para registar um fornecedor de recursos, tem de ter permissão para efetuar o `/register/action` operação para o fornecedor de recursos. Esta operação está incluída nas funções de contribuinte e proprietário.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Que devolve resultados semelhantes aos:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Não é possível anular o registo de um fornecedor de recursos quando tem tipos de recursos desse fornecedor de recursos na sua subscrição.

Para ver informações de um fornecedor de recurso específico, utilize:

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Que devolve resultados semelhantes aos:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Para ver os tipos de recursos para um fornecedor de recursos, utilize:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Que devolve:

```powershell
batchAccounts
operations
locations
locations/quotas
```

A versão de API corresponde a uma versão das operações de REST API que são lançados pelo fornecedor de recursos. Como um fornecedor de recursos permite que as novas funcionalidades, versões uma nova versão da REST API. 

Para obter versões de API disponíveis para um tipo de recurso, utilize:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Que devolve:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Gestor de recursos é suportado em todas as regiões, mas os recursos que implementar poderão não ser suportados em todas as regiões. Além disso, pode ser limitações na sua subscrição que impedem que algumas regiões que suportam o recurso a utilizar. 

Para obter as localizações suportadas para um tipo de recurso, utilize.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Que devolve:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>CLI do Azure
Para ver todos os fornecedores de recursos no Azure e o estado do registo para a sua subscrição, utilize:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Que devolve resultados semelhantes aos:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Registar um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. O âmbito de registo é sempre a subscrição. Por predefinição, muitos fornecedores de recursos são registados automaticamente. No entanto, terá de registar manualmente alguns fornecedores de recursos. Para registar um fornecedor de recursos, tem de ter permissão para efetuar o `/register/action` operação para o fornecedor de recursos. Esta operação está incluída nas funções de contribuinte e proprietário.

```azurecli
az provider register --namespace Microsoft.Batch
```

Que devolve uma mensagem que registo está em curso.

Não é possível anular o registo de um fornecedor de recursos quando tem tipos de recursos desse fornecedor de recursos na sua subscrição.

Para ver informações de um fornecedor de recurso específico, utilize:

```azurecli
az provider show --namespace Microsoft.Batch
```

Que devolve resultados semelhantes aos:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Para ver os tipos de recursos para um fornecedor de recursos, utilize:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Que devolve:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

A versão de API corresponde a uma versão das operações de REST API que são lançados pelo fornecedor de recursos. Como um fornecedor de recursos permite que as novas funcionalidades, versões uma nova versão da REST API. 

Para obter versões de API disponíveis para um tipo de recurso, utilize:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Que devolve:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Gestor de recursos é suportado em todas as regiões, mas os recursos que implementar poderão não ser suportados em todas as regiões. Além disso, pode ser limitações na sua subscrição que impedem que algumas regiões que suportam o recurso a utilizar. 

Para obter as localizações suportadas para um tipo de recurso, utilize.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Que devolve:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="portal"></a>Portal

Para ver todos os fornecedores de recursos no Azure e o estado do registo para a sua subscrição, selecione **subscrições**.

![Selecione as subscrições](./media/resource-manager-supported-services/select-subscriptions.png)

Selecione a subscrição para ver.

![Especifique a subscrição](./media/resource-manager-supported-services/subscription.png)

Selecione **fornecedores de recursos** e ver a lista de fornecedores de recursos disponíveis.

![Mostrar os fornecedores de recursos](./media/resource-manager-supported-services/show-resource-providers.png)

Registar um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. O âmbito de registo é sempre a subscrição. Por predefinição, muitos fornecedores de recursos são registados automaticamente. No entanto, terá de registar manualmente alguns fornecedores de recursos. Para registar um fornecedor de recursos, tem de ter permissão para efetuar o `/register/action` operação para o fornecedor de recursos. Esta operação está incluída nas funções de contribuinte e proprietário. Para registar um fornecedor de recursos, selecione **registar**.

![registar o fornecedor de recursos](./media/resource-manager-supported-services/register-provider.png)

Não é possível anular o registo de um fornecedor de recursos quando tem tipos de recursos desse fornecedor de recursos na sua subscrição.

Para ver informações de um fornecedor de recurso específico, selecione **mais serviços**.

![Selecione mais serviços](./media/resource-manager-supported-services/more-services.png)

Procurar **Explorador de recursos** e selecione-a partir das opções disponíveis.

![Selecione o Explorador de recursos](./media/resource-manager-supported-services/select-resource-explorer.png)

Selecione **Fornecedores**.

![Selecionar fornecedores](./media/resource-manager-supported-services/select-providers.png)

Selecione o fornecedor de recursos e o tipo de recurso que pretende visualizar.

![Selecione o tipo de recurso](./media/resource-manager-supported-services/select-resource-type.png)

Gestor de recursos é suportado em todas as regiões, mas os recursos que implementar poderão não ser suportados em todas as regiões. Além disso, pode ser limitações na sua subscrição que impedem que algumas regiões que suportam o recurso a utilizar. O Explorador de recursos mostra as localizações válidas para o tipo de recurso.

![Mostrar localizações](./media/resource-manager-supported-services/show-locations.png)

A versão de API corresponde a uma versão das operações de REST API que são lançados pelo fornecedor de recursos. Como um fornecedor de recursos permite que as novas funcionalidades, versões uma nova versão da REST API. O Explorador de recursos mostra válidas versões de API para o tipo de recurso.

![Mostrar versões de API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como criar modelos do Resource Manager, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre a implementação de recursos, consulte [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).
* Para ver as operações de um fornecedor de recursos, consulte [API REST da Azure](/rest/api/).


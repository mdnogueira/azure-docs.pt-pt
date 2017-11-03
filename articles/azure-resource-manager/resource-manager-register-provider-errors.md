---
title: Erros de registo do fornecedor de recursos do Azure | Microsoft Docs
description: Descreve como resolver erros de registo do fornecedor de recursos do Azure.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: d6a99917e732a3439a31cafa5608348694014054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Resolva os erros para o registo do fornecedor de recursos

Este artigo descreve os erros que poderá encontrar ao utilizar um fornecedor de recursos que não tenha anteriormente utilizado na sua subscrição.

## <a name="symptom"></a>Sintoma

Quando implementar recursos, poderá receber o seguinte código de erro e a mensagem:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Em alternativa, poderá receber uma mensagem semelhante que indica:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

## <a name="cause"></a>Causa

Receber estes erros para um de três motivos:

1. O fornecedor de recursos não foi registado para a sua subscrição
1. Versão de API não suportada para o tipo de recurso
1. Não é suportada para o tipo de recurso de localização

## <a name="solution"></a>Solução

A mensagem de erro deverá dar-lhe sugestões para as versões de API e localizações suportadas. Pode alterar o modelo para um dos valores sugeridos. A maioria dos fornecedores são registados automaticamente pelo portal do Azure ou a interface de linha de comandos que está a utilizar, mas não todos. Se não tiver utilizado um fornecedor de recursos específico antes, se pretender registar esse fornecedor. Pode detetar informações sobre fornecedores de recursos através do PowerShell ou a CLI do Azure.

### <a name="solution-1"></a>Solução 1

Para o PowerShell, utilize **Get-AzureRmResourceProvider** para ver o estado do registo.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Para registar um fornecedor, utilize **Register-AzureRmResourceProvider** e forneça o nome do fornecedor de recursos que pretende registar.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Para obter as localizações suportadas para um determinado tipo de recurso, utilize:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Para obter as versões de API suportadas para um determinado tipo de recurso, utilize:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

### <a name="solution-2"></a>Solução 2

**CLI do Azure**

Para ver se o fornecedor está registado, utilize o `az provider list` comando.

```azurecli-interactive
az provider list
```

Para registar um fornecedor de recursos, utilize o `az provider register` de comandos e especificar o *espaço de nomes* para registar.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Para ver as localizações suportadas e as versões de API para um tipo de recurso, utilize:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

### <a name="solution-3"></a>Solução 3

Pode ver o estado de registo e registar um espaço de nomes do fornecedor de recursos através do portal.

1. Para a sua subscrição, selecione **fornecedores de recursos**.

   ![Selecionar fornecedores de recursos](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Observe a lista de fornecedores de recursos e, se necessário, selecione o **registar** ligação para registar o fornecedor de recursos do tipo que está a tentar implementar.

   ![lista de fornecedores de recursos](./media/resource-manager-register-provider-errors/list-resource-providers.png)

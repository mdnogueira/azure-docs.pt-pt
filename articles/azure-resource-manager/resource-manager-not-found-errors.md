---
title: "Não foram encontrados erros de recursos do Azure | Microsoft Docs"
description: "Descreve como resolver erros quando não é possível localizar um recurso."
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
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Resolva os erros não encontrados para recursos do Azure

Este artigo descreve os erros que poderão surgir quando não é possível encontrar um recurso durante a implementação. 

## <a name="symptom"></a>Sintoma

Quando o modelo inclui o nome de um recurso que não pode ser resolvido, receberá um erro semelhante a:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Se tentar utilizar o [referência](resource-group-template-functions-resource.md#reference) ou [listKeys](resource-group-template-functions-resource.md#listkeys) funciona com um recurso que não pode ser resolvido, receberá o seguinte erro:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

Gestor de recursos tem de obter as propriedades de um recurso, mas não é possível identificar o recurso na sua subscrição.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Se está a tentar implementar o recurso em falta no modelo, verifique se tem de adicionar uma dependência. Gestor de recursos otimiza a implementação através da criação de recursos em paralelo, sempre que possível. Se um recurso tem de ser implementado após a outro recurso, tem de utilizar o **dependsOn** elemento no modelo para criar uma dependência no outros recursos. Por exemplo, quando implementar uma aplicação web, o plano de serviço aplicacional tem de existir. Se não tiver especificado que a aplicação web depende do plano do App Service, o Gestor de recursos cria os dois recursos ao mesmo tempo. Recebe um erro a indicar que não é possível localizar o recurso de plano do App Service, porque não existe ainda ao tentar definir uma propriedade na aplicação web. Impedir que este erro ao definir a dependência na aplicação web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Para sugestões de resolução de erros de dependência, consulte [Verifique a sequência de implementação](resource-manager-troubleshoot-tips.md#check-deployment-sequence).

### <a name="solution-2"></a>Solução 2

Quando o recurso existe num grupo de recursos diferente daquela que está a ser implementada, utilize o [resourceId função](resource-group-template-functions-resource.md#resourceid) para obter o nome completamente qualificado do recurso.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>Solução 3

Procure uma expressão que inclui o [referência](resource-group-template-functions-resource.md#reference) função. Os valores que fornece variam com base em se o recurso está no mesmo modelo, grupo de recursos e subscrição. Volte a verificar que está a fornecer os valores de parâmetros necessários para o seu cenário. Se o recurso está num grupo de recursos diferente, forneça o ID de recurso completo. Por exemplo, para fazer referência a uma conta de armazenamento noutro grupo de recursos, utilize:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```
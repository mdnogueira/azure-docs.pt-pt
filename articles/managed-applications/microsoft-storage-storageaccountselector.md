---
title: "Elemento de IU de StorageAccountSelector de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Storage.StorageAccountSelector para aplicações geridas do Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 366a862acc15decf6a8e19f875d5d052695f373c
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Elemento de IU Microsoft.Storage.StorageAccountSelector
Um controlo para selecionar uma conta de armazenamento novo ou existente. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- Se for especificado, `defaultValue.name` está a ser validado automaticamente exclusividade. Se o nome da conta de armazenamento não for exclusivo, o utilizador tem de especificar um nome diferente ou escolher uma conta de armazenamento existente.
- O valor predefinido para `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo que não foram especificado no `constraints.allowedTypes` está oculto e qualquer tipo que não foram especificadas no `constraints.excludedTypes` é apresentado.
`constraints.allowedTypes`e `constraints.excludedTypes` são opcionais, mas não podem ser utilizados em simultâneo.
- Se `options.hideExisting` é **verdadeiro**, o utilizador não é possível escolher uma conta de armazenamento existente. O valor predefinido é **falso**.


## <a name="sample-output"></a>Resultado da amostra
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

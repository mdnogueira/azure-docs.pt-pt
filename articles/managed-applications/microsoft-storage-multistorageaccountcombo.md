---
title: "Elemento de IU de MultiStorageAccountCombo de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Storage.MultiStorageAccountCombo para aplicações geridas do Azure"
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
ms.openlocfilehash: e1dda7917988146807ca6cfab10a3a4eac0b7bb2
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Elemento de IU Microsoft.Storage.MultiStorageAccountCombo
Um grupo de controlos para criar várias contas do storage, com nomes que começam com um prefixo comuns. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Observações
- O valor para `defaultValue.prefix` é concatenado com números inteiros de um ou mais para gerar a sequência de nomes de conta de armazenamento. Por exemplo, se `defaultValue.prefix` é **foobar** e `count` é **2**, em seguida, os nomes da conta de armazenamento **foobar1** e **foobar2** são gerados. Os nomes das contas de armazenamento gerado são validadas exclusividade automaticamente.
- Os nomes de conta de armazenamento são gerados com base lexicographically `count`. Por exemplo, se `count` é 10, em seguida, os nomes de conta de armazenamento de terminar com números inteiros de 2 dígitos (01, 02, 03, etc.).
- O valor predefinido para `defaultValue.prefix` é **nulo**e para `defaultValue.type` é **Premium_LRS**.
- Qualquer tipo que não foram especificado no `constraints.allowedTypes` está oculto e qualquer tipo que não foram especificadas no `constraints.excludedTypes` é apresentado.
`constraints.allowedTypes`e `constraints.excludedTypes` são opcionais, mas não podem ser utilizados em simultâneo.
- Além de gerar nomes de conta de armazenamento, `count` é utilizado para definir o multiplicador adequado para o elemento. Suporta um valor estático, tal como **2**, ou como um valor dinâmico de outro elemento, `[steps('step1').storageAccountCount]`. O valor predefinido é **1**.

## <a name="sample-output"></a>Resultado da amostra
```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

---
title: "Elemento de IU de lista pendente de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Common.DropDown para aplicações geridas do Azure"
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
ms.openlocfilehash: a3cbb7363c04043eb253e91e058753bc271e4e01
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento de IU Microsoft.Common.DropDown
Um controlo de seleção com uma lista pendente. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- A etiqueta de `constraints.allowedValues` é o texto apresentado para um item e respetivo valor é o valor de saída do elemento quando selecionado.
- Se for especificado, o valor predefinido tem de ser uma etiqueta presente no `constraints.allowedValues`. Se não for especificado, o primeiro item `constraints.allowedValues` está selecionada. O valor predefinido é **nulo**.
- `constraints.allowedValues`tem de conter pelo menos um item.
- Este elemento não suporta o `constraints.required` propriedade. Para emular este comportamento, adicionar um item com uma etiqueta e o valor de `""` (uma cadeia vazia) para `constraints.allowedValues`.

## <a name="sample-output"></a>Resultado da amostra
```json
"Bar"
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

---
title: "Elemento de IU de secção de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Common.Section para aplicações geridas do Azure"
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
ms.openlocfilehash: 5a460fde88982c53a7ef3a1ec444d50c1c482fc4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonsection-ui-element"></a>Elemento de IU Microsoft.Common.Section
Um controlo que agrupa um ou mais elementos sob um cabeçalho. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Observações
- `elements`tem de conter pelo menos um elemento e pode conter todos os tipos de elemento, exceto `Microsoft.Common.Section`.
- Este elemento não suporta o `toolTip` propriedade.

## <a name="sample-output"></a>Resultado da amostra
Para aceder os valores de saída de elementos na `elements`, utilize o [basics()](create-uidefinition-functions.md#basics) ou [steps()](create-uidefinition-functions.md#steps) funções e a notação de pontos:

```json
basics('section1').element1
```

Elementos do tipo `Microsoft.Common.Section` ter não existem valores de saída próprios.

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

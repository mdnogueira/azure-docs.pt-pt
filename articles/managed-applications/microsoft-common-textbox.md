---
title: "Elemento de IU de caixa de texto de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Common.TextBox para aplicações geridas do Azure"
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
ms.openlocfilehash: d3fae42ae202fe720761382e1020fa8bd8c62b44
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento de IU Microsoft.Common.TextBox
Um controlo que pode ser utilizado para editar o texto não formatado. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- Se `constraints.required` está definido como **verdadeiro**, em seguida, a caixa de texto tem de conter um valor para validar com êxito. O valor predefinido é **falso**.
- `constraints.regex`é um padrão de expressão regular de JavaScript. Se for especificado, em seguida, valor da caixa de texto tem de corresponder ao padrão para validar com êxito. O valor predefinido é **nulo**.
- `constraints.validationMessage`é uma cadeia a apresentar quando o valor da caixa de texto falhar na validação. Se não for especificado, em seguida, são utilizadas as mensagens de validação incorporadas a caixa de texto. O valor predefinido é **nulo**.
- É possível especificar um valor para `constraints.regex` quando `constraints.required` está definido como **falso**. Neste cenário, um valor não é necessário para a caixa de texto validar com êxito. Se for especificado, tem de corresponder o padrão de expressão regular.

## <a name="sample-output"></a>Resultado da amostra

```json
"foobar"
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

---
title: "Políticas de recursos do Azure para as convenções de nomenclatura | Microsoft Docs"
description: "Descreve as políticas do Azure Resource Manager para as convenções de nomenclatura de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: tomfitz
ms.openlocfilehash: 955b04517c3ccdbe530eb982e6aa1255b69e1edd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="apply-resource-policies-for-names-and-text"></a>Aplicar políticas de recursos de nomes e texto
Este artigo mostra várias [as políticas de recursos](resource-manager-policy.md) pode aplicar para estabelecer as convenções de atribuição de nomes e texto. Estas políticas garantir consistência para os nomes de recursos e valores de etiqueta. 

## <a name="set-naming-convention-with-wildcard"></a>Definir a Convenção de nomenclatura com carateres universais
O exemplo seguinte mostra a utilização do caráter universal, que é suportado pelo **como** condição. A condição indica que, se o nome corresponde ao padrão mencionado (namePrefix\*nameSuffix), em seguida, negar o pedido:

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>Definir a Convenção de nomenclatura com o padrão

Para especificar que os nomes de recursos correspondem um padrão, utilize a condição de correspondência. O exemplo seguinte requer nomes começar com `contoso` e conter seis letras adicionais:

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>Padrão de data de conjunto para o valor da etiqueta

Para exigir um padrão de data de dois dígitos, traço, três letras, dash e quatro dígitos, utilize:

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-multiple-naming-patterns"></a>Definir vários padrões de nomenclatura

Para especificar mais do que uma convenção de nomenclatura de permitido, utilize o **tudo** e **não** operadores. No exemplo seguinte, se o nome fornecido não corresponde um padrão, for negada.

```json
{
    "if": {
        "allOf": [
            {
                "not": {
                    "field": "name",
                    "match": "contoso??????"
                }
            },
            {
                "not": {
                    "field": "name",
                    "match": "contoso-???-##"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Passos seguintes
* Depois de definir uma regra de política (conforme ilustrado nos exemplos anteriores), terá de criar a definição de política e atribua-a um âmbito. O âmbito pode ser uma subscrição, o grupo de recursos ou o recurso. Para atribuir políticas através do portal, consulte [portal do Azure de utilização para atribuir e gerir políticas de recursos](resource-manager-policy-portal.md). Para atribuir políticas através da REST API, PowerShell ou o CLI do Azure, consulte [atribuir e gerir políticas através do script](resource-manager-policy-create-assign.md). 
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).


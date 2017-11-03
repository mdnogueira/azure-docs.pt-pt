---
title: Definir recurso subordinado num modelo do Azure | Microsoft Docs
description: Mostra como definir o tipo de recurso e o nome para o recurso de subordinados de um modelo Azure Resource Manager
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>Definir nome e tipo de recurso subordinado no modelo do Resource Manager
Ao criar um modelo, frequentemente tem de incluir um recurso de subordinados que está relacionado com um recurso principal. Por exemplo, o seu modelo pode incluir um SQL server e uma base de dados. O SQL server é o recurso principal e a base de dados é o recurso de subordinados. 

O formato do tipo de recurso subordinado é:`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

O formato do nome de recurso subordinado é:`{parent-resource-name}/{child-resource-name}`

No entanto, especifique o tipo e o nome num modelo de forma diferente com base em se está aninhado no recurso principal, ou no seu próprio no nível superior. Este tópico mostra como lidar com ambas as abordagens.

Ao construir uma referência a um recurso completamente qualificada, a ordem combinar segmentos do tipo e o nome não é simplesmente uma concatenação dos dois.  Em vez disso, após o espaço de nomes, utilize uma sequência de *nome do tipo* pares de específicos, pelo menos, mais específico para:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`está correto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto

## <a name="nested-child-resource"></a>Recurso subordinado aninhado
É a forma mais fácil para definir um recurso de subordinados para aninhá-lo no recurso principal. O exemplo seguinte mostra uma base de dados do SQL Server aninhado no SQL Server.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Para o recurso subordinado, o tipo está definido como `databases` , mas o respetivo tipo de recurso completo é `Microsoft.Sql/servers/databases`. Não fornecem `Microsoft.Sql/servers/` porque é suposto o principal do tipo de recurso. O nome do recurso subordinado está definido como `exampledatabase` , mas o nome completo inclui o nome do principal. Não fornecem `exampleserver` porque é suposto do recurso principal.

## <a name="top-level-child-resource"></a>Recurso de nível superior de subordinados
Pode definir os recursos subordinados no nível superior. Poderá utilizar esta abordagem se o recurso principal não está implementado no mesmo modelo ou se pretende utilizar `copy` criar subordinados vários recursos. Com esta abordagem, tem de fornecer o tipo de recurso completo e incluir o nome do recurso principal no nome de recurso do subordinado.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

A base de dados é um recurso de subordinados para o servidor, embora são definidos no nível do mesmo no modelo.

## <a name="next-steps"></a>Passos seguintes
* Para ver as recomendações sobre como criar modelos, consulte [melhores práticas para a criação de modelos Azure Resource Manager](resource-manager-template-best-practices.md).
* Para obter um exemplo de criação subordinado vários recursos, consulte [implementar várias instâncias de recursos em modelos do Azure Resource Manager](resource-group-create-multiple.md).

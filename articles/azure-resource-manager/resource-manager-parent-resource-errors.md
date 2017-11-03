---
title: Erros de recursos do principal do Azure | Microsoft Docs
description: Descreve como resolver erros ao trabalhar com um recurso principal.
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
ms.openlocfilehash: e59147c4ac18f730f27b9d4aa9c008f219881065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-parent-resources"></a>Resolva os erros para os recursos de principal

Este artigo descreve os erros que poderá encontrar ao implementar um recurso que depende de um recurso principal.

## <a name="symptom"></a>Sintoma

Quando implementar um recurso que é um subordinado a outro recurso, poderá receber o erro seguinte:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Quando um recurso é um subordinado a outro recurso, o recurso principal tem de existir antes de criar o recurso subordinado. O nome do recurso subordinado inclui o nome do principal. Por exemplo, uma base de dados do SQL Server pode ser definido como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

No entanto, se não especificar uma dependência no servidor, a implementação de base de dados poderá iniciar antes do servidor tenha implementado.

## <a name="solution"></a>Solução

Para resolver este erro, inclua uma dependência.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Para obter mais informações, consulte [definir a ordem de implementação de recursos em modelos do Azure Resource Manager](resource-group-define-dependencies.md).
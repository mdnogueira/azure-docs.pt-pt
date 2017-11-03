---
title: Erros de nomes de conta de armazenamento do Azure | Microsoft Docs
description: Descreve os erros que pode surgir quando especificar um nome de conta de armazenamento.
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
ms.openlocfilehash: dc045827fbd38054a334ff22eb30e0db6a31bac8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-storage-account-names"></a>Resolva os erros para os nomes de conta do storage

Este artigo descreve nomenclatura erros podem ocorrer ao implementar uma conta de armazenamento.

## <a name="symptom"></a>Sintoma

Se o nome da sua conta de armazenamento inclui carateres proibidos, receberá um erro, como:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Contas do storage, tem de fornecer um nome para o recurso que seja exclusivo em todo o Azure. Se não fornecer um nome exclusivo, receberá um erro, como:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Se implementar uma conta de armazenamento com o mesmo nome de uma conta de armazenamento existente na sua subscrição, mas fornecem uma localização diferente, receberá um erro que indica que a conta de armazenamento já existe numa localização diferente. A eliminar a conta de armazenamento existente ou fornecer a mesma localização que a conta de armazenamento existente.

## <a name="cause"></a>Causa

Os nomes das contas de armazenamento tem de ter entre 3 e 24 carateres de comprimento e utilizar apenas letras minúsculas e números. O nome tem de ser exclusivo.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Certifique-se de que o nome de conta de armazenamento é exclusivo. Pode criar um nome exclusivo, concatenar a Convenção de nomenclatura com o resultado do [uniqueString](resource-group-template-functions-string.md#uniquestring) função.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

### <a name="solution-2"></a>Solução 2

Certifique-se de que o nome da sua conta de armazenamento não exceder 24 carateres. O [uniqueString](resource-group-template-functions-string.md#uniquestring) função devolve 13 carateres. Se concatenar um prefixo ou postfix para o **uniqueString** resultar, forneça um valor que é de 11 carateres ou menos.

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

### <a name="solution-3"></a>Solução 3

Certifique-se de que o nome da sua conta de armazenamento não inclui quaisquer letras maiúsculas ou carateres especiais.
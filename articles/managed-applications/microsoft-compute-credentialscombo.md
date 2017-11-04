---
title: "Elemento de IU de CredentialsCombo de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Compute.CredentialsCombo para aplicações geridas do Azure"
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
ms.openlocfilehash: d8faa36aca762bc8d787d5750fcf7efdbaf986ea
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Elemento de IU Microsoft.Compute.CredentialsCombo
Um grupo de controlos com validação incorporadas para palavras-passe do Windows e Linux e das chaves públicas SSH. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>Esquema
Se `osPlatform` é **Windows**, será utilizado o esquema seguinte:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Se `osPlatform` é **Linux**, será utilizado o esquema seguinte:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Observações
- `osPlatform`tem de ser especificado e pode ser **Windows** ou **Linux**.
- Se `constraints.required` está definido como **verdadeiro**, em seguida, a palavra-passe ou as caixas de texto de chave pública SSH tem de conter valores para validar com êxito. O valor predefinido é **verdadeiro**.
- Se `options.hideConfirmation` está definido como **verdadeiro**, em seguida, a segunda caixa de texto para confirmar a palavra-passe do utilizador está oculto. O valor predefinido é **falso**.
- Se `options.hidePassword` está definido como **verdadeiro**, em seguida, a opção para utilizar a autenticação de palavra-passe está oculto. Pode ser utilizado apenas quando `osPlatform` é **Linux**. O valor predefinido é **falso**.
- Restrições adicionais nas palavras-passe permitidas podem ser implementadas utilizando o `customPasswordRegex` propriedade. A cadeia de `customValidationMessage` é apresentada quando uma palavra-passe falha, uma validação personalizada. O valor predefinido para ambas as propriedades é **nulo**.

## <a name="sample-output"></a>Resultado da amostra
Se `osPlatform` é **Windows**, ou o utilizador forneceu uma palavra-passe em vez de uma chave pública SSH, em seguida, é esperado o seguinte resultado:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Se o utilizador forneceu uma chave pública SSH, em seguida, é esperado o seguinte resultado:
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

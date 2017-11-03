---
title: "Elemento de IU de FileUpload de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Common.FileUpload para aplicações geridas do Azure"
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
ms.openlocfilehash: 095880322ba801895a22efcf3476fa37d9e2ac3c
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonfileupload-ui-element"></a>Elemento de IU Microsoft.Common.FileUpload
Um controlo que permite ao utilizador especificar um ou mais ficheiros para carregar. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- `constraints.accept`Especifica os tipos de ficheiros que são apresentados na caixa de diálogo de ficheiro do browser. Consulte o [HTML5 especificação](http://www.w3.org/TR/html5/forms.html#attr-input-accept) para os valores permitidos. O valor predefinido é **nulo**.
- Se `options.multiple` está definido como **verdadeiro**, o utilizador tem permissão para selecionar mais de um ficheiro na caixa de diálogo de ficheiro do browser. O valor predefinido é **falso**.
- Este elemento suporta o carregamento de ficheiros em dois modos com base no valor de `options.uploadMode`. Se **ficheiro** for especificado, a saída contém os conteúdos do ficheiro como um blob. Se **url** for especificado, o ficheiro é carregado para uma localização temporária e a saída contém o URL do blob. Os blobs temporários serão removidos após 24 horas. O valor predefinido é **ficheiro**.
- O valor de `options.openMode` determina como é ler o ficheiro. Se o ficheiro é esperado texto simples, especifique **texto**; pessoa, especifique **binário**. O valor predefinido é **texto**.
- Se `options.uploadMode` está definido como **ficheiro** e `options.openMode` está definido como **binário**, o resultado é codificado em base64.
- `options.encoding`Especifica a codificação a utilizar ao ler o ficheiro. O valor predefinido é **UTF-8**e é utilizado apenas quando `options.openMode` está definido como **texto**.

## <a name="sample-output"></a>Resultado da amostra
Se options.multiple está definido como false e options.uploadMode é o ficheiro, a saída contém os conteúdos do ficheiro como uma cadeia JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Se options.multiple for verdadeira and'options.uploadMode é o ficheiro, em seguida, a saída contém os conteúdos dos ficheiros, como uma matriz JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Se options.multiple está definido como false e options.uploadMode é o url, em seguida, a saída contém um URL como uma cadeia JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Se options.multiple é verdadeira e options.uploadMode é o url, em seguida, a saída contém uma lista dos URLs como uma matriz JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Ao testar um CreateUiDefinition, alguns browsers (como o Google Chrome) truncar URLs gerados pelo elemento Microsoft.Common.FileUpload na consola do browser. Poderá ter de com o botão direito ligações individuais para copiar os URLs completos.


## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

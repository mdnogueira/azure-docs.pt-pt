---
title: "Aplicação do Azure gerida criar funções de definição de IU | Microsoft Docs"
description: "Descreve as funções a utilizar quando criar definições de IU para aplicações geridas do Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 23e407bf93bc51116ca45339bffcb801d69290f0
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="createuidefinition-elements"></a>Elementos de CreateUiDefinition
Este artigo descreve as propriedades para todos os elementos suportados um CreateUiDefinition e o esquema. Utilize estes elementos quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md). O esquema para a maioria dos elementos é o seguinte:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Keep calm and visit the [Azure Portal](portal.azure.com).",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Propriedade | Necessário | Descrição |
| -------- | -------- | ----------- |
| nome | Sim | Um identificador interno para fazer referência a uma instância específica de um elemento. A utilização de mais comuns de nome de elemento está no `outputs`, onde os valores de saída dos elementos especificados estão mapeados para os parâmetros do modelo. Também pode utilizá-la para vincular o valor de saída de um elemento para a `defaultValue` de outro elemento. |
| tipo | Sim | O controlo de IU para compor para o elemento. Para obter uma lista dos tipos suportados, consulte [elementos](#elements). |
| Etiqueta | Sim | O texto a apresentar do elemento. Alguns tipos de elemento contém várias etiquetas, para que o valor pode ser um objeto que contém vários cadeias. |
| DefaultValue | Não | O valor predefinido do elemento. Alguns tipos de elemento suportam valores predefinidos complexa, pelo que o valor pode ser um objeto. |
| Descrição | Não | O texto a apresentar a descrição do elemento. Semelhante ao `label`, alguns elementos suportam vários cadeias de sugestão de ferramenta. Ligações de inline podem ser incorporadas utilizando a sintaxe de Markdown.
| Restrições | Não | Uma ou mais propriedades que são utilizadas para personalizar o comportamento de validação do elemento. As propriedades suportadas restrições variam consoante o tipo de elemento. Alguns tipos de elemento não suporta a personalização do comportamento de validação e assim não ter nenhuma propriedade de restrições. |
| Opções | Não | Propriedades adicionais personalizar o comportamento do elemento. Semelhante ao `constraints`, as propriedades suportadas variam consoante o tipo de elemento. |
| Visível | Não | Indica se o elemento é apresentado. Se `true`, o elemento e elementos subordinados aplicáveis são apresentados. O valor predefinido é `true`. Utilize [funções lógicas](create-uidefinition-functions.md#logical-functions) para controlar dinamicamente o valor desta propriedade.

## <a name="elements"></a>Elementos

A documentação de cada elemento contém um exemplo de IU, observações de esquema, o comportamento do elemento (normalmente concerning validação e personalização suportada) e saída de exemplo.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).

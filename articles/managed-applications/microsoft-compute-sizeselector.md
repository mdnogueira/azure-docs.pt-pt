---
title: "Elemento de IU de SizeSelector de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Compute.SizeSelector para aplicações geridas do Azure"
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
ms.openlocfilehash: 72278b1999f89e5bd5f203794ba3a403a695c933
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Elemento de IU Microsoft.Compute.SizeSelector
Um controlo para selecionar um tamanho de uma ou mais instâncias de máquina virtual. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": []
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Observações
- `recommendedSizes`deve conter, pelo menos, um tamanho. O primeiro tamanho recomendado é utilizado como predefinição.
- Se um tamanho recomendado não está disponível na localização selecionada, o tamanho automaticamente é ignorado. Em vez disso, o próximo tamanho recomendado é utilizado.
- Qualquer dimensão não especificado no `constraints.allowedSizes` está oculto e qualquer dimensão não especificado na `constraints.excludedSizes` é apresentado.
`constraints.allowedSizes`e `constraints.excludedSizes` são opcionais, mas não podem ser utilizados em simultâneo. É possível determinar a lista de tamanhos disponíveis ao chamar [listar tamanhos de máquina virtual disponíveis para uma subscrição](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region).
- `osPlatform`tem de ser especificado e pode ser **Windows** ou **Linux**. É utilizado para determinar os custos de hardware das máquinas virtuais.
- `imageReference`for omitido para imagens originais, mas fornecido para imagens de terceiros. É utilizado para determinar os custos de software das máquinas virtuais.
- `count`é utilizado para definir o multiplicador adequado para o elemento. Suporta um valor estático, tal como **2**, ou como um valor dinâmico de outro elemento, `[steps('step1').vmCount]`. O valor predefinido é **1**.

## <a name="sample-output"></a>Resultado da amostra
```json
"Standard_D1"
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

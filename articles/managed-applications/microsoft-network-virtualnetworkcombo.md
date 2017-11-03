---
title: "Elemento de IU de VirtualNetworkCombo de aplicação gerida do Azure | Microsoft Docs"
description: "Descreve o elemento de IU Microsoft.Network.VirtualNetworkCombo para aplicações geridas do Azure"
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
ms.openlocfilehash: c17ef740dcc709b5b344c4e60ef997a948b2e5de
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Elemento de IU Microsoft.Network.VirtualNetworkCombo
Um grupo de controlos para selecionar uma rede virtual nova ou existente. Utilize este elemento quando [criar uma aplicação gerida do Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Exemplo de IU
![Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo.png)

- No modelo de arames superior, o utilizador tiver selecionado uma nova rede virtual, para que o utilizador pode personalizar o prefixo de nome e endereço de cada sub-rede. Neste caso, a configuração de sub-redes é opcional.
- No modelo de arames na parte inferior, o utilizador tiver selecionado uma rede virtual existente, pelo que o utilizador tem de mapear cada sub-rede que requer que o modelo de implementação para uma sub-rede existente. Sub-redes neste caso, é necessário configurar.

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- Se for especificado, o primeiro sem sobreposição de endereços prefixo do tamanho `defaultValue.addressPrefixSize` é determinado automaticamente com base nas redes virtuais existentes na subscrição do utilizador.
- O valor predefinido para `defaultValue.name` e `defaultValue.addressPrefixSize` é **nulo**.
- `constraints.minAddressPrefixSize`tem de ser especificado. Redes virtuais existentes com um espaço de endereços inferior ao valor especificado não se encontram disponíveis para seleção.
- `subnets`tem de ser especificado, e `constraints.minAddressPrefixSize` tem de ser especificado para cada sub-rede.
- Ao criar uma nova rede virtual, o prefixo de endereço de cada sub-rede é calculado automaticamente com base no prefixo do endereço de rede virtual e o respetivo `addressPrefixSize`.
- Quando utilizar um existente virtual de rede, quaisquer sub-redes menores do que o respetivo `constraints.minAddressPrefixSize` não estão disponíveis para seleção. Além disso, se for especificado, as sub-redes que contenha, pelo menos, `minAddressCount` endereços disponíveis estão indisponíveis para seleção.
O valor predefinido é **0**. Para se certificar de que os endereços disponíveis contíguos, especifique **verdadeiro** para `requireContiguousAddresses`. O valor predefinido é **verdadeiro**.
- A criação de sub-redes na rede virtual existente não é suportada.
- Se `options.hideExisting` é **verdadeiro**, o utilizador não é possível escolher uma rede virtual existente. O valor predefinido é **falso**.

## <a name="sample-output"></a>Resultado da amostra
```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes
* Para uma introdução para aplicações geridas, consulte [descrição geral do Azure gerida aplicações](overview.md).
* Para uma introdução ao criar as definições de IU, consulte [introdução CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição de propriedades comuns de elementos de IU, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

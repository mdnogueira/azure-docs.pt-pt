---
title: "Referência a uma rede virtual existente num modelo de conjunto de dimensionamento do Azure | Microsoft Docs"
description: "Saiba como adicionar uma rede virtual a um modelo existente de conjunto de dimensionamento de Máquina Virtual do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: negat
ms.openlocfilehash: 28117d467b491704aed8d45e5eba42530579dfa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adicionar referência a uma rede virtual existente num modelo de conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento viável mínimo](./virtual-machine-scale-sets-mvss-start.md) para implementar numa rede virtual existente em vez de criar um novo.

## <a name="change-the-template-definition"></a>Altere a definição do modelo

Pode ser visto o nosso modelo de conjunto mínimo de escala viável [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), e o nosso modelo para implementar o dimensionamento definido numa rede virtual existente que pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Vamos examinar diff utilizado para criar este modelo (`git diff minimum-viable-scale-set existing-vnet`) peça a informação:

Em primeiro lugar, iremos adicionar um `subnetId` parâmetro. Esta cadeia será transmitida para a configuração de conjunto de dimensionamento, permitindo que a escala definida para identificar a sub-rede previamente criada para implementar máquinas virtuais em. Esta cadeia deve ter o formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Por exemplo, para implementar o dimensionamento defina numa rede virtual existente com o nome `myvnet`, sub-rede `mysubnet`, grupo de recursos `myrg`e subscrição `00000000-0000-0000-0000-000000000000`, seria o subnetId: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Em seguida, iremos poder eliminar o recurso de rede virtual do `resources` matriz, uma vez que vamos estiver a utilizar uma rede virtual existente e não precisa de implementar uma nova.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

A rede virtual já existe antes de implementar o modelo, pelo que não é necessário especificar uma cláusula dependsOn da escala definida para a rede virtual. Assim, vamos eliminar estas linhas:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Por fim, podemos passa o `subnetId` parâmetro definido pelo utilizador (em vez de utilizar `resourceId` para obter o id de uma vnet na mesma implementação, que é que o mínimo viável do conjunto de dimensionamento modelo faz).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

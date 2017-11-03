---
title: "Referência a uma imagem personalizada num modelo de conjunto de dimensionamento do Azure | Microsoft Docs"
description: "Saiba como adicionar uma imagem personalizada a um modelo existente de conjunto de dimensionamento de Máquina Virtual do Azure"
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
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: cf52fc9e95267c4bc5c0106aadf626685ddd5c24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada para um modelo de conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento viável mínimo](./virtual-machine-scale-sets-mvss-start.md) para implementar a partir da imagem personalizada.

## <a name="change-the-template-definition"></a>Altere a definição do modelo

Pode ser visto o nosso modelo de conjunto mínimo de escala viável [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), e o nosso modelo para implementar o dimensionamento definido a partir de uma imagem personalizada pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Vamos examinar diff utilizado para criar este modelo (`git diff minimum-viable-scale-set custom-image`) peça a informação:

### <a name="creating-a-managed-disk-image"></a>Criar uma imagem de disco gerido

Se já tiver uma imagem de disco gerido personalizado (um recurso do tipo `Microsoft.Compute/images`), em seguida, pode ignorar esta secção.

Em primeiro lugar, iremos adicionar um `sourceImageVhdUri` parâmetro, que é o URI para o blob no Storage do Azure que contém a imagem personalizada para implementar a partir generalizado.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Em seguida, iremos adicionar um recurso do tipo `Microsoft.Compute/images`, que é a imagem de disco gerido com base no blob generalizado localizado em URI `sourceImageVhdUri`. Esta imagem tem de ser na mesma região que o conjunto de dimensionamento que o utiliza. Nas propriedades da imagem, iremos especificar o tipo de SO, a localização do blob (da `sourceImageVhdUri` parâmetro) e o tipo de conta de armazenamento:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

No conjunto de dimensionamento recurso, iremos adicionar um `dependsOn` cláusula referir-se para a imagem personalizada para se certificar de que a imagem é criada antes da escala definida tenta implementar a partir dessa imagem:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>A alteração do dimensionamento definir as propriedades a utilizar a imagem de disco gerido

No `imageReference` da escala definir `storageProfile`, em vez de especificar o publicador, oferta, sku e versão de uma imagem de plataforma, especifique o `id` do `Microsoft.Compute/images` recursos:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, utilizamos o `resourceId` função para obter o ID de recurso da imagem criado no mesmo modelo. Se tiver criado a imagem de disco gerido com antecedência, deve fornecer o id dessa imagem em vez disso. Este id deve ter o formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

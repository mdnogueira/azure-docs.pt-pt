---
title: Converter um modelo de conjunto de dimensionamento do Azure Resource Manager para utilizar o disco gerido | Microsoft Docs
description: Converta um modelo de conjunto de dimensionamento para um modelo de conjunto de dimensionamento de disco gerido.
keywords: "Conjuntos de dimensionamento de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 2f5cb85703888c5056611d466f508547ee72e44b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Converter um modelo de conjunto de dimensionamento de um modelo de conjunto de dimensionamento de disco gerido

Clientes com um modelo do Resource Manager para criar um conjunto de dimensionamento não utilizar o disco gerido, podem querer modificá-lo para utilizar o disco gerido. Este artigo mostra como efetuar esta ação, utilizando como exemplo, um pedido de solicitação do [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates), um repositório de Comunidade para modelos de Gestor de recursos de exemplo. O pedido de solicitação completa pode ser visto aqui: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), e as partes relevantes do diff abaixo, juntamente com explicações:

## <a name="making-the-os-disks-managed"></a>Efetuar os discos de SO geridos

Diff abaixo, é possível ver que foi removido várias variáveis relacionados com propriedades de conta e o disco de armazenamento. Tipo de conta de armazenamento já não é necessário (Standard_LRS é a predefinição), mas foi ainda especificamos-lo se podemos wished para. Apenas Standard_LRS Premium_LRS são suportados e com o disco gerido. Novo sufixo de conta de armazenamento, a matriz de cadeia exclusiva e a contagem de sa foram utilizadas no modelo antigo para gerar nomes de conta de armazenamento. Estas variáveis já não são necessárias no modelo de novo porque o disco gerido cria automaticamente as contas de armazenamento em nome do cliente. Da mesma forma, nome do contentor de vhd e nome do disco de SO já não são necessárias porque o disco gerido automaticamente os nomes de contentores de BLOBs de armazenamento e discos subjacentes.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Diff abaixo, iremos pode ver atualizámos a capacidade de cálculo versão da api para 2016-04-30-preview, que é a versão mínima necessária para suporte de disco gerido com conjuntos de dimensionamento. Tenha em atenção de que foi ainda utilizamos discos não geridos na nova versão de api com a sintaxe antiga se assim o desejar. Por outras palavras, se atualizamos apenas a capacidade de cálculo versão de api e não altere mais nada, o modelo deve continuar a funcionar como anteriormente.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Diff abaixo, é possível ver que estamos a remover o recurso de conta de armazenamento da matriz de recursos completamente. Já não precisamos-los, uma vez que o disco gerido cria automaticamente em nosso nome.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Diff abaixo, iremos pode ver que estamos a remover o depende da cláusula referir-se do dimensionamento definido como o ciclo foi criar contas de armazenamento. O modelo antigo, isto foi garantir que as contas de armazenamento criadas antes do conjunto de dimensionamento teve início a criação, mas esta cláusula já não é necessária com disco gerido. Podemos também de remover a propriedade de contentores de vhd e a propriedade de nome de disco de SO como estas propriedades são processadas automaticamente os bastidores por disco gerido. Se podemos wished, iremos adicionar `"managedDisk": { "storageAccountType": "Premium_LRS" }` na configuração de "osDisk" se queremos discos de SO premium. Apenas as VMs com uma maiúscula ou do minúscula ' na VM sku pode utilizar discos premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Não há nenhuma propriedade explícita a configuração de conjunto de dimensionamento para se deve utilizar o disco gerido ou não gerido. O conjunto de dimensionamento sabe quais pode ser utilizado com base nas propriedades que estão presentes no perfil de armazenamento. Assim, é importante quando modificar o modelo para garantir que as propriedades de direitos estão no perfil de armazenamento do conjunto de dimensionamento.


## <a name="data-disks"></a>discos de dados

Com as alterações acima, escala conjunto utiliza gerido discos para o SO disco, mas que acerca dos discos de dados? Para adicionar os discos de dados, adicione a propriedade "dataDisks" em "storageProfile" ao nível do mesmo como "osDisk". O valor da propriedade é uma lista JSON de objetos, cada um dos quais tem propriedades "lun" (que tem de ser único por disco de dados numa VM), "createOption" ("vazio" está atualmente a única opção suportada) e "diskSizeGB" (o tamanho do disco em gigabytes; deve ser superior a 0 e inferior a 1024) como no exemplo seguinte: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Se especificar `n` esta matriz de discos, cada VM em escala definir obtém `n` discos de dados. Tenha em atenção, no entanto, se estes discos de dados são dispositivos sem formato. Não estão formatados. Trata-se até o cliente para anexar paritition e formatar os discos antes de os utilizar. Opcionalmente, pode também especificamos `"managedDisk": { "storageAccountType": "Premium_LRS" }` em cada objeto de disco de dados para especificar que deve ser um disco de dados premium. Apenas as VMs com uma maiúscula ou do minúscula ' na VM sku pode utilizar discos premium.

Para obter mais informações sobre como utilizar discos de dados com conjuntos de dimensionamento, consulte [neste artigo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Passos seguintes
Por exemplo modelos do Resource Manager utilizando conjuntos de dimensionamento, procure "vmss" no [repositório do github de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter informações gerais, veja o [página de destino principal para conjuntos de dimensionamento](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


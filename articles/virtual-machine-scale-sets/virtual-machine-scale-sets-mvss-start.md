---
title: "Saiba mais sobre modelos de conjunto de dimensionamento de máquina virtual | Microsoft Docs"
description: "Saiba como criar um modelo de conjunto de dimensionamento viável mínimo para conjuntos de dimensionamento de máquina virtual"
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
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 65f02c4675eb752dcc82e9a1d1c7f6c2c193fc32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Saiba mais sobre modelos de conjunto de dimensionamento de máquina virtual
Os [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) são uma ótima maneira de implementar grupos de recursos relacionados. Esta série tutorial mostra como criar um modelo de conjunto de dimensionamento viável mínima e como modificar este modelo de acordo com os vários cenários. Todos os exemplos provenientes isto [repositório do GitHub](https://github.com/gatneil/mvss). 

Este modelo se destina a ser simples. Para obter exemplos mais completos de escala definir modelos, consulte o [repositório do GitHub de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) e procure pastas que contêm a cadeia `vmss`.

Se já estiver familiarizado com a criação de modelos, pode avançar para a secção "Passos" para ver como modificar este modelo.

## <a name="review-the-template"></a>Reveja o modelo

Utilizar o GitHub para rever o nosso modelo de conjunto de escala viável mínima, [azuredeploy. JSON](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

Neste tutorial, vamos examinar o diff (`git diff master minimum-viable-scale-set`) criar a escala viável mínimo definido modelo peça a informação.

## <a name="define-schema-and-contentversion"></a>Definir $schema e contentVersion
Em primeiro lugar, iremos definir `$schema` e `contentVersion` no modelo. O `$schema` elemento define a versão do idioma do modelo e é utilizado para realce da sintaxe Visual Studio e semelhantes funcionalidades de validação. O `contentVersion` elemento não é utilizado pelo Azure. Em vez disso, ajuda a manter um registo da versão do modelo.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>definir os parâmetros
Em seguida, iremos definir dois parâmetros, `adminUsername` e `adminPassword`. Os parâmetros são valores que especificar no momento da implementação. O `adminUsername` parâmetro é simplesmente uma `string` tipo, mas porque `adminPassword` é um segredo, vamos dar tipo `securestring`. Mais tarde, estes parâmetros são transmitidos para a configuração de conjunto de dimensionamento.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definir variáveis
Modelos do Resource Manager também permitem-lhe definir as variáveis a utilizar mais tarde no modelo. Nosso exemplo não utiliza todas as variáveis, pelo que iremos tiver à esquerda do objeto JSON vazio.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definir os recursos
Em seguida, é a secção de recursos do modelo. Aqui, é possível definir o que, na verdade, pretende implementar. Ao contrário `parameters` e `variables` (que são objetos JSON), `resources` é uma lista JSON de objetos JSON.

```json
   "resources": [
```

Todos os recursos requerem `type`, `name`, `apiVersion`, e `location` propriedades. Recurso de primeira neste exemplo tem o tipo `Microsft.Network/virtualNetwork`, nome `myVnet`e apiVersion `2016-03-30`. (Para localizar a versão mais recente da API de um tipo de recurso, consulte o [documentação da API de REST de Azure](https://docs.microsoft.com/rest/api/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Especifique a localização
Para especificar a localização da rede virtual, utilizamos uma [da função de modelo do Resource Manager](../azure-resource-manager/resource-group-template-functions.md). Esta função tem de ser colocado entre aspas e parênteses Retos como esta: `"[<template-function>]"`. Neste caso, utilizamos o `resourceGroup` função. Aceita sem argumentos e devolve um objeto JSON com metadados sobre o grupo de recursos que desta implementação está a ser implementada. O grupo de recursos é definido pelo utilizador no momento da implementação. Vamos, em seguida, índice para este objeto JSON `.location` para obter a localização do objeto JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Especificar as propriedades de rede virtual
Cada recurso de Gestor de recursos tem o seu próprio `properties` secção para configurações específicas para o recurso. Neste caso, iremos especificar que a rede virtual deve ter uma sub-rede com o intervalo de endereços IP privado `10.0.0.0/16`. Um conjunto de dimensionamento sempre está contido dentro de uma sub-rede. Este não pode abranger sub-redes.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Adicionar dependsOn lista
Para além de necessários `type`, `name`, `apiVersion`, e `location` propriedades, cada recurso podem ter opcional `dependsOn` lista de cadeias. Esta lista especifica qual outros recursos a partir desta implementação devem ser concluído antes de implementar este recurso.

Neste caso, não há apenas um elemento na lista, a rede virtual do exemplo anterior. Especificamos esta dependência porque do conjunto de dimensionamento necessita da rede existir antes de criar as VMs. Desta forma, o conjunto de dimensionamento pode dar estes endereços IP privados de VMs do intervalo de endereços IP anteriormente especificado nas propriedades de rede. O formato da cadeia de cada na lista dependsOn é `<type>/<name>`. Utilizar o mesmo `type` e `name` anteriormente utilizado na definição do recurso de rede virtual.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Especificar as propriedades do conjunto de dimensionamento
Conjuntos de dimensionamento tem demasiadas propriedades para personalizar as VMs no conjunto de dimensionamento. Para obter uma lista completa destas propriedades, consulte o [documentação da REST API do conjunto de dimensionamento](https://docs.microsoft.com/en-us/rest/api/virtualmachinescalesets/create-or-update-a-set). Para este tutorial, iremos irá definir apenas algumas propriedades comummente utilizadas.
### <a name="supply-vm-size-and-capacity"></a>Forneça o tamanho da VM e a capacidade
Definir a escala tem de saber que o tamanho da VM para criar ("nome do sku") e quantos essas VMs, criar ("capacidade sku"). Para ver os tamanhos de VM estão disponíveis, consulte o [documentação de tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Escolha o tipo de atualizações
A escala definir também precisa de saber como lidar com atualizações no conjunto de dimensionamento. Atualmente, existem duas opções, `Manual` e `Automatic`. Para obter mais informações sobre as diferenças entre as duas, consulte a documentação sobre [como atualizar um conjunto de dimensionamento](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Escolha o sistema operativo VM
A escala definida tem de saber que colocar em VMs do sistema operativo. Aqui, criamos as VMs com uma imagem de 16.04 LTS Ubuntu patched completamente.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Especifique o computerNamePrefix
O conjunto de dimensionamento implementa várias VMs. Em vez de especificar cada nome VM, especificamos `computerNamePrefix`. O conjunto de dimensionamento acrescenta um índice para o prefixo para cada VM, pelo que os nomes VM têm o formato `<computerNamePrefix>_<auto-generated-index>`.

O fragmento seguinte, utilizamos os parâmetros do antes de definir o nome de utilizador administrador e a palavra-passe para todas as VMs no conjunto de dimensionamento. Podemos fazê-lo com o `parameters` da função de modelo. Esta função aceita uma cadeia que especifica o parâmetro para fazer referência aos e produz o valor para este parâmetro.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Especificar a configuração de rede VM
Por fim, é necessário especificar a configuração de rede para as VMs no conjunto de dimensionamento. Neste caso, iremos só é necessário especificar o ID de sub-rede criada anteriormente. Isto indica a escala definida para colocar as interfaces de rede nesta sub-rede.

Pode obter o ID de rede virtual que contém a sub-rede utilizando o `resourceId` da função de modelo. Esta função aceita o tipo e o nome de um recurso e devolve o identificador de recurso de completamente qualificado. Este ID tem o formato:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

No entanto, o identificador da rede virtual não é suficiente. Tem de especificar a sub-rede específica, que o conjunto de dimensionamento de VMs deve estar no. Para tal, concatenar `/subnets/mySubnet` no ID de rede virtual. O resultado é o ID de completamente qualificado da sub-rede. Efetue esta concatenação com o `concat` função, que utiliza uma série de cadeias e devolve os respetivos concatenação.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

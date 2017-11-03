---
title: "Implementar várias instâncias de recursos do Azure | Microsoft Docs"
description: "Utilize a operação de cópia e matrizes de um modelo Azure Resource Manager para iterar várias vezes durante a implementação de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: tomfitz
ms.openlocfilehash: ed8e3081d2b2e07938d7cf3aa5f95f6dde81bc66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Implementar várias instâncias de um recurso ou a propriedade de modelos Azure Resource Manager
Este tópico mostra como iterar no seu modelo do Azure Resource Manager para criar várias instâncias de um recurso, ou várias instâncias de uma propriedade num recurso.

Se precisar de adicionar lógica para o modelo que lhe permite especificar se um recurso é implementado, consulte [condicionalmente implementar recursos](#conditionally-deploy-resource).

## <a name="resource-iteration"></a>Iteração de recursos
Para criar várias instâncias de um tipo de recurso, adicione um `copy` elemento para que o tipo de recurso. No elemento de cópia, especifique o número de iterações e um nome para este ciclo. O valor da contagem tem de ser um número inteiro positivo e não pode exceder 800. Gestor de recursos cria os recursos em paralelo. Por conseguinte, não é garantida a ordem em que são criados. Para criar recursos iterated na sequência, consulte [cópia série](#serial-copy). 

O recurso para criar várias vezes assume o formato seguinte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Tenha em atenção que o nome de cada recurso inclui o `copyIndex()` função, que devolve a atual iteração do ciclo. `copyIndex()`é baseado em zero. Deste modo, o exemplo seguinte:

```json
"name": "[concat('storage', copyIndex())]",
```

Cria estes nomes de:

* storage0
* storage1
* storage2.

Para alterar o valor de índice, pode transferir um valor na função copyindex (). O número de iterações para efetuar ainda é especificado no elemento copiar, mas o valor de copyIndex é deslocamento pelo valor especificado. Deste modo, o exemplo seguinte:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes de:

* storage1
* storage2
* storage3

A operação de cópia é útil ao trabalhar com as matrizes porque pode iterar através de cada elemento na matriz. Utilize o `length` função na matriz para especificar a contagem de iterações, e `copyIndex` para obter o índice atual na matriz. Deste modo, o exemplo seguinte:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Cria estes nomes de:

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="serial-copy"></a>Cópia de série

Quando utiliza o elemento de cópia para criar várias instâncias de um tipo de recurso, o Resource Manager, por predefinição, implementa as instâncias em paralelo. No entanto, pode pretender especificar que os recursos são implementados numa sequência. Por exemplo, ao atualizar um ambiente de produção, poderá pretender escalonar as atualizações, por isso, apenas um determinado número são atualizadas ao mesmo tempo.

O Resource Manager fornece propriedades no elemento de cópia que lhe permite implementar serialmente várias instâncias. O elemento de cópia, defina `mode` para **série** e `batchSize` para o número de instâncias para implementar cada vez. Com o modo de série, o Gestor de recursos cria uma dependência em instâncias anteriores no ciclo de, pelo que não for iniciado um batch até que o lote anterior seja concluída.

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

A propriedade de modo também aceita **paralelas**, que é o valor predefinido.

Para testar cópia série sem criar recursos reais, utilize o modelo seguinte, que implementa modelos aninhados vazios:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

No histórico de implementação, tenha em atenção que as implementações aninhadas são processadas em sequência.

![implementação de série](./media/resource-group-create-multiple/serial-copy.png)

Num cenário mais realistas, o exemplo seguinte implementa duas instâncias em vez de uma VM com Linux a partir de um modelo aninhado:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

## <a name="property-iteration"></a>Iteração de propriedade

Para criar vários valores para uma propriedade num recurso, adicione um `copy` matriz no elemento de propriedades. Esta matriz contém objetos e cada objeto tem as seguintes propriedades:

* nome – o nome da propriedade para criar vários valores para
* Conte - o número de valores a criar
* um objeto que contém os valores a atribuir à propriedade - de entrada  

O exemplo seguinte mostra como aplicar `copy` para a propriedade dataDisks numa máquina virtual:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Repare que, quando utilizar `copyIndex` dentro de uma iteração de propriedade, tem de fornecer o nome da iteração. Não é necessário que fornecer o nome quando utilizado com iteração do recurso.

O Resource Manager expande o `copy` matriz durante a implementação. O nome da matriz torna-se o nome da propriedade. Os valores de entrada tornar-se as propriedades do objeto. O modelo implementado torna-se:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
          {
              "lun": 0,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 1,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 2,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Pode utilizar iteração do recurso e a propriedade em conjunto. Referência da iteração de propriedade por nome.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
    "copy":{
        "count": 2,
        "name": "vnetloop"
    },
    "location": "[resourceGroup().location]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "copy": [
            {
                "name": "subnets",
                "count": 2,
                "input": {
                    "name": "[concat('subnet-', copyIndex('subnets'))]",
                    "properties": {
                        "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
                    }
                }
            }
        ]
    }
}
```

Só pode incluir um elemento de cópia nas propriedades de cada recurso. Para especificar um ciclo de iteração para a mais do que uma propriedade, defina vários objetos na matriz da cópia. Cada objeto é iterated separadamente. Por exemplo, para criar várias instâncias de ambos os `frontendIPConfigurations` propriedade e o `loadBalancingRules` propriedade num Balanceador de carga, definir ambos os objetos num elemento cópia única: 

```json
{
    "name": "[variables('loadBalancerName')]",
    "type": "Microsoft.Network/loadBalancers",
    "properties": {
        "copy": [
          {
              "name": "frontendIPConfigurations",
              "count": 2,
              "input": {
                  "name": "[concat('loadBalancerFrontEnd', copyIndex('frontendIPConfigurations', 1))]",
                  "properties": {
                      "publicIPAddress": {
                          "id": "[variables(concat('publicIPAddressID', copyIndex('frontendIPConfigurations', 1)))]"
                      }
                  }
              }
          },
          {
              "name": "loadBalancingRules",
              "count": 2,
              "input": {
                  "name": "[concat('LBRuleForVIP', copyIndex('loadBalancingRules', 1))]",
                  "properties": {
                      "frontendIPConfiguration": {
                          "id": "[variables(concat('frontEndIPConfigID', copyIndex('loadBalancingRules', 1)))]"
                      },
                      "backendAddressPool": {
                          "id": "[variables('lbBackendPoolID')]"
                      },
                      "protocol": "tcp",
                      "frontendPort": "[variables(concat('frontEndPort' copyIndex('loadBalancingRules', 1))]",
                      "backendPort": "[variables(concat('backEndPort' copyIndex('loadBalancingRules', 1))]",
                      "probe": {
                          "id": "[variables('lbProbeID')]"
                      }
                  }
              }
          }
        ],
        ...
    }
}
```

## <a name="depend-on-resources-in-a-loop"></a>Dependem de recursos num ciclo
Especifique a que um recurso é implementado depois de outro recurso utilizando o `dependsOn` elemento. Para implementar um recurso que depende da coleção de recursos num ciclo, forneça o nome do ciclo de cópia do elemento dependsOn. O exemplo seguinte mostra como implementar três contas de armazenamento antes de implementar a Máquina Virtual. Não é apresentada a definição de Máquina Virtual completa. Tenha em atenção que o elemento de cópia tem name definido como `storagecopy` e o elemento de dependsOn para as máquinas virtuais também está definido como `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>Criar várias instâncias de um recurso de subordinados
Não é possível utilizar um ciclo de cópia para um recurso subordinado. Para criar várias instâncias de um recurso que normalmente definir como aninhada dentro de outro recurso, em vez disso, tem de criar o recurso de como um recurso de nível superior. É possível definir a relação com o recurso principal através de propriedades de tipo e nome.

Por exemplo, suponha que, normalmente, é possível definir um conjunto de dados como um recurso subordinado dentro de uma fábrica de dados.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Para criar várias instâncias de conjuntos de dados, movê-la fora da fábrica de dados. O conjunto de dados tem de estar no mesmo nível que a fábrica de dados, mas ainda é um recurso de subordinados da fábrica de dados. Manter a relação entre o conjunto de dados e a fábrica de dados através de propriedades de tipo e nome. Uma vez que já não pode ser inferido tipo da posição no modelo, tem de fornecer o tipo completamente qualificado no formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Para estabelecer uma relação principal/subordinado com uma instância do data factory, forneça um nome para o conjunto de dados que inclui o nome do recurso principal. Utilize o formato: `{parent-resource-name}/{child-resource-name}`.  

O exemplo seguinte mostra a implementação:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="conditionally-deploy-resource"></a>Implementar condicionalmente recursos

Para especificar se um recurso é implementado, utilize o `condition` elemento. O valor para este elemento é resolvido para true ou false. Quando o valor for VERDADEIRO, o recurso é implementado. Quando o valor for FALSO, o recurso não está implementado. Por exemplo, para especificar se for implementada uma nova conta de armazenamento ou uma conta de armazenamento existente é utilizada, utilize:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Para obter um exemplo de utilização de um recurso novo ou existente, consulte [novo ou existente de condição de modelo](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json).

Para obter um exemplo de utilização de uma palavra-passe ou chave SSH para implementar a máquina virtual, consulte [modelo de condição de nome de utilizador ou SSH](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="next-steps"></a>Passos seguintes
* Se pretender saber mais sobre as secções de um modelo, consulte [criação de modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber como implementar o modelo, consulte o artigo [implementar uma aplicação com o modelo do Azure Resource Manager](resource-group-template-deploy.md).


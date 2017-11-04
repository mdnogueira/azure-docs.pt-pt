---
title: "Estrutura de definição de política do Azure | Microsoft Docs"
description: "Descreve como definição de política de recurso é utilizada pela política do Azure para estabelecer as convenções de recursos na sua organização ao descrever quando a política é imposta e a ação a tomar."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/31/2017
ms.topic: article
ms.service: azure-policy
ms.custom: 
ms.openlocfilehash: 8ff85f842356eff3f12ccd04e337d71c52d0efcd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição de política do Azure

Definição de política de recurso utilizada pela política do Azure permite-lhe estabelecer as convenções de recursos na sua organização ao descrever quando a política é imposta e a ação a tomar. Se definir convenções, pode controlar os custos e gerir mais facilmente os seus recursos. Por exemplo, pode especificar que apenas determinados tipos de máquinas virtuais são permitidos. Em alternativa, pode exigir que todos os recursos tem uma tag específica. As políticas são herdadas por todos os recursos subordinados. Por isso, se uma política é aplicada a um grupo de recursos, é aplicável a todos os recursos nesse grupo de recursos.

Utilize o JSON para criar uma definição de política. A definição de política contém elementos para:

* Modo
* parâmetros
* Nome a apresentar
* descrição
* Regra de política
  * avaliação lógica
  * efeito

Por exemplo, o seguinte JSON mostra uma política que limita a onde os recursos são implementados:

```json
{
  "properties": {
    "mode": "all",
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

Todas as amostras de modelo de política do Azure estão em [modelos de política do Azure](json-samples.md).

## <a name="mode"></a>Modo

Recomendamos que defina `mode` para `all` para ter uma política de atribuição de avaliar todos os tipos e grupos de recursos. Pode ver um exemplo de uma definição de política que impõe etiquetas num grupo de recursos em [permitir imagem VM personalizada de um grupo de recursos](scripts/allow-custom-vm-image.md).

Quando o definido como **todos os**, grupos de recursos e todos os tipos de recursos são avaliados para a política. O portal utiliza **todos os** para todas as políticas. Se utilizar o PowerShell ou a CLI do Azure, tem de especificar o `mode` parâmetro e defina-o como **todos os**.

Todas as definições de política criadas utilizando o portal utilizam uma `all` modo, contudo se pretender utilizar o PowerShell ou a CLI do Azure, tem de especificar o `mode` parâmetro e configurá-lo para `all`.

Se definir o modo como `indexed`, a atribuição de política será avaliada apenas tipos de recursos que suportam as etiquetas e a localização.


## <a name="parameters"></a>Parâmetros

Parâmetros de ajudam a simplificar a gestão de política, reduzindo o número de definições de política. Considere parâmetros, como os campos de um formulário – `name`, `address`, `city`, `state`. Estes parâmetros sempre permanecem igual ao, no entanto alterar os respetivos valores com base no individuais preencher o formulário. Os parâmetros funcionam da mesma forma na criação de políticas. Incluindo parâmetros na definição de política, pode reutilizar essa política para diferentes cenários utilizando valores diferentes.

Por exemplo, pode definir uma política para uma propriedade de recurso limitar as localizações onde os recursos podem ser implementados. Neste caso, seriam declarar os parâmetros seguintes ao criar a política:


```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

O tipo de um parâmetro pode ser a cadeia ou matriz. A propriedade de metadados é utilizada para ferramentas como o portal do Azure para apresentar informações amigável de utilizador.

A regra de política, referenciar parâmetros com a seguinte sintaxe:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nome a apresentar e uma descrição

Pode utilizar **displayName** e **Descrição** para identificar a definição de política e fornecer contexto para quando é utilizado.

## <a name="policy-rule"></a>Regra de política

A regra de política é composta por **se** e **, em seguida,** blocos. No **se** bloquear, definir uma ou mais condições que especificar quando a política é imposta. Pode aplicar operadores lógicos para estas condições precisamente definir o cenário de uma política.

No **, em seguida,** bloquear, definir o efeito que ocorre quando o **se** condições são verdadeiras.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Operadores lógicos

Operadores lógicos suportados são:

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

O **não** sintaxe inverts o resultado da condição. O **tudo** sintaxe (semelhante ao lógico **e** operação) requer que todas as condições. O **anyOf** sintaxe (semelhante ao lógico **ou** operação) requer um ou mais condições.

Podem aninhar operadores lógicos. O seguinte exemplo mostra um **não** operação que está aninhada dentro de um **tudo** operação.

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>Condições

A condição for avaliada se um **campo** cumpra determinados critérios. As condições suportadas são:

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Ao utilizar o **como** condição, pode fornecer um caráter universal (*) no valor.

Ao utilizar o **corresponder** condição, fornecer `#` para representar um dígito, `?` para uma letra e quaisquer outros carateres para representar esse caráter real. Para obter exemplos, consulte [imagens da VM aprovados](scripts/allowed-custom-images.md).

### <a name="fields"></a>Campos
Condições são formadas campos. Um campo representa propriedades no payload de pedido de recurso que é utilizada para descrever o estado do recurso.  

São suportados os seguintes campos:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*`
* aliases de propriedade - para obter uma lista, consulte [Aliases](#aliases).

### <a name="effect"></a>Efeito
Política suporta os seguintes tipos de efeito:

* **Negar**: gera um evento no registo de auditoria e falhar o pedido
* **Auditoria**: gera um evento de aviso no registo de auditoria, mas não a falhar o pedido
* **Acrescentar**: Adiciona um conjunto definido de campos para o pedido
* **AuditIfNotExists**: permite que o se não existir um recurso de auditoria
* **DeployIfNotExists**: implementa um recurso, se já existir. Atualmente, este efeito só é suportado através de políticas incorporadas.
* **DenyIfNotExists**: nega a criação de um exist se não existe

Para **acrescentar**, tem de fornecer os seguintes detalhes:

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

O valor pode ser uma cadeia ou um objeto de formato JSON.

Com **AuditIfNotExists**, **DeployIfNotExists**, e **DenyIfNotExists**, pode avaliar a existência de um recurso de subordinados e aplicar uma regra e um efeito correspondente Quando esse recurso não existe. Por exemplo, pode exigir que um observador de rede é implementado para todas as redes virtuais.
Para obter um exemplo de auditoria quando uma extensão da máquina virtual não está a ser implementada, consulte [auditoria se a extensão não existe](scripts/audit-ext-not-exist.md).


## <a name="aliases"></a>Aliases

Pode utilizar os aliases de propriedade para aceder às propriedades específicas para um tipo de recurso. Aliases permitem-lhe restringir os valores ou condições são permitidas para uma propriedade num recurso. Cada alias mapeia para caminhos em diferentes versões de API para um tipo de recurso especificado. Durante a avaliação da política, o motor de política obtém o caminho da propriedade para essa versão de API.

**Microsoft.Cache/Redis**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Definir se o servidor de Redis não ssl da porta (6379) está ativada. |
| Microsoft.Cache/Redis/shardCount | Defina o número de partições horizontais sejam criados para de uma Cache de Cluster Premium.  |
| Microsoft.Cache/Redis/sku.capacity | Defina o tamanho da cache de Redis para implementar.  |
| Microsoft.Cache/Redis/sku.family | Defina a família de SKUS para utilizar. |
| Microsoft.Cache/Redis/sku.name | Defina o tipo de Cache de Redis para implementar. |

**Microsoft.Cdn/profiles**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Defina o nome do escalão de preço. |

**Microsoft.Compute/disks**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Defina a oferta da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imagePublisher | Defina o publicador da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imageSku | Defina o SKU da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imageVersion | Defina a versão da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |


**Microsoft.Compute/virtualMachines**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Compute/imageId | Defina o identificador da imagem utilizada para criar a máquina virtual. |
| Microsoft.Compute/imageOffer | Defina a oferta da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imagePublisher | Defina o publicador da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imageSku | Defina o SKU da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imageVersion | Defina a versão da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/licenseType | Defina a que a imagem ou o disco está licenciado no local. Este valor só é utilizado para imagens que contém o sistema operativo do Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Defina a oferta da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/virtualMachines/imagePublisher | Defina o publicador da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/virtualMachines/imageSku | Defina o SKU da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/virtualMachines/imageVersion | Defina a versão da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Defina o URI do vhd. |
| Microsoft.Compute/virtualMachines/sku.name | Defina o tamanho da máquina virtual. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Defina o nome do publicador a extensão. |
| Microsoft.Compute/virtualMachines/extensions/type | Defina o tipo de extensão. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Defina a versão da extensão. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Compute/imageId | Defina o identificador da imagem utilizada para criar a máquina virtual. |
| Microsoft.Compute/imageOffer | Defina a oferta da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imagePublisher | Defina o publicador da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imageSku | Defina o SKU da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/imageVersion | Defina a versão da imagem de plataforma ou imagem do marketplace utilizado para criar a máquina virtual. |
| Microsoft.Compute/licenseType | Defina a que a imagem ou o disco está licenciado no local. Este valor só é utilizado para imagens que contém o sistema operativo do Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Defina o prefixo de nome de computador para todas as máquinas virtuais no conjunto de dimensionamento. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Defina o URI de blob de imagem do utilizador. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Defina os URLs de contentor que são utilizados para armazenar discos de sistema operativo para o conjunto de dimensionamento. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Defina o tamanho das máquinas virtuais num conjunto de dimensionamento. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Defina a camada de máquinas virtuais num conjunto de dimensionamento. |

**Network/applicationgateways**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Defina o tamanho do gateway. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Defina o tipo deste gateway de rede virtual. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Defina o nome do SKU de gateway. |

**Microsoft.Sql/servers**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Defina a versão do servidor. |

**Microsoft.Sql/databases**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Defina a edição da base de dados. |
| Microsoft.Sql/servers/databases/elasticPoolName | Defina o nome do conjunto elástico que está a ser a base de dados. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Defina o serviço configurado objetivo ID do nível da base de dados. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Defina o nome do objetivo de nível de serviço configurado da base de dados.  |

**Microsoft.Sql/elasticpools**

| Alias | Descrição |
| ----- | ----------- |
| servidores/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Defina a DTU partilhada total para o conjunto elástico da base de dados. |
| servidores/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Defina a edição do conjunto elástico. |

**Storage/storageaccounts**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Defina a camada de acesso utilizada para faturação. |
| Microsoft.Storage/storageAccounts/accountType | Defina o nome do SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Defina se o serviço encripta os dados porque esta está armazenada no serviço de armazenamento de Blobs. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Defina se o serviço encripta os dados porque esta está armazenada no serviço de armazenamento de ficheiros. |
| Microsoft.Storage/storageAccounts/sku.name | Defina o nome do SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Definido para permitir apenas o tráfego https para o serviço de armazenamento. |

## <a name="initiatives"></a>Iniciativas

Ativar iniciativas agrupar vários relacionadas com definições de política para simplificar a gestão e atribuições porque funciona com um grupo como um único item. Por exemplo, pode agrupar relacionados todas as definições de política de etiquetagem numa iniciativa único. Em vez de atribuir a cada política individualmente, aplique a iniciativa.

O exemplo a seguir ilustra como criar uma iniciativa para processamento de duas etiquetas: `costCenter` e `productName`. Utiliza duas políticas incorporadas para aplicar o valor da etiqueta predefinida.


```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Passos seguintes

- Reveja os exemplos de modelo de política do Azure em [modelos de política do Azure](json-samples.md).

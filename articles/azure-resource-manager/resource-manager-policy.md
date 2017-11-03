---
title: "As políticas de recursos do Azure | Microsoft Docs"
description: "Descreve como utilizar políticas do Azure Resource Manager para garantir que estão definidas propriedades de recurso consistente durante a implementação. Podem ser aplicadas políticas em grupos de subscrição ou recurso."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: cfdbf35b76b6a7f3cddb2deb35dfc475e0fc600f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resource-policy-overview"></a>Descrição geral da política de recurso
As políticas de recursos permitem-lhe estabelecer as convenções de recursos na sua organização. Se definir convenções, pode controlar os custos e gerir mais facilmente os seus recursos. Por exemplo, pode especificar que apenas determinados tipos de máquinas virtuais são permitidos. Em alternativa, pode exigir que todos os recursos tem uma tag específica. As políticas são herdadas por todos os recursos subordinados. Por isso, se uma política é aplicada a um grupo de recursos, é aplicável a todos os recursos nesse grupo de recursos.

Existem dois conceitos para compreender sobre as políticas:

* definição de política - descrevem quando a política é imposta e a ação a tomar
* atribuição de política - lhe aplica a definição de política para um âmbito (subscrição ou grupo de recursos)

Este tópico centra-se numa definição de política. Para obter informações sobre a atribuição de política, consulte [portal do Azure de utilização para atribuir e gerir políticas de recursos](resource-manager-policy-portal.md) ou [atribuir e gerir políticas através do script](resource-manager-policy-create-assign.md).

As políticas são avaliadas quando criar e atualizar recursos (PUT e aplicar o PATCH operações).

## <a name="how-is-it-different-from-rbac"></a>Como é diferente do RBAC?
Existem alguns principais diferenças entre a política e o controlo de acesso baseado em funções (RBAC). RBAC centra-se nos **utilizador** ações em âmbitos diferentes. Por exemplo, são adicionados à função de contribuinte para um grupo de recursos no âmbito pretendido, pelo que pode efetuar alterações nesse grupo de recursos. Política centra-se nos **recursos** propriedades durante a implementação. Por exemplo, através de políticas, pode controlar os tipos de recursos que podem ser aprovisionados. Em alternativa, pode restringir as localizações onde os recursos podem ser aprovisionados. Ao contrário do RBAC, a política é permitir que um predefinido e explícita negar sistema. 

Utilizar políticas, tem de ser autenticado através do RBAC. Especificamente, a conta tem de:

* `Microsoft.Authorization/policydefinitions/write`permissão para definir uma política
* `Microsoft.Authorization/policyassignments/write`permissão para atribuir uma política 

Estas permissões não estão incluídas no **contribuinte** função.

## <a name="built-in-policies"></a>Políticas incorporadas

O Azure oferece algumas definições de política incorporadas que podem reduzir o número de políticas, que tem de definir. Antes de prosseguir com as definições de política, deve considerar se uma política incorporada já fornece a definição que pretende. As definições de política incorporadas são:

* Localizações permitidas
* Tipos de recursos permitidos
* Permitido SKUs de conta de armazenamento
* Permitido SKUs de máquina virtual
* Aplicar o valor de tag e predefinido
* Impor a etiqueta e o valor
* Não é permitida tipos de recursos
* Necessita do SQL Server versão 12.0
* Exigir encriptação de conta de armazenamento

Pode atribuir alguma destas políticas através de [portal](resource-manager-policy-portal.md), [PowerShell](resource-manager-policy-create-assign.md#powershell), ou [CLI do Azure](resource-manager-policy-create-assign.md#azure-cli).

## <a name="policy-definition-structure"></a>Estrutura de definição de política
Utilize o JSON para criar uma definição de política. A definição de política contém elementos para:

* Modo
* parâmetros
* Nome a apresentar
* descrição
* Regra de política
  * avaliação lógica
  * efeito

O exemplo seguinte mostra uma política que limita a onde os recursos são implementados:

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

## <a name="mode"></a>Modo

Recomendamos que defina `mode` para `all`. Quando o definido como **todos os**, grupos de recursos e todos os tipos de recursos são avaliados para a política. O portal utiliza **todos os** para todas as políticas. Se utilizar o PowerShell ou a CLI do Azure, tem de especificar o `mode` parâmetro e defina-o como **todos os**.
 
Anteriormente, a política foi avaliada nos apenas os tipos de recurso que suportados etiquetas e a localização. O `indexed` modo continua este comportamento. Se utilizar o **todos os** modo, também são avaliadas as políticas em tipos de recurso que não suportam as etiquetas e a localização. [A sub-rede de rede virtual](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet) é um exemplo de um tipo adicionado recentemente. Além disso, os grupos de recursos são avaliados quando o modo está definido como **todos os**. Por exemplo, pode [etiquetas num grupo de recursos de impor](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags). 

## <a name="parameters"></a>Parâmetros
A utilização de parâmetros ajuda a simplificar a gestão de política, reduzindo o número de definições de política. Pode definir uma política para uma propriedade de recursos (por exemplo, limitar as localizações onde os recursos podem ser implementados) e incluir parâmetros na definição do. Em seguida, reutilizar essa definição de política para diferentes cenários mediante a transmissão valores diferentes (por exemplo, especificar um conjunto de localizações para uma subscrição) quando atribuição da política.

Declarar parâmetros quando criar definições de política.

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

Utilizar o **displayName** e **Descrição** para identificar a definição de política e fornecer contexto para quando é utilizado.

## <a name="policy-rule"></a>Regra de política

A regra de política é composta por **se** e **, em seguida,** blocos. No **se** bloquear, definir uma ou mais condições que especificar quando a política é imposta. Pode aplicar operadores lógicos para estas condições precisamente definir o cenário de uma política. No **, em seguida,** bloquear, definir o efeito que ocorre quando o **se** condições são verdadeiras.

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
Os operadores lógicos suportados são:

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
A condição avalia se um **campo** cumpra determinados critérios. As condições suportadas são:

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Ao utilizar o **como** condição, pode fornecer um caráter universal (*) no valor.

Ao utilizar o **corresponder** condição, fornecer `#` para representar um dígito, `?` para uma letra e quaisquer outros carateres para representar esse caráter real. Para obter exemplos, consulte [aplicar políticas de recursos de nomes e texto](resource-manager-policy-naming-convention.md).

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
Política suporta três tipos de efeito - `deny`, `audit`, `append`, `AuditIfNotExists`, e `DeployIfNotExists`. 

* **Negar** gera um evento no registo de auditoria e falhar o pedido
* **Auditoria** gera um evento de aviso no registo de auditoria, mas não a falhar o pedido
* **Acrescentar** adiciona um conjunto definido de campos para o pedido 
* **AuditIfNotExists** -ativar a auditoria se não existir um recurso
* **DeployIfNotExists** -implementar um recurso, se já existir. Atualmente, este efeito só é suportado através de políticas incorporadas.

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

Com **AuditIfNotExists** e **DeployIfNotExists**, pode avaliar a existência de um recurso de subordinados e aplicar uma regra quando não existe esse recurso. Por exemplo, pode exigir que um observador de rede é implementado para todas as redes virtuais.

Para obter um exemplo de auditoria quando uma extensão da máquina virtual não está a ser implementada, consulte [extensões de VM de auditoria](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json).

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

## <a name="policy-sets"></a>Conjuntos de política

Conjuntos de políticas permitem-lhe agrupar várias definições de política relacionada. O conjunto de política simplifica a atribuição e gestão porque funciona com o grupo como um único item. Por exemplo, pode agrupar relacionadas todas as políticas de etiquetagem num conjunto único de política. Em vez de atribuir a cada política individualmente, aplicar o conjunto de política.
 
O exemplo seguinte ilustra como criar uma política definida para processamento de duas etiquetas (costCenter e productName). Utiliza duas políticas incorporadas para aplicar o valor da etiqueta predefinida e impor o valor da etiqueta. O conjunto de política declara dois parâmetros, costCenterValue e productNameValue para reusability. Referencia as duas definições de política incorporada várias vezes com diferentes parâmetros. Para cada parâmetro, pode optar por fornecer um valor fixo, conforme mostrado para tagName ou definido um parâmetro da política, conforme mostrado para tagValue.

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

Adicionar uma política definida com o **New-AzureRMPolicySetDefinition** comando do PowerShell.

Para as operações REST, utilize o **2017-06-01-preview** versão da API, conforme mostrado no exemplo seguinte:

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>Passos seguintes
* Depois de definir uma regra de política, atribua-a um âmbito. Para atribuir políticas através do portal, consulte [portal do Azure de utilização para atribuir e gerir políticas de recursos](resource-manager-policy-portal.md). Para atribuir políticas através da REST API, PowerShell ou o CLI do Azure, consulte [atribuir e gerir políticas através do script](resource-manager-policy-create-assign.md).
* Por exemplo políticas, consulte [repositório do GitHub de política de recursos do Azure](https://github.com/Azure/azure-policy-samples).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](resource-manager-subscription-governance.md).
* O esquema de política está publicado no [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 


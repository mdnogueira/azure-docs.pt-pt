---
title: "Impor segurança com políticas em VMs do Linux no Azure | Microsoft Docs"
description: "Como aplicar uma política para um Gestor de recursos do Linux Máquina Virtual do Azure"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: singhkay
ms.openlocfilehash: 58eaab4fa03afc1e6a5e38bef691cce62a921ea9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Aplicar políticas para VMs com Linux com o Azure Resource Manager
Ao utilizar políticas, uma organização pode aplicar vários convenções e regras em toda a empresa. Imposição do comportamento pretendido pode ajudar a mitigar o risco ao contribuir para o êxito da organização. Neste artigo, vamos descrever como pode utilizar as políticas do Azure Resource Manager para definir o comportamento pretendido para máquinas virtuais da sua organização.

Para uma introdução às políticas, consulte [utilizar a política para gerir recursos e controlar o acesso](../../azure-resource-manager/resource-manager-policy.md).

## <a name="permitted-virtual-machines"></a>Máquinas virtuais permitidos
Para garantir que as máquinas virtuais para a sua organização são compatíveis com uma aplicação, pode restringir os sistemas operativos permitidos. No exemplo seguinte da política, permitir apenas Ubuntu 14.04.2-LTS máquinas virtuais que sejam criados.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Utilize um caráter universal para modificar a política anterior para permitir que qualquer imagem Ubuntu LTS: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Para obter informações sobre campos da política, consulte [aliases de política](../../azure-resource-manager/resource-manager-policy.md#aliases).

## <a name="managed-disks"></a>Managed disks

Para exigir a utilização de discos geridos, use a seguinte política:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Imagens de máquinas virtuais

Por motivos de segurança, pode exigir que apenas imagens personalizadas aprovadas são implementadas no seu ambiente. Pode especificar o grupo de recursos que contém as imagens aprovadas, ou o específicos aprovado imagens.

O exemplo seguinte requer imagens a partir de um grupo de recursos aprovados:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

O exemplo seguinte especifica os IDs de imagem aprovados:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensões de máquina virtual

Pode querer forbid a utilização de determinados tipos de extensões. Por exemplo, uma extensão não pode ser compatível com determinadas imagens de máquina virtual personalizada. O exemplo seguinte mostra como bloquear uma extensão específica. Utiliza publicador e o tipo para determinar que extensões para bloquear.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Passos seguintes
* Depois de definir uma regra de política (conforme ilustrado nos exemplos anteriores), terá de criar a definição de política e atribua-a um âmbito. O âmbito pode ser uma subscrição, o grupo de recursos ou o recurso. Para atribuir políticas através do portal, consulte [portal do Azure de utilização para atribuir e gerir políticas de recursos](../../azure-resource-manager/resource-manager-policy-portal.md). Para atribuir políticas através da REST API, PowerShell ou o CLI do Azure, consulte [atribuir e gerir políticas através do script](../../azure-resource-manager/resource-manager-policy-create-assign.md).
* Para uma introdução às políticas de recursos, consulte [descrição geral da política de recurso](../../azure-resource-manager/resource-manager-policy.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](../../azure-resource-manager/resource-manager-subscription-governance.md).

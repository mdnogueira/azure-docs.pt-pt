---
title: "Impor segurança com políticas em VMs do Windows no Azure | Microsoft Docs"
description: "Como aplicar uma política para um Gestor de recursos do Windows Máquina Virtual do Azure"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: 4bb9a5087ead309ab24128594c53ae313cd4b63c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Aplicar políticas de VMs do Windows com o Azure Resource Manager
Ao utilizar políticas, uma organização pode aplicar vários convenções e regras em toda a empresa. Imposição do comportamento pretendido pode ajudar a mitigar o risco ao contribuir para o êxito da organização. Neste artigo, vamos descrever como pode utilizar as políticas do Azure Resource Manager para definir o comportamento pretendido para máquinas virtuais da sua organização.

Para uma introdução às políticas, consulte [o que é a política do Azure?](../../azure-policy/azure-policy-introduction.md).

## <a name="permitted-virtual-machines"></a>Máquinas virtuais permitidos
Para garantir que as máquinas virtuais para a sua organização são compatíveis com uma aplicação, pode restringir os sistemas operativos permitidos. No exemplo seguinte da política, permitir apenas Windows Server 2012 R2 Datacenter máquinas virtuais que sejam criados:

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
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

Utilize um caráter universal para modificar a política anterior para permitir que qualquer imagem do Datacenter do Windows Server:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Utilize anyOf para modificar a política anterior para permitir que qualquer Datacenter do Windows Server 2012 R2 ou superior imagem:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Para obter informações sobre campos da política, consulte [aliases de política](../../azure-policy/policy-definition.md#aliases).

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


## <a name="azure-hybrid-use-benefit"></a>Benefício de utilização de híbridos do Azure

Quando tiver uma licença no local, pode guardar a taxa de licença na suas máquinas virtuais. Quando não tiver a licença, deve forbid a opção. A seguinte política proíbe a utilização do Azure híbrida utilize benefício (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Passos seguintes
* Depois de definir uma regra de política (conforme ilustrado nos exemplos anteriores), terá de criar a definição de política e atribua-a um âmbito. O âmbito pode ser uma subscrição, o grupo de recursos ou o recurso. Para atribuir políticas, consulte [portal do Azure de utilização para atribuir e gerir políticas de recursos](../../azure-policy/assign-policy-definition.md), [utilize o PowerShell para atribuir políticas](../../azure-policy/assign-policy-definition-ps.md), ou [CLI do Azure de utilização para atribuir políticas](../../azure-policy/assign-policy-definition-cli.md).
* Para uma introdução às políticas de recursos, consulte [o que é a política do Azure?](../../azure-policy/azure-policy-introduction.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](../../azure-resource-manager/resource-manager-subscription-governance.md).
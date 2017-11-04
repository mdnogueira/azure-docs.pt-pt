---
title: "Utilizar a configuração do Estado com conjuntos de dimensionamento de Máquina Virtual de pretendido | Microsoft Docs"
description: "Define a utilização de dimensionamento da Máquina Virtual com a extensão de DSC do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: b61b0acf3072569ab733a13defb465c921d26187
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Define a utilização de dimensionamento da Máquina Virtual com a extensão de DSC do Azure
[Conjuntos de dimensionamento de máquina virtual](virtual-machine-scale-sets-overview.md) pode ser utilizado com o [pretendido Estado Configuration (DSC) do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) processador de extensão. Conjuntos de dimensionamento de máquina virtual fornecem uma forma de implementar e gerir um grande número de máquinas virtuais e podem dimensionar e terminar em resposta a carregar. DSC é utilizado para configurar as VMs à medida que a ficar online, para que executem o software de produção.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Diferenças entre a implementar máquinas virtuais e os conjuntos de dimensionamento de Máquina Virtual
A estrutura subjacente do modelo para um conjunto de dimensionamento de máquina virtual é ligeiramente diferente de uma única VM. Especificamente, uma única VM implementa extensões sob o nó "virtualMachines". Há uma entrada do tipo "extensões" onde o DSC é adicionado ao modelo de

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Um nó de conjunto de dimensionamento de máquina virtual tem uma secção de "Propriedades" com o "VirtualMachineProfile", "extensionProfile" atributo. DSC for adicionado "extensões"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Comportamento de um conjunto de dimensionamento de Máquina Virtual
O comportamento de um conjunto de dimensionamento de máquina virtual é idêntico ao comportamento para uma única VM. Quando é criada uma nova VM, é automaticamente aprovisionado com a extensão de DSC. Se uma versão mais recente do WMF é necessário para a extensão, a VM é reiniciado antes de esteja online. Assim que estiver online, transfere a configuração de DSC. zip e aprovisionar na VM. Podem encontrar mais detalhes no [descrição geral de extensão do DSC do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes
Examine o [modelo Azure Resource Manager para a extensão de DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Saiba como o [extensão de DSC com segurança processa credenciais](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter mais informações sobre o processador de extensão de DSC do Azure, consulte [introdução para o processador de extensão de configuração de estado pretendido do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter mais informações sobre o PowerShell DSC, [visitar o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 


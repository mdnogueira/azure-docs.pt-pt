---
title: "Extensão da máquina virtual do Azure do OMS para Windows | Microsoft Docs"
description: "Implemente o agente do OMS na máquina virtual do Windows com uma extensão da máquina virtual."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: nepeters
ms.openlocfilehash: d933f488fdda0c1d37892be65f2712cf0eb5694e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="oms-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual OMS para Windows

Operations Management Suite (OMS) fornece capacidades de remediação de monitorização, alertas e alertas na nuvem e no local ativos. A extensão de máquina virtual de agente do OMS para Windows é publicada e suportada pela Microsoft. A extensão instala o agente do OMS em máquinas virtuais do Azure e inscreve máquinas virtuais para uma área de trabalho existente do OMS. Este documento fornece detalhes sobre as plataformas suportadas, configurações e opções de implementação para a extensão de máquina virtual do OMS para Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo
A extensão de agente do OMS para o Windows pode ser executado no Windows Server 2008 R2, 2012, 2012 R2 e 2016 versões.

### <a name="internet-connectivity"></a>Conectividade Internet
A extensão de agente do OMS para o Windows requer que a máquina virtual de destino está ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de agente do OMS. A extensão necessita do Id da área de trabalho e a chave de área de trabalho a partir da área de trabalho do OMS de destino, pode encontrá-las no portal do OMS. Porque a chave da área de trabalho deve ser tratada como dados confidenciais, devem ser armazenado numa configuração de definição protegido. Dados da definição de extensão protegido de VM do Azure é encriptados e desencriptados apenas na máquina virtual de destino. Tenha em atenção que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Fabricante | Microsoft.EnterpriseCloud.Monitoring |
| tipo | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (por exemplo) | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo Azure Resource Manager para executar a extensão de agente do OMS durante uma implementação de modelo Azure Resource Manager. Um modelo de exemplo que inclui a extensão de VM de agente do OMS pode ser encontrado no [Galeria de início rápido de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

O JSON para uma extensão da máquina virtual pode ser aninhado o recurso de máquina virtual ou colocado no nível superior de um modelo do Resource Manager JSON ou raiz. O posicionamento de JSON afeta o valor do nome do recurso e do tipo. Para obter mais informações, consulte [definir nome e tipo para recursos subordinados](../../azure-resource-manager/resource-manager-template-child-resource.md). 

O exemplo seguinte assume que a extensão do OMS é aninhada o recurso de máquina virtual. Quando o recurso de extensão de aninhamento, JSON é colocado no `"resources": []` objeto da máquina virtual.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Quando colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual principal e o tipo de reflete a configuração aninhada. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Implementação de PowerShell

O `Set-AzureRmVMExtension` comando pode ser utilizado para implementar a extensão de máquina virtual de agente do OMS para uma máquina virtual existente. Antes de executar o comando, as configurações de públicas e privadas tem de ser armazenados numa tabela hash do PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS ` 
```

## <a name="troubleshoot-and-support"></a>Resolver problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

É possível obter dados sobre o estado das implementações de extensão do portal do Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implementação das extensões para uma determinada VM, execute o seguinte comando utilizando o módulo PowerShell do Azure.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Resultado da execução de extensão é registado para ficheiros encontrados no diretório seguinte:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/en-us/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/en-us/support/faq/).

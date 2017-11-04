---
title: "Extensão de Script personalizado do Azure para Windows | Microsoft Docs"
description: "Automatizar tarefas de configuração de VM do Windows utilizando a extensão de Script personalizado"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: nepeters
ms.openlocfilehash: 4117b6020d2d75a953fd5f032b378e49d2c752ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="custom-script-extension-for-windows"></a>Extensão de Script personalizado para o Windows

A extensão de Script personalizado transfere e executa os scripts em máquinas virtuais do Azure. Esta extensão é útil para configuração de implementação de post, instalação de software ou qualquer outra configuração / tarefas de gestão. Scripts podem ser transferidos a partir do armazenamento do Azure ou o GitHub ou fornecidos para o portal do Azure em tempo de execução de extensão. A extensão de Script personalizado se integra com modelos Azure Resource Manager e também pode ser executada utilizando a CLI do Azure, o PowerShell, o portal do Azure ou a API de REST de Máquina Virtual do Azure.

Este documento fornece detalhes sobre como utilizar a extensão de Script personalizado com o módulo Azure PowerShell, os modelos Azure Resource Manager e detalhes de resolução de problemas de passos em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema Operativo

O personalizada Script extensão para o Windows pode ser executado no cliente do Windows 10, Windows Server 2008 R2, 2012, 2012 R2 e 2016 versões.

### <a name="script-location"></a>Localização do script

O script tem de ser armazenados no Blob storage do Azure ou qualquer outra localização acessível através de um URL válido.

### <a name="internet-connectivity"></a>Conectividade Internet

O personalizada Script extensão para o Windows requer que a máquina virtual de destino está ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de Script personalizado. A extensão requer uma localização de script (Storage do Azure ou noutro local de URL válido) e um comando a executar. Se utilizar o armazenamento do Azure como a origem do script, uma chave de conta e o nome da conta de armazenamento do Azure é necessária. Estes itens devem ser tratados como dados confidenciais e especificados na configuração da definição protegido de extensões. Dados da definição de extensão protegido de VM do Azure é encriptados e desencriptados apenas na máquina virtual de destino.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Fabricante | Microsoft.Compute |
| tipo | extensões |
| typeHandlerVersion | 1.9 |
| fileUris (por exemplo) | https://RAW.githubusercontent.com/Microsoft/DotNet-Core-Sample-Templates/Master/DotNet-Core-Music-Windows/scripts/configure-Music-App.ps1 |
| commandToExecute (por exemplo) | PowerShell sem restrições - ExecutionPolicy - configurar-música-app.ps1 de ficheiros |
| storageAccountName (por exemplo) | examplestorageacct |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q / + hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = |

**Tenha em atenção** -estes nomes de propriedade são sensíveis a maiúsculas e minúsculas. Utilize os nomes visto acima para evitar problemas de implementação.

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo Azure Resource Manager para executar a extensão de Script personalizado durante uma implementação de modelo Azure Resource Manager. Um modelo de exemplo que inclui a extensão de Script personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Implementação de PowerShell

O `Set-AzureRmVMCustomScriptExtension` comando pode ser utilizado para adicionar a extensão de Script personalizado para uma máquina virtual existente. Para obter mais informações, consulte [conjunto AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>Resolver problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

É possível obter dados sobre o estado das implementações de extensão do portal do Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implementação das extensões para uma determinada VM, execute o seguinte comando.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Resultado da execução de extensão é registado para ficheiros encontrados no diretório de seguinte na máquina virtual de destino.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os ficheiros especificados são transferidos para o diretório seguinte na máquina virtual de destino.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
onde `<n>` é um número inteiro decimal que pode ser alterada entre execuções da extensão.  O `1.*` valor corresponde à atual real, `typeHandlerVersion` valor da extensão.  Por exemplo, poderia ser o diretório real `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o `commandToExecute` comando, a extensão será definiu neste diretório (por exemplo, `...\Downloads\2`) como o diretório de trabalho atual. Isto permite a utilização de caminhos relativos para localizar os ficheiros transferidos através de `fileURIs` propriedade. Consulte a tabela abaixo para obter exemplos.

Uma vez que o caminho de transferência absoluto pode variar devido ao longo do tempo, é melhor optar ativamente por participar para caminhos de script/ficheiro relativo no `commandToExecute` string, sempre que possível. Por exemplo:
```json
    "commandToExecute": "powershell.exe . . . -File './scripts/myscript.ps1'"
```

Informações de caminho após o primeiro segmento URI é mantido para os ficheiros transferidos através de `fileUris` lista de propriedades.  Como é mostrado na tabela abaixo, os ficheiros transferidos estão mapeados em subdiretórios de transferência para refletir a estrutura do `fileUris` valores.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de ficheiros transferidos

| URI no fileUris | Caminho relativo de localização de transferência | Localização de transferência absoluto * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\*Como acima, os caminhos de diretório absoluto serão alterado durante a duração da VM, mas não uma execução única da extensão CustomScript.

### <a name="support"></a>Suporte

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure nos [MSDN Azure e Stack Overflow fóruns] (https://azure.microsoft.com/en-us/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/en-us/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/en-us/support/faq/).

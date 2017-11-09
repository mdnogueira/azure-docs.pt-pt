---
title: "Extensão de Script personalizado na VM do Windows | Microsoft Docs"
description: "Automatizar tarefas de configuração de VM do Azure utilizando a extensão de Script personalizado para executar PowerShell scripts numa VM Windows remoto"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: danis
ms.openlocfilehash: b62cf38b2b16bd54b4df38402e8b7d75f5fd5e68
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="custom-script-extension-for-windows-using-the-classic-deployment-model"></a>Personalizado Script extensão para Windows utilizando o modelo de implementação clássica

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como [executar estes passos com o modelo do Resource Manager](../extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

A extensão de Script personalizado transfere e executa os scripts em máquinas virtuais do Azure. Esta extensão é útil para configuração de implementação de post, instalação de software ou qualquer outra configuração / tarefas de gestão. Scripts podem ser transferidos a partir do armazenamento do Azure ou o GitHub ou fornecidos para o portal do Azure em tempo de execução de extensão. A extensão de Script personalizado se integra com modelos Azure Resource Manager e também pode ser executada utilizando a CLI do Azure, o PowerShell, o portal do Azure ou a API de REST de Máquina Virtual do Azure.

Este documento fornece detalhes sobre como utilizar a extensão de Script personalizado com o módulo Azure PowerShell, os modelos Azure Resource Manager e detalhes de resolução de problemas de passos em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema Operativo

A extensão de Script personalizado para o Windows pode ser executado no Windows Server 2008 R2, 2012, 2012 R2 e 2016 versões.

### <a name="script-location"></a>Localização do script

O script tem de ser armazenado no armazenamento do Azure ou qualquer outra localização acessível através de um URL válido.

### <a name="internet-connectivity"></a>Conectividade Internet

O personalizada Script extensão para o Windows requer que a máquina virtual de destino está ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de Script personalizado. A extensão requer uma localização de script (Storage do Azure ou noutro local de URL válido) e um comando a executar. Se utilizar o armazenamento do Azure como a origem do script, uma chave de conta e o nome da conta de armazenamento do Azure é necessária. Estes itens devem ser tratados como dados confidenciais e especificados na configuração da definição protegido de extensões. Dados da definição de extensão protegido de VM do Azure é encriptados e desencriptados apenas na máquina virtual de destino.

```json
{
    "name": "config-app",
    "type": "Microsoft.ClassicCompute/virtualMachines/extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-01",
    "properties": {
        "publisher": "Microsoft.Compute",
        "extension": "CustomScriptExtension",
        "version": "1.8",
        "parameters": {
            "public": {
                "fileUris": "[myScriptLocation]"
            },
            "private": {
                "commandToExecute": "[myExecutionString]"
            }
        }
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Fabricante | Microsoft.Compute |
| Extensão | CustomScriptExtension |
| typeHandlerVersion | 1.8 |
| fileUris (por exemplo) | https://RAW.githubusercontent.com/Microsoft/DotNet-Core-Sample-Templates/Master/DotNet-Core-Music-Windows/scripts/configure-Music-App.ps1 |
| commandToExecute (por exemplo) | PowerShell sem restrições - ExecutionPolicy - configurar-música-app.ps1 de ficheiros |

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo Azure Resource Manager para executar a extensão de Script personalizado durante uma implementação de modelo Azure Resource Manager. Um modelo de exemplo que inclui a extensão de Script personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Implementação de PowerShell

O `Set-AzureVMCustomScriptExtension` comando pode ser utilizado para adicionar a extensão de Script personalizado para uma máquina virtual existente. Para obter mais informações, consulte [conjunto AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).

```powershell
# create vm object
$vm = Get-AzureVM -Name 2016clas -ServiceName 2016clas1313

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri myFileUri -Run 'create-file.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Resolver problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

É possível obter dados sobre o estado das implementações de extensão do portal do Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implementação das extensões para uma determinada VM, execute o seguinte comando.

```powershell
Get-AzureVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Execução de extensão de saída-nos com sessão iniciada ficheiros encontrados no diretório seguinte na máquina virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

O próprio script é transferido para o diretório seguinte na máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda, a qualquer altura neste artigo, pode contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/en-us/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/en-us/support/faq/).
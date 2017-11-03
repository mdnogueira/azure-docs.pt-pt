---
title: "Extensões de máquina virtual e funcionalidades do Windows no Azure | Microsoft Docs"
description: "Saiba que extensões estão disponíveis para máquinas virtuais do Azure, agrupadas por que forneça ou melhorar."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ce0eebd2585c9457d7f922898d7f2fa3e7ffad7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Extensões de máquina virtual e funcionalidades para o Windows

Extensões de máquina virtual do Azure são aplicações de pequenas a indicar tarefas de configuração e a automatização de pós-implementação em máquinas virtuais do Azure. Por exemplo, se uma máquina virtual requer a instalação de software, a proteção de software antivírus ou a configuração de Docker, uma extensão da VM pode ser utilizada para concluir estas tarefas. Extensões VM do Azure podem ser executadas utilizando a CLI do Azure, PowerShell, os modelos Azure Resource Manager e o portal do Azure. As extensões podem ser incluídas com uma nova implementação da máquina virtual ou executar qualquer sistema existente.

Este documento fornece uma descrição geral das extensões de máquina virtual, pré-requisitos para utilizar extensões de máquina virtual e orientações sobre como detetar, gerir e remover as extensões de máquina virtual. Este documento fornece informações generalizadas porque há várias extensões VM, cada um com uma configuração potencialmente exclusiva. Extensão específicos podem ser encontrados detalhes cada documento específicos para a extensão individuais.

## <a name="use-cases-and-samples"></a>Casos de utilização e amostras

Existem muitos diferentes extensões de VM do Azure disponíveis, cada um com específico utilizar maiúsculas e minúsculas. Nalguns casos de utilização de exemplo são:

- Aplicar as configurações de PowerShell Desired estado para uma máquina virtual utilizando a extensão de DSC para Windows. Para obter mais informações, consulte [extensão de configuração do Azure Desired estado](extensions-dsc-overview.md).
- Configure a monitorização de máquina virtual utilizando a extensão de VM de agente de monitorização da Microsoft. Para obter mais informações, consulte [máquinas de virtuais do Azure de ligar ao Log Analytics](../../log-analytics/log-analytics-azure-vm-extension.md).
- Configure a monitorização da infraestrutura do Azure com a extensão de Datadog. Para obter mais informações, consulte o [Datadog blogue](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configure uma máquina virtual do Azure utilizando Chef. Para obter mais informações, consulte [a implementação da máquina virtual de automatização do Azure com Chef](chef-automation.md).

Para além das extensões de específicos de processos, uma extensão de Script personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de Script personalizado para o Windows permite que qualquer script do PowerShell para ser executado numa máquina virtual. Isto é útil quando estiver a conceber o implementações do Azure que necessitam de configuração para além dos quais as nativa ferramentas do Azure podem fornecer. Para obter mais informações, consulte [a extensão de Script do Windows VM personalizada](extensions-customscript.md).


## <a name="prerequisites"></a>Pré-requisitos

Cada extensão da máquina virtual pode ter o seu próprio conjunto de pré-requisitos. Por exemplo, a extensão de VM de Docker tem um pré-requisito de uma distribuição Linux suportado. Requisitos de extensões individuais são detalhados na documentação do específicos de extensão.

### <a name="azure-vm-agent"></a>Agente da VM do Azure
O agente da VM do Azure gere a interação entre uma máquina virtual do Azure e o controlador de recursos de infraestrutura do Azure. O agente da VM é responsável por muitos aspetos funcionais de implementar e gerir máquinas virtuais do Azure, incluindo a executar as extensões VM. O agente da VM do Azure está pré-instalado imagens do Azure Marketplace e pode ser instalado em sistemas operativos suportados.

Para obter informações sobre sistemas operativos suportados e instruções de instalação, consulte [agente da máquina virtual do Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Detetar as extensões de VM
Várias extensões VM diferentes estão disponíveis para utilização com máquinas virtuais do Azure. Para ver uma lista completa, execute o seguinte comando com o módulo do PowerShell do Azure Resource Manager. Certifique-se de que especifica a localização pretendida, quando estiver a executar este comando.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Executar as extensões VM

Extensões de máquina virtual do Azure podem ser executadas em máquinas virtuais existentes, que é útil quando for necessário efetuar alterações de configuração ou recuperar conectividade numa VM já implementada. Extensões VM também podem ser incluídas com implementações de modelos Azure Resource Manager. Ao utilizar extensões com modelos do Resource Manager, pode ativar a máquinas virtuais do Azure, deve ser implementado e configurado sem a necessidade de intervenção de pós-implementação.

Os métodos seguintes podem ser utilizados para executar uma extensão na máquina virtual existente.

### <a name="powershell"></a>PowerShell

Existem vários comandos do PowerShell para executar as extensões individuais. Para ver uma lista, execute os seguintes comandos do PowerShell.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Isto fornece o resultado semelhante ao seguinte:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

O exemplo seguinte utiliza a extensão de Script personalizado para transferir um script a partir de um repositório do GitHub para a máquina virtual de destino e, em seguida, execute o script. Para obter mais informações sobre a extensão de Script personalizado, consulte [descrição geral de extensão de Script personalizado](extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

Neste exemplo, a extensão de acesso de VM é utilizada para repor a palavra-passe administrativa de uma máquina virtual do Windows. Para obter mais informações sobre a extensão de acesso de VM, consulte [serviço de reposição de ambiente de trabalho remoto numa Windows VM](reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

O `Set-AzureRmVMExtension` comando pode ser utilizado para iniciar de qualquer extensão VM. Para obter mais informações, consulte o [referência de conjunto AzureRmVMExtension](https://msdn.microsoft.com/en-us/library/mt603745.aspx).


### <a name="azure-portal"></a>Portal do Azure

Uma extensão VM pode ser aplicada a uma máquina virtual existente através do portal do Azure. Para tal, selecione a máquina virtual que pretende utilizar, escolha **extensões**e clique em **adicionar**. Isto fornece uma lista de extensões disponíveis. Selecione a que pretende e siga os passos no assistente.

A imagem seguinte mostra a instalação da extensão de Antimalware da Microsoft do portal do Azure.

![Instalar a extensão de antimalware](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Extensões VM podem ser adicionadas a um modelo Azure Resource Manager e executadas com a implementação do modelo. As extensões com um modelo de implementação é útil para a criação de implementações do Azure totalmente configuradas. Por exemplo, o seguinte JSON é obtido a partir de um modelo do Resource Manager que implementa um conjunto de máquinas de virtuais com balanceamento de carga e uma base de dados SQL do Azure e, em seguida, instala uma aplicação .NET Core em cada VM. A extensão da VM encarrega-se de que a instalação de software.

Para obter mais informações, consulte o [completa modelo do Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Para obter mais informações, consulte [Authoring Azure Resource Manager modelos com extensões de VM do Windows](template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteger os dados de extensão VM

Quando estiver a executar uma extensão VM, poderá ser necessário incluir informações confidenciais, tais como as credenciais, os nomes das contas de armazenamento e chaves de acesso de conta de armazenamento. Várias extensões VM incluem uma configuração protegida que encripta os dados e apenas desencripta a mesma dentro da máquina virtual de destino. Cada extensão com um esquema de configuração protegido específicos que será detalhado na documentação de extensão específica.

O exemplo seguinte mostra uma instância da extensão de Script personalizado para o Windows. Tenha em atenção de que o comando a executar inclui um conjunto de credenciais. Neste exemplo, o comando a executar não será encriptado.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Proteger a cadeia de execução, movendo a **comando a executar** propriedade para o **protegidos** configuração.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>Resolver problemas de extensões VM

Cada extensão VM poderá ter de passos de resolução de problemas específicos. Por exemplo, quando estiver a utilizar a extensão de Script personalizado, detalhes de execução do script podem ser encontrados localmente na máquina virtual em que a extensão foi executada. Quaisquer passos de resolução de problemas específicos da extensão estão descritos na documentação de extensão específica.

Os passos de resolução de problemas seguintes aplicam-se a todas as extensões de máquina virtual.

### <a name="view-extension-status"></a>Ver o estado de extensão

Depois de uma extensão da máquina virtual tiver sido executada em relação a uma máquina virtual, utilize o seguinte comando do PowerShell para devolver o estado da extensão. Substitua os nomes dos parâmetros de exemplo com os seus próprios valores. O `Name` parâmetro assume o nome especificado para a extensão ao tempo de execução.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

O resultado tem o seguinte aspeto:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

Também é possível encontrar o estado de execução da extensão no portal do Azure. Para ver o estado de uma extensão, selecione a máquina virtual, escolha **extensões**e selecione a extensão pretendida.

### <a name="rerun-vm-extensions"></a>Volte a executar as extensões de VM

Pode haver casos em que uma extensão da máquina virtual tem de ser executada novamente. Pode fazê-lo ao remover a extensão e, em seguida, execute novamente a extensão com um método de execução da sua preferência. Para remover uma extensão, execute o seguinte comando com o módulo Azure PowerShell. Substitua os nomes dos parâmetros de exemplo com os seus próprios valores.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Uma extensão também pode ser removida utilizando o portal do Azure. Para tal:

1. Selecione uma máquina virtual.
2. Selecione **extensões**.
3. Escolha a extensão pretendida.
4. Selecione **desinstalar**.

## <a name="common-vm-extensions-reference"></a>Referência de extensões VM comuns
| Nome de extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de Script personalizado para o Windows |Executar scripts em relação a uma máquina virtual do Azure |[Extensão de Script personalizado para o Windows](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extensão de DSC para Windows |Extensão do PowerShell DSC (configuração do estado pretendido) |[Extensão de DSC para Windows](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extensão do Diagnóstico do Azure |Gerir o diagnóstico do Azure |[Extensão do Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso VM do Azure |Gerir utilizadores e as credenciais |[Extensão de acesso VM para Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

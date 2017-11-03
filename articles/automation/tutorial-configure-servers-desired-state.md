---
title: "Configurar servidores para um estado pretendido e gerir que se desviam com a automatização do Azure | Microsoft Docs"
description: "Tutorial - Gerir configurações de servidor com o Automation DSC do Azure"
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.openlocfilehash: 63a83e35ce29541de578cb264464448fb6ee3e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurar servidores para um estado pretendido e gerir que se desviam

Configuração de estado de Desired de automatização do Azure (DSC) permite-lhe especificar configurações para os servidores e certifique-se de que esses servidores estão no estado especificado ao longo do tempo.



> [!div class="checklist"]
> * Carregar uma VM para serem geridos pelo Automation DSC do Azure
> * Carregar uma configuração para a automatização do Azure
> * Compilar uma configuração para uma configuração de nó
> * Atribuir uma configuração de nó a um nó gerido
> * Verificar o estado de conformidade de um nó gerido

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, terá de:

* Uma conta de Automatização do Azure. Para obter instruções sobre como criar uma conta Run As de Automatização do Azure, veja [Conta Run As do Azure](automation-sec-configure-azure-runas-account.md).
* VM do Azure Resource Manager (não clássica) com o Windows Server 2008 R2 ou posterior. Para obter instruções sobre como criar uma VM, veja [Criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* O Azure PowerShell versão do módulo 3,6 ou posterior. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).
* Familiaridade com DSC. Para obter informações sobre o DSC, consulte [Windows PowerShell Desired Configuration descrição geral do Estado](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração para a automatização do Azure

Para este tutorial, utilizamos uma configuração de DSC simple que assegura que o IIS está instalado na VM.

Para obter informações sobre configurações de DSC, consulte [configurações de DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Num editor de texto, escreva o seguinte e guarde-o localmente como `TestConfig.ps1`.

```powershell
configuration TestConfig {

   Node WebServer {

      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

Chamar o `Import-AzureRmAutomationDscConfiguration` cmdlet para carregar a configuração na sua conta de automatização:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração para uma configuração de nó

Uma configuração de DSC deve ser compilada numa configuração de nó antes de pode ser atribuído a um nó.

Para obter informações sobre configurações de compilação, consulte [configurações de DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Chamar o `Start-AzureRmAutomationDscCompilationJob` cmdlet para compilar o `TestConfig` configuração para uma configuração de nó:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Esta ação cria uma configuração de nó com o nome `TestConfig.WebServer` na sua conta de automatização.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Registar uma VM para serem geridos pelo DSC

Pode utilizar o DSC de automatização do Azure para gerir VMs do Azure (clássica e Resource Manager), VMs no local, máquinas Linux, VMs do AWS e máquinas físicas no local. Neste tópico, vamos abordar registar apenas VMs do Azure Resource Manager.
Para obter informações sobre como registar outros tipos de máquinas, consulte [máquinas de integração para gestão pelo Azure Automation DSC](automation-dsc-onboarding.md).

Chamar o `Register-AzureRmAutomationDscNode` cmdlet para registar a VM no Automation DSC do Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

Este procedimento regista a VM especificada como um nó de DSC na sua conta de automatização do Azure.

### <a name="specify-configuration-mode-settings"></a>Especifique as definições do modo de configuração

Ao registar uma VM como um nó gerido, também pode especificar propriedades da configuração.
Por exemplo, pode especificar que o estado da máquina está a ser aplicado apenas uma vez (DSC não tenta aplicar a configuração após a verificação inicial), especificando `ApplyOnly` como o valor de **ConfigurationMode** propriedade :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

Também pode especificar com que frequência o DSC verifica o estado da configuração utilizando o **ConfigurationModeFrequencyMins** propriedade:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

Para obter mais informações sobre como definir propriedades de configuração para um nó gerido, consulte [Register-AzureRmAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

Para obter mais informações sobre as definições de configuração de DSC, consulte [configurar o Gestor de configuração Local](https://docs.microsoft.com/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerido

Agora, pode atribuir a configuração do nó compilada para a VM que pretende configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

Esta ação atribui a configuração do nó com o nome `TestConfig.WebServer` para o nó de DSC registado com o nome `DscVm`.
Por predefinição, o nó de DSC é analisado relativamente à conformidade com a configuração do nó cada 30 minutos.
Para obter informações sobre como alterar o intervalo de verificação de compatibilidade, consulte [configurar o Gestor de configuração Local](https://docs.microsoft.com/PowerShell/DSC/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verificar o estado de conformidade de um nó gerido

Pode obter relatórios sobre o estado de conformidade de um nó de DSC ao chamar o `Get-AzureRmAutomationDscNodeReport` cmdlet:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports Get-Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$report[0]
```

## <a name="next-steps"></a>Passos seguintes

* Para saber como nós de carregar para serem geridos pelo Automation DSC do Azure, consulte [máquinas de integração de gestão do Automation DSC do Azure](automation-dsc-onboarding.md)
* Para saber como utilizar o portal do Azure para utilizar o DSC de automatização, consulte [introdução ao Azure Automation DSC](automation-dsc-getting-started.md)
* Para saber mais sobre como compilar as configurações de DSC para que pode atribuir-lhes para nós de destino, consulte o artigo [compilar configurações de DSC de automatização do Azure](automation-dsc-compile.md)
* Para referência de cmdlet do PowerShell para o Automation DSC do Azure, consulte [cmdlets do Automation DSC do Azure](/powershell/module/azurerm.automation/#automation)
* Para obter informações sobre preços, consulte [preços do Automation DSC do Azure](https://azure.microsoft.com/pricing/details/automation/)
* Para ver um exemplo de utilização do Azure Automation DSC num pipeline a implementação contínua, consulte [a implementação contínua para IaaS VMs utilizando o Automation DSC do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
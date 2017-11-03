---
title: "Compilar configurações de DSC de automatização do Azure | Microsoft Docs"
description: "Este artigo descreve como compilar as configurações de configuração de estado pretendido (DSC) para a automatização do Azure."
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
ms.assetid: 49f20b31-4fa5-4712-b1c7-8f4409f1aecc
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 02/07/2017
ms.author: magoedte; eslesar
ms.openlocfilehash: 1aadd604e676659475f00760af3b0bdfb13a4792
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Compilar configurações de DSC de automatização do Azure

Pode compilar configurações de configuração de estado pretendido (DSC) de duas formas com a automatização do Azure: no portal do Azure e com o Windows PowerShell. A tabela seguinte irá ajudá-lo a determinar quando deve utilizar o método com base em caraterísticas de cada:

### <a name="azure-portal"></a>Portal do Azure

* Método mais simples com a interface de utilizador interativa
* Formulário para fornecer valores de parâmetros simples
* Controlar facilmente o estado da tarefa
* Acesso autenticado com início de sessão do Azure

### <a name="windows-powershell"></a>Windows PowerShell

* Chamada a partir da linha de comandos com cmdlets do Windows PowerShell
* Pode ser incluído numa solução automatizada com vários passos
* Fornecer valores de parâmetros simples e complexos
* Controlar o estado da tarefa
* Cliente necessário para suportar os cmdlets do PowerShell
* Passar ConfigurationData
* As configurações que utilizarem credenciais de compilação

Depois de decidir um método de compilação, pode seguir os procedimentos respetivos abaixo para iniciar a compilar.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Compilar uma configuração de DSC com o portal do Azure

1. Da sua conta de automatização, clique em **configurações de DSC**.
2. Clique uma configuração para abrir o painel.
3. Clique em **compilar**.
4. Se a configuração não tem parâmetros, será solicitado para confirmar se pretende compilá-la. Se a configuração tiver parâmetros, o **compilar a configuração** painel será aberto para que possam fornecer valores de parâmetros. Consulte o [ **parâmetros básicos** ](#basic-parameters) secção abaixo para obter mais detalhes sobre os parâmetros.
5. O **tarefa de compilação** painel está aberto num modo a que pode controlar o estado da tarefa de compilação e as configurações de nó (documentos de configuração do MOF)-causado que seja colocada no servidor de solicitação do DSC do Azure da automatização.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Compilar uma configuração de DSC com o Windows PowerShell

Pode utilizar [ `Start-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) para iniciar a compilar com o Windows PowerShell. O código de exemplo seguinte inicia compilação de uma configuração de DSC chamada **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob`Devolve um objeto de tarefa de compilação que pode utilizar para monitorizar o estado. Em seguida, pode utilizar este objeto de tarefa de compilação com [ `Get-AzureRmAutomationDscCompilationJob` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) para determinar o estado da tarefa de compilação e [ `Get-AzureRmAutomationDscCompilationJobOutput` ](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) para ver o respetivos fluxos (saída). O código de exemplo seguinte inicia compilação do **SampleConfig** configuração, tem de aguardar até ser concluída e, em seguida, apresenta os fluxos.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Parâmetros básicos
Declaração de parâmetro nas configurações de DSC, incluindo os tipos de parâmetro e propriedades, funciona os mesmos que os runbooks de automatização do Azure. Consulte [iniciar um runbook na automatização do Azure](automation-starting-a-runbook.md) para saber mais sobre os parâmetros do runbook.

O exemplo seguinte utiliza dois parâmetros denominados **FeatureName** e **IsPresent**, para determinar os valores de propriedades no **ParametersExample.sample** configuração de nó, gerada durante a compilação.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

Pode compilar configurações de DSC utilizar parâmetros básicos no portal do Automation DSC do Azure, ou com o Azure PowerShell:

### <a name="portal"></a>Portal

No portal, pode introduzir os valores de parâmetros depois de clicar em **compilar**.

![texto alternativo](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell requer parâmetros um [tabela hash](http://technet.microsoft.com/library/hh847780.aspx) em que a chave corresponde ao nome de parâmetro e o valor igual ao valor de parâmetro.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Para obter informações sobre a transmitir PSCredentials como parâmetros, consulte <a href="#credential-assets"> **ativos de credenciais** </a> abaixo.

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** permite separar estrutural configuração a partir de qualquer configuração específica do ambiente ao utilizar o PowerShell DSC. Consulte [separando "Que" a partir de "Onde" no DSC do PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **ConfigurationData**.

> [!NOTE]
> Pode utilizar **ConfigurationData** durante a compilação no DSC da automatização do Azure com o Azure PowerShell, mas não no portal do Azure.

A configuração de DSC de exemplo seguinte utiliza **ConfigurationData** através de **$ConfigurationData** e **$AllNodes** palavras-chave. Também terá do [ **xWebAdministration** módulo](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Pode compilar a configuração de DSC acima com o PowerShell. O abaixo PowerShell adiciona duas configurações de nó para o servidor de solicitação do DSC do Azure Automation: **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Elementos

Referências de recurso são os mesmos nas configurações de DSC de automatização do Azure e runbooks. O seguinte para obter mais informações, consulte:

* [Certificados](automation-certificates.md)
* [Ligações](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Variáveis](automation-variables.md)

### <a name="credential-assets"></a>Ativos de credenciais

Enquanto as configurações de DSC na automatização do Azure podem referenciar ativos de credenciais utilizando **Get-AzureRmAutomationCredential**, ativos de credenciais podem também ser transmitidos através dos parâmetros, se assim o desejar. Se uma configuração assume um parâmetro de **PSCredential** escreva, em seguida, tem de transmitir o nome de cadeia de um recurso de credencial de automatização do Azure como valor do parâmetro, que, em vez de um objeto PSCredential. Nos bastidores, o recurso de credencial de automatização do Azure com esse nome será obtido e transmitido para a configuração.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer encriptar as credenciais no ficheiro MOF de configuração de nó. A automatização do Azure assume este passo adicional e encripta todo o ficheiro MOF. No entanto, atualmente tem indicar DSC do PowerShell é pode as credenciais ser debitado em texto simples durante a geração de MOF de configuração de nó, porque o PowerShell DSC não sabe que da automatização do Azure irá encriptar todo o ficheiro MOF após a respetiva geração através de uma tarefa de compilação.

Pode dizer DSC do PowerShell que pode as credenciais ser debitado em texto simples na configuração de nó geradas MOFs utilizando [ **ConfigurationData**](#configurationdata). Deverá passar `PSDscAllowPlainTextPassword = $true` através de **ConfigurationData** para o nome de cada bloco de nó que aparece na configuração de DSC e utiliza as credenciais.

O exemplo seguinte mostra uma configuração de DSC que utiliza um recurso de credencial de automatização.

```powershell
Configuration CredentialSample
{
    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -AutomationAccountName "AutomationAcct" -Name "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Pode compilar a configuração de DSC acima com o PowerShell. O abaixo PowerShell adiciona duas configurações de nó para o servidor de solicitação do DSC do Azure Automation: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

## <a name="importing-node-configurations"></a>Importar configurações de nó

Também pode importar configuratons de nó (MOFs) que têm de ser compilado fora do Azure. Uma vantagem desta situação é que confiturations esse nó pode ser assinado.
Uma configuração de nó assinada é verificada localmente num nó gerido pelo agente de DSC, garantindo que a configuração a ser aplicada ao nó provenientes de uma origem autorizada.

> [!NOTE]
> Pode utilizar Importar assinado configurações na sua conta de automatização do Azure, mas a automatização do Azure não suporta atualmente compilar configurações assinadas.

> [!NOTE]
> Um ficheiro de configuração do nó tem de ser não superior a 1 MB para permitir que seja possível importar para a automatização do Azure.

Pode saber como assinar configurações de nó em https://msdn.microsoft.com/en-us/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no portal do Azure

1. Da sua conta de automatização, clique em **configurações de nó DSC**.

    ![Configurações de nó DSC](./media/automation-dsc-compile/node-config.png)
2. No **configurações de nó DSC** painel, clique em **adicionar um NodeConfiguration**.
3. No **importação** painel, clique no ícone de pasta junto a **ficheiro de configuração de nó** caixa de texto para procurar um ficheiro de configuração do nó (MOF) no seu computador local.

    ![Procurar ficheiro local](./media/automation-dsc-compile/import-browse.png)
4. Introduza um nome no **nome da configuração** caixa de texto. Este nome tem de corresponder ao nome da configuração a partir da qual a configuração do nó foi compilada.
5. Clique em **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importar uma configuração de nó com o PowerShell

Pode utilizar o [importação AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) cmdlet para importar uma configuração de nó para a sua conta de automatização.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```




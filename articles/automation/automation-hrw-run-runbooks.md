---
title: "Executar runbooks num trabalho de Runbook híbrida de automatização do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre a execução de runbooks em computadores no seu local datacenter ou o fornecedor de nuvem com a função Runbook Worker híbrido."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: magoedte
ms.openlocfilehash: d069b5040e0e280e54d4ffd8eccdacca302b7cc5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks em execução num Runbook Worker híbrido 
Não há qualquer diferença na estrutura de runbooks que são executados na automatização do Azure e os que executam o um Runbook Worker híbrido. Os Runbooks que utiliza com cada provavelmente diferem significativamente entanto, uma vez que os runbooks direcionada para um Runbook Worker híbrido normalmente gerir recursos no próprio computador local ou relativamente aos recursos no ambiente local onde está implementada, enquanto os runbooks do A automatização do Azure, normalmente, gerir os recursos na nuvem do Azure.

Pode editar um runbook para o trabalho de Runbook híbrida na automatização do Azure, mas poderão ter dificuldades tentar testar o runbook no editor.  Os módulos do PowerShell que aceder os recursos locais poderão não estar instalados no seu ambiente de automatização do Azure sendo que nesse caso, o teste falhará.  Se instalar os módulos necessários, em seguida, o runbook será executado, mas não será capaz de aceder a recursos locais para um teste completo.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>A iniciar um runbook no Runbook Worker híbrido
[Iniciar um Runbook na automatização do Azure](automation-starting-a-runbook.md) descreve diferentes métodos para iniciar um runbook.  Runbook Worker híbrido adiciona um **RunOn** opção onde pode especificar o nome de um grupo de trabalho de Runbook híbrida.  Se não for especificado um grupo, o runbook é obtido e execute dos workers nesse grupo.  Se esta opção não for especificada, em seguida, é executado na automatização do Azure como habitualmente.

Quando inicia um runbook no portal do Azure, é-lhe apresentado um **executar em** opção onde pode selecionar **Azure** ou **Worker híbrido**.  Se selecionar **Worker híbrido**, em seguida, pode selecionar o grupo de uma lista pendente.

Utilize o **RunOn** parâmetro.  Pode utilizar o seguinte comando para iniciar um runbook denominado Test-Runbook num grupo de trabalho de Runbook híbrida denominado MyHybridGroup através do Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> O **RunOn** parâmetro foi adicionado para o **AzureAutomationRunbook início** cmdlet na versão 0.9.1 do Microsoft Azure PowerShell.  Deve [transferir a versão mais recente](https://azure.microsoft.com/downloads/) se tiver um anteriormente instalado.  Só tem de instalar esta versão numa estação de trabalho em que estiver a iniciar o runbook a partir do Windows PowerShell.  Não é necessário instalá-lo no computador de trabalho, a menos que pretende iniciar runbooks a partir desse computador.  Atualmente não é possível iniciar um runbook num Runbook Worker híbrido partir de outro runbook, uma vez que isto iria requer a versão mais recente do Azure Powershell para ser instalada na sua conta de automatização.  A versão mais recente é atualizada automaticamente na automatização do Azure e automaticamente enviadas por push para baixo para os trabalhadores em breve.
>
>

## <a name="runbook-permissions"></a>Permissões de Runbook
Os Runbooks em execução num Runbook Worker híbrido não é possível utilizar o mesmo método que é normalmente utilizado para autenticação de runbooks nos recursos do Azure, uma vez que estão a aceder a recursos fora do Azure.  O runbook ou pode fornecer a sua própria autenticação aos recursos locais, ou pode especificar uma conta RunAs para fornecer um contexto de utilizador para todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de Runbook
Por predefinição, os runbooks serão executados no contexto da conta do sistema local no computador local, pelo que têm de fornecer as suas próprias autenticação a recursos que acedem.  

Pode utilizar [credencial](http://msdn.microsoft.com/library/dn940015.aspx) e [certificado](http://msdn.microsoft.com/library/dn940013.aspx) ativos no runbook com os cmdlets que permitem-lhe especificar as credenciais para que pode autenticar em recursos diferentes.  O exemplo seguinte mostra uma parte de um runbook que reinicia um computador.  Este obtém as credenciais de um recurso de credenciais e o nome do computador de um recurso de variável e, em seguida, utiliza estes valores com o cmdlet de reiniciar o computador.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Também pode tirar partido [InlineScript](automation-powershell-workflow.md#inlinescript), que lhe permite executar blocos de código noutro computador com as credenciais especificadas pelo [parâmetro comum de PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Conta RunAs
Em vez de ter runbooks fornecer as seus próprios autenticação aos recursos locais, pode especificar um **RunAs** conta para um grupo de trabalho híbrida.  Especificar um [recurso de credencial](automation-credentials.md) que tem acesso aos recursos locais e todos os runbooks executados estas credenciais quando em execução num Runbook Worker híbrido no grupo.  

O nome de utilizador para a credencial tem de ser um dos seguintes formatos:

* domínio ome de utilizador
* username@domain
* nome de utilizador (para contas locais no computador local)

Utilize o procedimento seguinte para especificar uma conta RunAs para um grupo de trabalho híbridas:

1. Criar um [recurso de credencial](automation-credentials.md) com acesso aos recursos locais.
2. Abra a conta de automatização no portal do Azure.
3. Selecione o **grupos de trabalho híbrida** mosaico e, em seguida, selecione o grupo.
4. Selecione **todas as definições** e, em seguida, **definições do grupo de trabalho híbrida**.
5. Alteração **Run** de **predefinido** para **personalizada**.
6. Selecione a credencial e clique em **guardar**.

### <a name="automation-run-as-account"></a>Conta Run As de automatização
Como parte do processo de compilação automatizada para a implementação de recursos no Azure, pode necessitar de acesso a sistemas para suportar uma tarefa ou o conjunto de passos numa sequência de implementação no local.  Para suportar a autenticação no Azure utilizando a conta Run As, tem de instalar o certificado da conta Run As.  

O runbook do PowerShell seguinte, *exportação RunAsCertificateToHybridWorker*, exporta o certificado Run As da conta de automatização do Azure e transfere e importa-os para o arquivo de certificados do computador local numa versão híbrida trabalho ligado para a mesma conta.  Depois de concluído este passo, verifica que o worker pode autenticar com êxito para o Azure utilizando a conta Run As.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT 
    .TAGS Azure Automation 
    .LICENSEURI 
    .PROJECTURI 
    .ICONURI 
    .EXTERNALMODULEDEPENDENCIES 
    .REQUIREDSCRIPTS 
    .EXTERNALSCRIPTDEPENDENCIES 
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. 
  
    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team 
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])] 

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"
       
    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"
   
    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose 

    Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection" 
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

Guardar o *exportação RunAsCertificateToHybridWorker* runbook para o seu computador com um `.ps1` extensão.  Importe-o para a sua conta de automatização e editar o runbook, alterar o valor da variável `$Password` com a sua própria palavra-passe.  Publicar e, em seguida, executar o runbook direcionada para o grupo de trabalho híbrida que são executados e autenticar runbooks com a conta Run As.  O fluxo de trabalho relatórios ao tentar importar o certificado para o arquivo do computador local e está de acordo com várias linhas, dependendo de quantos contas de automatização são definidas na sua subscrição e se a autenticação é efetuada com êxito.  

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Resolução de problemas de runbooks num Runbook Worker híbrido
Os registos são armazenados localmente em cada função de trabalho híbrida no C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Função de trabalho híbrida também regista eventos e erros no registo de eventos do Windows no **aplicações e serviços Logs\Microsoft-SMA\Operational**.  Os eventos relacionados com os runbooks executados na função de trabalho são escritos **aplicações e serviços Logs\Microsoft-Automation\Operational**.  O **Microsoft SMA** registo inclui muitas mais eventos relacionados com a tarefa de runbook enviada para o trabalho e o processamento do runbook.  Enquanto o **automatização do Microsoft** registo de eventos tem muitos eventos com detalhes a prestar assistência com a resolução de problemas de execução do runbook, irá encontrar, pelo menos, os resultados da tarefa de runbook.  

[Runbook de saída e mensagens](automation-runbook-output-and-messages.md) são enviadas para a automatização do Azure do híbrida workers, tal como tarefas de runbook é executado na nuvem.  Também pode ativar os fluxos de verboso e progresso da mesma forma que faria para outros runbooks.  

Se os runbooks não são concluir com êxito e a tarefa de resumo mostra um Estado de **suspenso**, consulte o artigo de resolução de problemas [Runbook Worker híbrido: uma tarefa de runbook termina com o estado suspenso ](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).   

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre os diferentes métodos que podem ser utilizados para iniciar um runbook, consulte o artigo [iniciar um Runbook na automatização do Azure](automation-starting-a-runbook.md).  
* Para compreender os diferentes procedimentos para trabalhar com runbooks do PowerShell e o fluxo de trabalho do PowerShell na automatização do Azure utilizando o editor de texto, consulte [editar um Runbook na automatização do Azure](automation-edit-textual-runbook.md)
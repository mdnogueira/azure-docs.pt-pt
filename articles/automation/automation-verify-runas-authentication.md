---
title: "Validar a configuração da conta de Automatização do Azure | Microsoft Docs"
description: "Este artigo descreve como confirmar que a configuração da sua conta de automatização está corretamente configurada."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 55f5d5524019ac63565e5ddd1f47dbdd65f05065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Testar a autenticação da conta Run As de Automatização do Azure
Depois de criar uma conta de Automatização com êxito, pode executar um teste simples para confirmar que consegue autenticar com êxito no Azure Resource Manager ou na implementação clássica do Azure com a sua conta Run As de Automatização recentemente criada ou atualizada.    

## <a name="automation-run-as-authentication"></a>Autenticação Run As de Automatização
Utilize o código de exemplo abaixo para [criar um runbook do PowerShell](automation-creating-importing-runbook.md) para verificar a autenticação com a conta Run As e também nos runbooks personalizados para autenticar e gerir os recursos do Resource Manager com a sua conta de Automatização.   

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Add-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Repare que o cmdlet utilizado para autenticar no runbook - **Add-AzureRmAccount**, utiliza o conjunto de parâmetros *ServicePrincipalCertificate*.  Autentica utilizando o certificado de serviço principal, não as credenciais.  

Quando [executar o runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) para validar a conta Run As, é criada uma [tarefa de runbook](automation-runbook-execution.md), é apresentado o painel Tarefa, bem como o estado da tarefa no mosaico **Resumo da Tarefa**. O estado da tarefa começará como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na nuvem fique disponível. Em seguida, irá mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.  Quando tiver concluído a tarefa de runbook, vemos um estado de **Concluído**.

Para ver os resultados detalhados do runbook, clique no mosaico **Saída**.  No painel **Saída**, deve verificar se foi autenticado com êxito e devolveu uma lista de todos os recursos em todos os grupos de recursos na sua subscrição.  

Não se esqueça de remover o bloco de código que começa com o comentário `#Get all ARM resources from all resource groups` quando reutilizar o código para os runbooks.

## <a name="classic-run-as-authentication"></a>Autenticação Run As clássica
Utilize o código de exemplo abaixo para [criar um runbook do PowerShell](automation-creating-importing-runbook.md) para verificar a autenticação com a conta Run As Clássica e também nos runbooks personalizados para autenticar e gerir os recursos no modelo de implementação clássico.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

Quando [executar o runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) para validar a conta Run As, é criada uma [tarefa de runbook](automation-runbook-execution.md), é apresentado o painel Tarefa, bem como o estado da tarefa no mosaico **Resumo da Tarefa**. O estado da tarefa começará como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na nuvem fique disponível. Em seguida, irá mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.  Quando tiver concluído a tarefa de runbook, vemos um estado de **Concluído**.

Para ver os resultados detalhados do runbook, clique no mosaico **Saída**.  No painel **Saída**, deve verificar se foi autenticado com êxito e devolveu uma lista de todas as VMs do Azure por VMName implementadas na sua subscrição.  

Não se esqueça de remover o cmdlet **Get-AzureVM** quando reutilizar o código para os runbooks.

## <a name="next-steps"></a>Passos seguintes
* Para começar com runbooks do PowerShell, consulte [My first PowerShell runbook (O meu primeiro runbook do PowerShell)](automation-first-runbook-textual-powershell.md).
* Para obter mais informações sobre a Criação de Gráficos, consulte [Graphical authoring in Azure Automation (Criação de gráficos na Automatização do Azure)](automation-graphical-authoring-intro.md).

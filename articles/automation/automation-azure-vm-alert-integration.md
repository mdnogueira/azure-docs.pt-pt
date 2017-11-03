---
title: " Remediar alertas VM do Azure com Runbooks de automatização | Microsoft Docs"
description: "Este artigo demonstra como integrar os alertas de Máquina Virtual do Azure com runbooks de automatização do Azure e, remediar automaticamente problemas"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: 18cccc88ab74235722e2f4886671fc483ab67da8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Cenário de automatização do Azure - remediar alertas de VM do Azure
Automatização do Azure e de máquinas virtuais do Azure tem lançada uma nova funcionalidade, permitindo-lhe configurar alertas de Máquina Virtual (VM) para executar runbooks de automatização. Esta nova funcionalidade permite-lhe efetuar automaticamente a remediação padrão em resposta a alertas VM, como reiniciar ou parar a VM.

Anteriormente, durante a criação de regra de alerta de VM tenha sido [especificar um webhook da automatização](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) a um runbook para executar o runbook, sempre que acionou o alerta. No entanto, isto necessário, faça o trabalho de criar o runbook, criar o webhook para o runbook e, em seguida, copiar e colar o webhook durante a criação de regra de alerta. Com esta nova versão, o processo é mais fácil porque diretamente pode escolher um runbook a partir de uma lista durante a criação de regra de alerta e pode escolher uma conta de automatização que executa o runbook ou criar facilmente uma conta.

Neste artigo, vamos mostrar-lhe como é fácil configurar um alerta de VM do Azure e um runbook de automatização para ser executado sempre que o alerta é acionado. Os cenários de exemplo incluem reiniciar uma VM quando a utilização da memória exceder algumas limiar devido a uma aplicação na VM com uma fuga de memória ou a parar uma VM quando o tempo de utilizador da CPU foi inferior a 1% para as últimas horas e não está em utilização. Também vamos explicar como a criação automática de um serviço principal na sua conta de automatização simplifica a utilização de runbooks em remediação de alerta do Azure.

## <a name="create-an-alert-on-a-vm"></a>Criar um alerta numa VM
Execute os seguintes passos para configurar um alerta para iniciar um runbook quando o limiar foi cumprida.

> [!NOTE]
> Com esta versão, iremos só suportam máquinas de virtuais V2 e suporte para clássico que VMS serão adicionadas em breve.  
> 
> 

1. Inicie sessão no portal do Azure e clique em **máquinas virtuais**.  
2. Selecione uma das suas máquinas virtuais.  
3. No ecrã da VM, no **monitorização** secção, clique em **regras de alerta**.
4. No **regras de alerta** painel, clique em **Adicionar alerta**.

Isto abre-se a **adicionar uma regra de alerta** página, onde pode configurar as condições para o alerta e escolha entre uma ou todas estas opções: enviar e-mail para alguém, utilize um webhook para reencaminhar o alerta para outro sistema de e/ou executar um O runbook de automatização em resposta tentar corrigir o problema.

## <a name="configure-a-runbook"></a>Configurar um runbook
Para configurar um runbook seja executado quando é cumprido o limiar de alerta de VM, selecione **Runbook de automatização**. No **configurar runbook** painel, pode selecionar o runbook seja executado e a conta de automatização para executar o runbook.

![Configurar um runbook da automatização e criar uma nova conta de automatização](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> Para esta versão pode escolher entre três runbooks que fornece o serviço – VM reiniciar, pare a VM ou VM remover (eliminar).  A capacidade de selecionar um dos seus próprios runbooks ou outros runbooks estará disponível numa versão futura.
> 
> 

![A escolha de Runbooks](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

Depois de selecionar uma das três runbooks disponíveis, o **conta de automatização** aparece na lista pendente e pode selecionar o runbook será executado como uma conta de automatização. Os Runbooks precisam de ser executado em contexto de um [conta de automatização](automation-security-overview.md) que está na sua subscrição do Azure. Pode selecionar uma conta de automatização que já criou ou pode ter uma conta de automatização novo criada para si.

Os runbooks que são fornecidos autenticados no Azure utilizando um principal de serviço. Se optar por executar o runbook de uma das suas contas de automatização existentes, iremos criar automaticamente o serviço principal para si. Se optar por criar uma nova conta de automatização, em seguida, iremos criar automaticamente a conta e o principal de serviço. Em ambos os casos, os dois recursos também são criados na conta de automatização – um recurso de certificado com o nome **AzureRunAsCertificate** e um recurso de ligação com o nome **AzureRunAsConnection**. Utilize os runbooks **AzureRunAsConnection** para autenticar com o Azure para efetuar a ação de gestão contra a VM.

> [!NOTE]
> O principal de serviço é criado no âmbito da subscrição e é atribuído a função de contribuinte. Esta função é necessário para a conta para ter permissão para executar runbooks de automatização para gerir VMs do Azure.  A criação de uma conta de Automaton e/ou principal de serviço é um evento de uso individual. Assim que forem criadas, pode utilizar essa conta para executar runbooks para outros alertas de VM do Azure.
> 
> 

Ao clicar em **OK** o alerta está configurado e se tiver selecionado a opção para criar uma nova conta de automatização, é criada, juntamente com o principal de serviço.  Esta operação pode demorar alguns segundos a concluir.  

![Runbook que está a ser configurada](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

Uma vez concluída a configuração, consulte o nome do runbook no **adicionar uma regra de alerta** página.

![Runbook configurado](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

Clique em **OK** no **adicionar uma regra de alerta** página.  A regra de alerta é criado e ativados se a máquina virtual está num Estado de execução.

### <a name="enable-or-disable-a-runbook"></a>Ativar ou desativar um runbook
Se tiver um runbook configurado para um alerta, pode desativá-lo sem a remover a configuração do runbook. Isto permite-lhe manter o alerta em execução e talvez testar algumas das regras de alerta e, em seguida, mais tarde voltar a ativar o runbook.

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Criar um runbook que funciona com um alerta do Azure
Quando seleciona um runbook como parte de uma regra de alerta do Azure, o runbook tem de ter lógica na mesma para gerir os dados de alerta que são transmitidos ao mesmo.  Quando um runbook está configurado numa regra de alerta, um webhook é criado para o runbook; esse webhook, em seguida, é utilizada para iniciar o runbook sempre que o alerta é acionado.  A chamada real para iniciar o runbook é um pedido de HTTP POST para o URL do webhook. O corpo do pedido POST contém um objeto JSON formated que contém as propriedades úteis relacionadas com o alerta.  Como pode ver abaixo, os dados do alerta contém detalhes, como o subscriptionID, resourceGroupName, resourceName e resourceType.

### <a name="example-of-alert-data"></a>Exemplo de dados de alertas
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

Quando o serviço de webhook de automatização recebe o HTTP POST extrai os dados de alerta e passa-a para o runbook no parâmetro de entrada do runbook WebhookData.  Segue-se um runbook de exemplo que mostra como utilizar o parâmetro WebhookData e extrair os dados de alertas e utilizá-lo para gerir os recursos do Azure que acionou o alerta.

### <a name="example-runbook"></a>Runbook de exemplo
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>Resumo
Quando configurar um alerta numa VM do Azure, tem agora a capacidade de configurar facilmente um runbook de automatização para realizar automaticamente a ação de remediação quando o alerta é acionado. Nesta versão, pode escolher entre runbooks para reiniciar, interromper ou eliminar uma VM dependendo do seu cenário de alerta. Esta é apenas o início de ativação de cenários em que controla as ações (a notificação, resolução de problemas, remediação) que são executadas automaticamente quando um alerta é acionado.

## <a name="next-steps"></a>Passos Seguintes
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)


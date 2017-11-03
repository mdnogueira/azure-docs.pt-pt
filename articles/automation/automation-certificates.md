---
title: "Certificado ativos na automatização do Azure | Microsoft Docs"
description: "Certificados podem ser armazenados em segurança na automatização do Azure para que possam ser acedidos por runbooks ou configurações de DSC para se autenticarem do Azure e os recursos de terceiros.  Este artigo explica os detalhes de certificados e como trabalhar com os mesmos no texto e gráficos de criação."
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: e434292485ef9da1a8e23da25ac731d9bf0177ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="certificate-assets-in-azure-automation"></a>Ativos de certificado na automatização do Azure

Certificados podem ser armazenados em segurança na automatização do Azure para que possam ser acedidos por runbooks ou configurações de DSC utilizando o **Get-AzureRmAutomationCertificate** atividade para recursos do Azure Resource Manager. Isto permite-lhe criar runbooks e configurações de DSC que utilizam certificados para autenticação ou adiciona-os para o Azure ou recursos de terceiros.

> [!NOTE] 
> Proteger recursos na automatização do Azure incluem as credenciais, certificados, ligações e as variáveis encriptadas. Estes elementos são encriptados e armazenados na automatização do Azure com uma chave exclusiva que é gerada para cada conta de automatização. Esta chave é encriptada por um certificado principal e armazenada na automatização do Azure. Antes de o armazenamento de um recurso seguro, a chave da conta de automatização é desencriptada utilizando o certificado principal e, em seguida, utilizado para encriptar o elemento.
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela seguinte são utilizados para criar e gerir recursos de certificado de automatização com o Windows PowerShell. Estes são enviados como parte do [módulo Azure PowerShell](../powershell-install-configure.md) que está disponível para utilização nos runbooks de automatização e configurações de DSC.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-4.3.1)|Obtém informações sobre um certificado a utilizar num runbook ou configuração de DSC. Apenas pode obter o certificado propriamente dito da atividade de Get-AutomationCertificate.|
|[Novo AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate?view=azurermps-4.3.1)|Cria um novo certificado para a automatização do Azure.|
[Remover AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate?view=azurermps-4.3.1)|Remove um certificado da automatização do Azure.|Cria um novo certificado para a automatização do Azure.
|[Conjunto AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate?view=azurermps-4.3.1)|Define as propriedades de um certificado existente, incluindo o carregamento do ficheiro de certificado e definir a palavra-passe para um ficheiro. pfx.|
|[AzureCertificate adicionar](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Carrega um certificado de serviço para o serviço de nuvem especificada.|


## <a name="python2-functions"></a>Funções de Python2

A função na tabela seguinte é utilizada para aceder aos certificados num Python2 runbook.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_certificate | Obtém informações sobre um recurso de certificado. |

> [!NOTE]
> Tem de importar o **automationassets** módulo no início do runbook Python para poder aceder as funções de recurso.


## <a name="creating-a-new-certificate"></a>Criar um novo certificado

Quando cria um novo certificado, carregar um ficheiro. cer ou. pfx para automatização do Azure. Se marcar o certificado como exportável, pode transferi-la fora do arquivo de certificados de automatização do Azure. Se não é exportável, em seguida, esta só pode ser utilizada para assinatura dentro do runbook ou configuração de DSC.


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para criar um novo certificado com o portal do Azure

1. Da sua conta de automatização, clique em de **ativos** mosaico para abrir o **ativos** painel.
1. Clique em de **certificados** mosaico para abrir o **certificados** painel.
1. Clique em **adicionar um certificado** na parte superior do painel.
2. Escreva um nome para o certificado no **nome** caixa.
2. Clique em **selecione um ficheiro** em **carregar um ficheiro de certificado** para procurar um ficheiro. cer ou. pfx.  Se selecionar um ficheiro. pfx, especifique uma palavra-passe e se deve ser permitido para ser exportada.
1. Clique em **criar** para guardar o novo elemento de certificado.


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Para criar um novo certificado com o Windows PowerShell

O exemplo seguinte demonstra como criar um novo certificado de automatização e marcá-la exportável. Esta ação importa um ficheiro. pfx existente.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>Utilizar um certificado

Tem de utilizar o **Get-AutomationCertificate** atividade para utilizar um certificado. Não é possível utilizar o [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) cmdlet, uma vez que devolve informações sobre o recurso de certificado, mas não o certificado propriamente dito.

### <a name="textual-runbook-sample"></a>Exemplo de textual runbook

O código de exemplo seguinte mostra como adicionar um certificado para um serviço em nuvem num runbook. Neste exemplo, a palavra-passe é obtida a partir de uma variável de automatização encriptados.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Exemplo de um runbook gráfico

Adicionar um **Get-AutomationCertificate** para um runbook gráfico ao clicar no certificado no painel de biblioteca do editor gráfico e seleção de **adicionar à tela**.

![Adicionar certificado à tela](media/automation-certificates/automation-certificate-add-to-canvas.png)

A imagem seguinte mostra um exemplo de utilização de um certificado de um runbook gráfico.  Este é o mesmo exemplo mostrado acima para adicionar um certificado para um serviço em nuvem a partir de um runbook textual.

![Criação de gráficos de exemplo ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Exemplo de Python2
O exemplo seguinte mostra como aceder a certificados em Python2 runbooks.

    # get a reference to the Azure Automation certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    
    # returns the binary cert content  
    print cert 

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre como trabalhar com ligações para controlar o fluxo lógico de atividades de runbook foi concebido para efetuar, consulte [nas hiperligações na criação de gráficos](automation-graphical-authoring-intro.md#links-and-workflow). 

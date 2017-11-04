---
title: "Recursos de credencial na automatização do Azure | Microsoft Docs"
description: "Recursos de credencial na automatização do Azure contêm as credenciais de segurança que podem ser utilizadas para autenticar em recursos acedidos pelo runbook ou a configuração de DSC. Este artigo descreve como criar ativos de credenciais e utilizá-los num runbook ou configuração de DSC."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 3209bf73-c208-425e-82b6-df49860546dd
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: bwren
ms.openlocfilehash: 70f6272d936b097a9458dfa18279a70727da9d6b
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="credential-assets-in-azure-automation"></a>Recursos de credencial na automatização do Azure
Um recurso de credencial de automatização contém um [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) objeto que contém as credenciais de segurança, tais como um nome de utilizador e palavra-passe. Configurações de Runbooks e DSC podem utilizar os cmdlets que aceite um objeto PSCredential para autenticação ou poderão extrair o nome de utilizador e palavra-passe do objeto PSCredential para fornecer a algumas aplicações ou serviço que requer autenticação. As propriedades das credenciais são armazenadas em segurança na automatização do Azure e podem ser acedidas no runbook ou a configuração de DSC com o [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) atividade.

> [!NOTE]
> Proteger recursos na automatização do Azure incluem as credenciais, certificados, ligações e as variáveis encriptadas. Estes elementos são encriptados e armazenados na automatização do Azure com uma chave exclusiva que é gerada para cada conta de automatização. Esta chave é encriptada por um certificado principal e armazenada na automatização do Azure. Antes de o armazenamento de um recurso seguro, a chave da conta de automatização é desencriptada utilizando o certificado principal e, em seguida, utilizado para encriptar o elemento.  

## <a name="azure-classic-powershell-cmdlets"></a>Cmdlets do PowerShell clássico do Azure
Os cmdlets na tabela seguinte são utilizados para criar e gerir recursos de credencial de automatização com o Windows PowerShell.  Estes são enviados como parte do [módulo Azure PowerShell](/powershell/azure/overview) que está disponível para utilização nos runbooks de automatização e configurações de DSC.

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Obtém informações sobre um recurso de credencial. Apenas pode obter a credencial próprio da **Get-AutomationPSCredential** atividade. |
| [Novo AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Cria uma credencial de automatização novo. |
| [Remove - AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Remove uma credencial de automatização. |
| [Set - AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Define as propriedades para uma credencial de automatização existente. |

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do AzureRM PowerShell
Para AzureRM, os cmdlets na tabela seguinte são utilizados para criar e gerir recursos de credencial de automatização com o Windows PowerShell.  Estes são enviados como parte do [AzureRM.Automation módulo](/powershell/azure/overview) que está disponível para utilização nos runbooks de automatização e configurações de DSC.

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Obtém informações sobre um recurso de credencial.  |
| [Novo AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Cria uma credencial de automatização novo. |
| [Remover AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Remove uma credencial de automatização. |
| [Conjunto AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Define as propriedades para uma credencial de automatização existente. |

## <a name="runbook-activities"></a>Atividades do Runbook
As atividades na tabela seguinte são utilizadas para aceder a credenciais num runbook e configurações de DSC.

| Atividades | Descrição |
|:--- |:--- |
| Get-AutomationPSCredential |Obtém uma credencial para utilizar num runbook ou configuração de DSC. Devolve um [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) objeto. |

> [!NOTE]
> Deve evitar utilizar variáveis no parâmetro – Name de Get-AutomationPSCredential, uma vez que esta situação pode dificultar a deteção de dependências entre runbooks ou configurações de DSC e recursos da credencial no momento da concepção.

## <a name="python2-functions"></a>Funções de Python2
A função na tabela seguinte é utilizada para aceder às credenciais num Python2 runbook.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_credential | Obtém informações sobre um recurso de credencial. |

> [!NOTE]
> Tem de importar o módulo "automationassets" na parte superior do Python runbook para poder aceder as funções de recurso.

## <a name="creating-a-new-credential-asset"></a>Criar um novo recurso de credencial

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Para criar um novo recurso de credencial com o portal do Azure
1. Da sua conta de automatização, clique em de **ativos** parte para abrir o **ativos** painel.
2. Clique em de **credenciais** parte para abrir o **credenciais** painel.
3. Clique em **adicionar uma credencial** na parte superior do painel.
4. Preencha o formulário e clique em **criar** para guardar a credencial de novo.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Para criar um novo recurso de credencial com o Windows PowerShell
Os comandos de exemplo seguintes mostram como criar uma nova credencial de automatização. Um objeto PSCredential é criado pela primeira vez com o nome e a palavra-passe e, em seguida, utilizado para criar o recurso de credencial. Em alternativa, pode utilizar o **Get-Credential** cmdlet para ser-lhe pedido que introduza um nome e a palavra-passe.

    $user = "MyDomain\MyUser"
    $pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
    $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
    New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

### <a name="to-create-a-new-credential-asset-with-the-azure-classic-portal"></a>Para criar um novo recurso de credencial com o portal clássico do Azure
1. Da sua conta de automatização, clique em **ativos** na parte superior da janela.
2. Na parte inferior da janela, clique em **Adicionar definição**.
3. Clique em **adicionar credencial**.
4. No **tipo de credencial** lista pendente, selecione **credencial do PowerShell**.
5. Conclua o assistente e clique na caixa de verificação para guardar a credencial de novo.

## <a name="using-a-powershell-credential"></a>Utilizando uma credencial de PowerShell
Obter um recurso de credencial num runbook ou configuração de DSC com o **Get-AutomationPSCredential** atividade. Esta ação devolve uma [objeto PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que pode utilizar com uma atividade ou um cmdlet que necessita de um parâmetro de PSCredential. Também pode obter as propriedades do objeto de credencial para utilizar individualmente. O objeto tem uma propriedade para o nome de utilizador e a palavra-passe segura, ou pode utilizar o **GetNetworkCredential** método para devolver um [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) objeto que fornecerá uma versão não segura a palavra-passe.

### <a name="textual-runbook-sample"></a>Exemplo de textual runbook
Os comandos de exemplo seguintes mostram como utilizar uma credencial do PowerShell num runbook. Neste exemplo, a credencial é obtida e o nome de utilizador e palavra-passe atribuídos às variáveis.

    $myCredential = Get-AutomationPSCredential -Name 'MyCredential'
    $userName = $myCredential.UserName
    $securePassword = $myCredential.Password
    $password = $myCredential.GetNetworkCredential().Password


### <a name="graphical-runbook-sample"></a>Exemplo de um runbook gráfico
Adicionar um **Get-AutomationPSCredential** atividade para um runbook gráfico ao clicar na credencial no painel de biblioteca do editor gráfico e seleção de **adicionar à tela**.

![Adicionar credencial à tela](media/automation-credentials/credential-add-canvas.png)

A imagem seguinte mostra um exemplo de como utilizar uma credencial num runbook gráfico.  Neste caso, está a ser utilizado para fornecer autenticação para um runbook para recursos do Azure, conforme descrito em [autenticar Runbooks com a conta de utilizador do Azure AD](automation-create-aduser-account.md).  A primeira atividade obtém a credencial que tem acesso à subscrição do Azure.  O **Add-AzureAccount** atividade, em seguida, utiliza esta credencial para fornecer autenticação para todas as atividades que pode segui-lo.  A [ligação de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) está aqui porque **Get-AutomationPSCredential** está à espera de um único objeto.  

![Adicionar credencial à tela](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Utilizar uma credencial do PowerShell DSC
Enquanto as configurações de DSC na automatização do Azure podem referenciar ativos de credenciais utilizando **Get-AutomationPSCredential**, ativos de credenciais podem também ser transmitidos através dos parâmetros, se assim o desejar. Para obter mais informações, consulte [compilar configurações de DSC de automatização do Azure](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Utilizar as credenciais no Python2
O exemplo seguinte mostra um exemplo de aceder a credenciais em Python2 runbooks.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a credential
    cred = automationassets.get_automation_credential("credtest")
    print cred["username"]
    print cred["password"]

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre ligações na criação de gráficos, consulte o artigo [nas hiperligações na criação de gráficos](automation-graphical-authoring-intro.md#links-and-workflow)
* Para compreender os métodos de autenticação diferentes com a automatização, consulte [segurança de automatização do Azure](automation-security-overview.md)
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md) 
* Para começar com Python2 runbooks, consulte [o meu primeiro runbook Python2](automation-first-runbook-textual-python2.md) 


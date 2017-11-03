---
title: "Recursos de ligação da automatização do Azure | Microsoft Docs"
description: "Recursos de ligação da automatização do Azure contêm as informações necessárias para ligar a uma aplicação ou serviço externo, a partir de um runbook ou a configuração de DSC. Este artigo explica os detalhes das ligações e como trabalhar com os mesmos no texto e gráficos de criação."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: f0239017-5c66-4165-8cca-5dcb249b8091
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/13/2017
ms.author: magoedte; bwren
ms.openlocfilehash: f1746f4f6706835d43edc171b03d4ececfa3560c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connection-assets-in-azure-automation"></a>Recursos de ligação da automatização do Azure

Um recurso de ligação da automatização contém as informações necessárias para ligar a uma aplicação ou serviço externo a partir de um runbook ou a configuração de DSC. Isto pode incluir as informações necessárias para a autenticação como um nome de utilizador e palavra-passe para além das informações de ligação como um URL ou uma porta. O valor de uma ligação consiste em manter todas as propriedades para ligar a uma determinada aplicação num recurso em vez de criar várias variáveis. O utilizador pode editar os valores para uma ligação no local e pode transmitir o nome de uma ligação a um runbook ou a configuração de DSC num único parâmetro. As propriedades de uma ligação podem ser acedidas no runbook ou a configuração de DSC com o **Get-AutomationConnection** atividade. 

Quando criar uma ligação, tem de especificar um *tipo de ligação*. O tipo de ligação é um modelo que define um conjunto de propriedades. A ligação define valores para cada propriedade definida no respetivo tipo de ligação. Tipos de ligação são adicionados a automatização do Azure em módulos de integração ou criados com o [API de automatização do Azure](http://msdn.microsoft.com/library/azure/mt163818.aspx) se o módulo de integração inclui um tipo de ligação e é importado para a sua conta de automatização. Caso contrário, terá de criar um ficheiro de metadados para especificar um tipo de ligação de automatização.  Para obter mais informações sobre isto, consulte [módulos de integração](automation-integration-modules.md).  

>[!NOTE] 
>Proteger recursos na automatização do Azure incluem as credenciais, certificados, ligações e as variáveis encriptadas. Estes elementos são encriptados e armazenados na automatização do Azure com uma chave exclusiva que é gerada para cada conta de automatização. Esta chave é encriptada por um certificado principal e armazenada na automatização do Azure. Antes de o armazenamento de um recurso seguro, a chave da conta de automatização é desencriptada utilizando o certificado principal e, em seguida, utilizado para encriptar o elemento.

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela seguinte são utilizados para criar e gerir ligações de automatização com o Windows PowerShell. Estes são enviados como parte do [módulo Azure PowerShell](/powershell/azure/overview) que está disponível para utilização nos runbooks de automatização e configurações de DSC.

|Cmdlet|Descrição|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Obtém uma ligação. Inclui uma tabela hash com os valores dos campos da ligação.|
|[Novo AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Cria uma nova ligação.|
|[Remover AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Remove uma ligação existente.|
|[Conjunto AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Define o valor de um determinado campo para uma ligação existente.|

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são utilizadas para aceder a ligações num runbook ou configuração de DSC.

|Atividades|Descrição|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Obtém uma ligação a utilizar. Devolve uma tabela hash com as propriedades da ligação.|

>[!NOTE] 
>Deve evitar utilizar variáveis com o parâmetro – Name de **Get - AutomationConnection** , uma vez que isso pode dificultar a deteção de dependências entre runbooks ou configurações de DSC e recursos de ligação no momento da concepção.

 
## <a name="python2-functions"></a>Funções de Python2 
A função na tabela seguinte é utilizada para aceder a ligações num Python2 runbook. 

| Função | Descrição | 
|:---|:---| 
| automationassets.get_automation_connection | Obtém uma ligação. Devolve um dicionário com as propriedades da ligação. | 

> [!NOTE] 
> Tem de importar o módulo "automationassets" na parte superior do Python runbook para poder aceder as funções de recurso.

## <a name="creating-a-new-connection"></a>Criar uma nova ligação

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Para criar uma nova ligação com o portal do Azure

1. Da sua conta de automatização, clique em de **ativos** parte para abrir o **ativos** painel.
2. Clique em de **ligações** parte para abrir o **ligações** painel.
3. Clique em **adicionar uma ligação** na parte superior do painel.
4. No **tipo** lista pendente, selecione o tipo de ligação que pretende criar. O formulário irá apresentar as propriedades para esse tipo específico.
5. Preencha o formulário e clique em **criar** para guardar a nova ligação.

### <a name="to-create-a-new-connection-with-the-azure-classic-portal"></a>Para criar uma nova ligação com o portal clássico do Azure

1. Da sua conta de automatização, clique em **ativos** na parte superior da janela.
2. Na parte inferior da janela, clique em **Adicionar definição**.
3. Clique em **adicionar ligação**.
4. No **tipo de ligação** lista pendente, selecione o tipo de ligação que pretende criar.  O assistente irá apresentar as propriedades para esse tipo específico.
5. Conclua o assistente e clique na caixa de verificação para guardar a nova ligação.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Para criar uma nova ligação com o Windows PowerShell

Criar uma nova ligação do Windows PowerShell com o [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) cmdlet. Este cmdlet tem um parâmetro com o nome **ConnectionFieldValues** que espera um [tabela hash](http://technet.microsoft.com/library/hh847780.aspx) definir valores para cada uma das propriedades definidas pelo tipo de ligação.

Se estiver familiarizado com a automatização [conta Run As](automation-sec-configure-azure-runas-account.md) para autenticar runbooks com o principal de serviço, o script do PowerShell, fornecido como uma alternativa ao criar a conta Run As no portal, cria uma nova ligação recurso utilizando os seguintes comandos de exemplo.  

    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 

Poderá utilizar o script para criar o recurso de ligação pois quando criar a sua conta de automatização, automaticamente inclui vários módulos global por predefinição, juntamente com o tipo de ligação **AzurServicePrincipal** para criar o **AzureRunAsConnection** recurso de ligação.  Isto é importante a ter em consideração, porque se tentar criar um novo elemento de ligação para ligar a um serviço ou aplicação com um método de autenticação diferentes, irá falhar porque o tipo de ligação já não está definido na sua conta de automatização.  Para obter mais informações sobre como criar o seu próprio tipo de ligação para o seu personalizado ou o módulo a partir de [galeria do PowerShell](https://www.powershellgallery.com), consulte [módulos de integração](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Utilizar uma ligação num runbook ou configuração DSC

Obter uma ligação num runbook ou configuração de DSC com o **Get-AutomationConnection** cmdlet.  Não é possível utilizar o [Get-AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn) atividade.  Esta atividade obtém os valores dos diferentes campos da ligação e devolve-los como um [tabela hash](http://go.microsoft.com/fwlink/?LinkID=324844) que, em seguida, pode ser utilizada com os comandos apropriados no runbook ou a configuração de DSC.

### <a name="textual-runbook-sample"></a>Exemplo de textual runbook

Os comandos de exemplo seguintes mostram como utilizar a conta executar como mencionada anteriormente, para autenticar com recursos do Azure Resource Manager no runbook.  Utiliza o recurso de ligação que representa a conta Run As, que referencia a baseada em certificado principal de serviço, não as credenciais.  

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint 

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Adicionar um **Get-AutomationConnection** atividade para um runbook gráfico ao clicar na ligação no painel de biblioteca do editor gráfico e seleção de **adicionar à tela**.

![](media/automation-connections/connection-add-canvas.png)

A imagem seguinte mostra um exemplo de utilização de uma ligação num runbook gráfico.  Este é o mesmo exemplo mostrado acima para autenticar utilizando a conta Run As com um runbook textual.  Este exemplo utiliza o **valor constante** conjunto de dados para o **obter ligação RunAs** atividade que utiliza um objeto de ligação para a autenticação.  A [ligação de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) é utilizado aqui, uma vez que o conjunto de parâmetros ServicePrincipalCertificate está à espera de um único objeto.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Exemplo de runbook Python2
O exemplo seguinte mostra como autenticar a ligação Run As a utilizar num Python2 runbook.

    """ Tutorial to show how to authenticate against Azure resource manager resources """
    import azure.mgmt.resource
    import automationassets


    def get_automation_runas_credential(runas_connection):
        """ Returns credentials to authenticate against Azure resoruce manager """
        from OpenSSL import crypto
        from msrestazure import azure_active_directory
        import adal

        # Get the Azure Automation Run As service principal certificate
        cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
        pks12_cert = crypto.load_pkcs12(cert)
        pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

        # Get Run As connection information for the Azure Automation service principal
        application_id = runas_connection["ApplicationId"]
        thumbprint = runas_connection["CertificateThumbprint"]
        tenant_id = runas_connection["TenantId"]

        # Authenticate with service principal certificate
        resource = "https://management.core.windows.net/"
        authority_url = ("https://login.microsoftonline.com/" + tenant_id)
        context = adal.AuthenticationContext(authority_url)
        return azure_active_directory.AdalAuthentication(
            lambda: context.acquire_token_with_client_certificate(
                resource,
                application_id,
                pem_pkey,
                thumbprint)
        )


    # Authenticate to Azure using the Azure Automation Run As service principal
    runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
    azure_credential = get_automation_runas_credential(runas_connection)

## <a name="next-steps"></a>Passos seguintes

- Reveja [nas hiperligações na criação de gráficos](automation-graphical-authoring-intro.md#links-and-workflow) para compreender como direcionar e controlar o fluxo de lógica nos runbooks.  

- Para saber mais sobre a utilização da automatização do Azure de módulos do PowerShell e melhores práticas para criar os seus próprios módulos do PowerShell para funcionar como módulos de integração na automatização do Azure, consulte [módulos de integração](automation-integration-modules.md).  

<properties
    pageTitle="Configurar Conta Run As do Azure | Microsoft Azure"
    description="Tutorial que o orienta através da criação, teste e exemplo de utilização da autenticação principal de segurança na Automatização do Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="service principal name, setspn, azure authentication"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/07/2016"
    ms.author="magoedte"/>

# Autenticar Runbooks com a conta Run As do Azure
Este tópico mostra como configurar uma conta de Automatização do portal do Azure, utilizando a nova funcionalidade da conta Run as (também referida como um principal de serviço) para aceder aos recursos do Azure Resource Manager na sua subscrição com runbooks de Automatização.  Quando cria uma nova conta de Automatização no portal do Azure, cria automaticamente um novo principal de serviço atribuído à função de controlo de acesso baseado em funções (RBAC) do Contribuidor na subscrição por predefinição.  Isto simplifica o processo para si e ajuda-o a começar rapidamente a criar e implementar runbooks para suportar as suas necessidades de automatização.      

Com um principal de serviço, pode:

* Fornecer uma forma normalizada de autenticar no Azure ao gerir os recursos do Azure Resource Manager através de runbooks
* Automatizar a utilização de runbooks globais configurados nos Alertas do Azure


>[AZURE.NOTE] A [Funcionalidade de integração de alertas](../azure-portal/insights-receive-alert-notifications.md) do Azure com Runbooks Globais de Automatização requer uma conta de Automatização que esteja configurada com um principal de serviço. Pode selecionar uma conta de Automatização que já tem um principal de serviço definido pelo utilizador ou optar por criar um novo.



Vamos mostrar como criar a conta de Automatização a partir do portal do Azure e atualizar uma conta com uma conta Run As através do Azure PowerShell e como se autenticar nesse principal de serviço nos runbooks.  

## Criar uma nova Conta de Automatização a partir do Portal do Azure
Nesta secção, é necessário executar os seguintes passos para criar uma nova conta de Automatização do Azure e o principal de serviço a partir do portal do Azure.

>[AZURE.NOTE] O utilizador que efetua estes passos *tem* de ser um membro da função de 	Administradores da subscrição.

1. Inicie sessão no portal do Azure como um administrador de serviços para a subscrição do Azure que pretende gerir.
2. Selecione **Contas de Automatização**.
3. No painel Contas de Automatização, clique em **Adicionar**.<br>![Adicionar Conta de Automatização](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. No painel **Adicionar Conta de Automatização**, no tipo de caixa **Nome**, escreva um nome para a sua nova conta de Automatização.
5. Se tiver mais do que uma subscrição, especifique a subscrição para a nova conta, bem como um **Grupo de recursos** novo ou existente e uma **localização** do datacenter do Azure.
6. Verifique se valor **Sim** está selecionado para a opção **Criar conta Run As do Azure** e clique no botão **Criar**.  

    ![Adicionar Aviso de Conta de Automatização](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] Se optar por não criar a conta Run As, selecionando a opção **Não**, será apresentada uma mensagem de aviso no painel **Adicionar Conta de Automatização**.  Enquanto a conta é criada e atribuída à função **Contribuidor** na subscrição, não terá uma identidade de autenticação correspondente dentro do seu serviço de diretório de subscrições e, por isso, sem recursos de acesso na sua subscrição.  Isto irá impedir que todos os runbooks que referenciam esta conta a partir da capacidade autenticar e executar tarefas relativamente aos recursos do Azure Resource Manager.

    ![Adicionar Aviso de Conta de Automatização](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] Se receber uma mensagem de erro de permissão negada depois de clicar no botão **Criar**, isto acontece porque a conta não é um membro da função de administradores da Subscrição.  

7. Enquanto o Azure cria a conta de automatização, pode acompanhar o progresso em **Notificações** a partir do menu.

### Recursos incluídos
Quando a criação da conta de automatização estiver concluída, vários recursos são automaticamente criados. Estes estão resumidos na tabela abaixo.

Recurso|Descrição 
----|----
Runbook AzureAutomationTutorial|Um runbook de exemplo que demonstra como efetuar a autenticação com a conta Run As e apresentar as primeiras 10 VMs do Azure na sua subscrição.
AzureRunAsCertificate|Recurso de certificado criado se optou por ter a conta Run As criada durante a criação da conta de automatização ou utilizou o script do PowerShell abaixo para uma conta existente.  Este certificado tem um tempo de vida de um ano. 
AzureRunAsConnection|O recurso de ligação foi criado se optou por ter a conta Run As criada durante a criação da conta de Automatização ou a utilização do script do PowerShell abaixo para uma conta existente.
Módulos|15 módulos com cmdlets para o Azure, PowerShell e Automatização para começar a utilizar imediatamente nos runbooks.  

## Atualizar uma Conta de Automatização com o PowerShell
O procedimento abaixo atualiza uma conta de Automatização existente e cria o principal de serviço com o PowerShell.  Este procedimento é necessário se tiver criado uma conta, mas recusou-se a criar a conta Run As.

Antes de continuar, verifique o seguinte:

1. Transferiu e instalou [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) se estiver a executar o Windows 7.   
    Se estiver a executar o Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 e Windows 7 SP1, o [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) está disponível para instalação.
2. Azure PowerShell 1.0. Para obter informações sobre esta versão e como a instalar, consulte o artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). 
3. Criou uma conta de automatização.  Esta conta vai ser referenciada como o valor para os parâmetros -AutomationAccountName e -ApplicationDisplayName no script abaixo.


O script do PowerShell irá configurar o seguinte:

* Uma aplicação do Azure AD que será autenticada com o certificado autoassinado, crie uma conta do principal de serviço para esta aplicação no Azure AD e atribuída a função Contribuidor (pode alterar isto para Proprietário ou função) para esta conta na sua subscrição atual.  Para obter mais informações, consulte o [Controlo de acesso baseado em funções na Automatização do Azure](../automation/automation-role-based-access-control.md).  
* Um recurso de certificado de Automatização na conta de automatização especificada com o nome **AzureRunAsCertificate**, que contém o certificado utilizado no principal de serviço.
* Um recurso de ligação de Automatização na conta de automatização especificada com o nome **AzureRunAsConnection**, que contém applicationId, tenantId, subscriptionId e o thumbprint do certificado.  


### Executar o script do PowerShell

1. Guarde o seguinte script no seu computador.  Neste exemplo, guarde-o com o nome de ficheiro **New-AzureServicePrincipal.ps1**.  

    ```
    #Requires -RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory=$true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,

    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )

    Login-AzureRmAccount
    Import-Module AzureRM.Resources
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

    $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

    $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
    $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

    $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
    $KeyCredential.StartDate = $CurrentDate
    $KeyCredential.EndDate= $EndDate
    $KeyCredential.KeyId = $KeyId
    $KeyCredential.Type = "AsymmetricX509Cert"
    $KeyCredential.Usage = "Verify"
    $KeyCredential.Value = $KeyValue

    # Use Key credentials
    $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. No seu computador, inicie o **Windows PowerShell** a partir do ecrã **Iniciar** ecrã com direitos de utilizador elevados.
3. A partir da shell de linha de comandos do PowerShell elevada, navegue para a pasta que contém o script criado no Passo 1 e execute o script alterando os valores para os parâmetros *– ResourceGroup*, *- AutomationAccountName*, *- ApplicationDisplayName*, *- SubscriptionId* e *- CertPlainPassword*.<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -SubscriptionId <SubscriptionId> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] Será solicitado para autenticar com o Azure, depois de executar o script.   *Tem de* iniciar sessão com uma conta que seja um Administrador de serviço na subscrição.  
<br>
4. Após a conclusão do script com êxito, avance para a secção seguinte para testar e verificar a nova configuração de credenciais.

### Verificar autenticação
Em seguida, iremos efetuar um pequeno teste para confirmar se é possível efetuar a autenticação com êxito utilizando o novo principal de serviço. Se não for possível autenticar-se com êxito, volte ao Passo 1 e confirme cada um dos passos anteriores novamente.    

1. No Portal do Azure, abra a conta de Automatização criada anteriormente.  
2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.
3. Crie um novo runbook ao clicar no botão **Adicionar um runbook** e, em seguida, no painel **Adicionar Runbook**, selecione **Criar um runbook novo**.
4. Atribua ao runbook o nome *Test-SecPrin-Runbook* e selecione o PowerShell para o **Tipo de Runbook**.  Clique em **Criar** para criar o runbook.
5. No painel **Editar Runbook do PowerShell**, cole o seguinte código na tela:<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ```  
<br>
6. Guarde o runbook ao clicar em **Guardar**.
7. Clique em **Painel de teste** para abrir o painel de **Teste**.
8. Clique em **Iniciar** para iniciar o teste.
9. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado no painel.  
10. O estado da tarefa começará como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na nuvem fique disponível. Em seguida, irá mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.  
11. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No nosso caso, deveremos ver o estado **Concluído**.<br> ![Teste do Runbook de Principal de Segurança](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. Feche o painel de **Teste** para voltar à tela.
13. Feche o painel **Editar Runbook do PowerShell**.
14. Feche o painel **Test-SecPrin-Runbook**.

## Código de exemplo para autenticar com recursos do Resource Manager

Pode utilizar o código de exemplo atualizado abaixo, retirado do runbook de exemplo AzureAutomationTutorial, para efetuar a autenticação com a conta Run As para gerir os recursos do Resource Manager com os runbooks. 

   ```
   $connectionName = "AzureRunAsConnection"
   $SubId = Get-AutomationVariable -Name 'SubscriptionId'
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
      "Setting context to a specific subscription"   
      Set-AzureRmContext -SubscriptionId $SubId          
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
   ```

O script inclui duas linhas adicionais de código para suportar a referência a um contexto de subscrição para que possa trabalhar facilmente entre várias subscrições. Um recurso variável com o nome SubscriptionId contém o ID da subscrição e, após a declaração de cmdlet Add-AzureRmAccount, o [cmdlet Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) é declarado com o conjunto de parâmetros *-SubscriptionId*. Se o nome da variável for demasiado genérico, pode rever o nome da variável para incluir um prefixo ou outra convenção de nomenclatura para ser mais fácil de identificar para os fins pretendidos. Em alternativa, pode utilizar o parâmetro definido -SubscriptionName em vez de -SubscriptionId com um recurso de variável correspondente.  

## Passos Seguintes
- Para mais informações sobre Principais de Serviço, consulte a [Objetos da Aplicação e Objetos de Principais de Serviço](../active-directory/active-directory-application-objects.md).
- Para obter mais informações sobre o Controlo de Acesso baseado em Funções na Automatização do Azure, consulte o [Controlo de acesso baseado em funções da Automatização do Azure](../automation/automation-role-based-access-control.md).



<!--HONumber=Jun16_HO2-->



---
title: Configurar uma Conta Run As do Azure | Microsoft Docs
description: "Este tutorial orienta-o ao longo da criação, do teste e do exemplo de utilização da autenticação principal de segurança na Automatização do Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "nome principal do serviço, setspn, autenticação do azure"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
ROBOTS: NOINDEX
redirect_url: /azure/automation/automation-offering-get-started
redirect_document_id: TRUE
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 90570886b3a1ae0c48683691cb438b5a61195d76
ms.lasthandoff: 04/27/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Autenticar runbooks com uma conta Run As do Azure
Este artigo mostra-lhe como configurar uma conta de Automatização do Azure no portal do Azure. Para tal, vai utilizar a funcionalidade de conta Run As para autenticar runbooks que gerem recursos no Azure Resource Manager ou na Gestão do Serviço do Azure.

Quando cria uma conta de Automatização no portal do Azure, são criadas automaticamente duas contas.

* Uma conta Run As. Esta conta cria um principal de serviço no Azure Active Directory (Azure AD) e um certificado. Também atribui o controlo de acesso baseado em funções (RBAC) Contribuinte, que gere os recursos do Resource Manager mediante a utilização de runbooks.
* Uma conta Run As Clássica. Esta conta carrega um certificado de gestão, que é utilizado para gerir recursos da Gestão do Serviço ou clássicos mediante a utilização de runbooks.

A criação de uma conta de Automatização simplifica o processo para si e ajuda-o a começar rapidamente a criar e implementar runbooks para suportar as suas necessidades de automatização.

Com contas Run As e Run As Clássica, pode:

* Proporcionar uma forma normalizada de autenticar com o Azure quando gere os recursos do Azure Resource Manager ou da Gestão do Serviço através de runbooks no portal do Azure.
* Automatizar a utilização de runbooks globais, que pode configurar nos Alertas do Azure.

> [!NOTE]
> A [funcionalidade de integração de Alertas do Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) com Runbooks globais da Automatização requer uma conta de Automatização que esteja configurada com uma conta Run As e uma conta Run As clássica. Pode selecionar uma conta de Automatização que já tenha contas Run As e Run As clássica definidas ou optar por criar uma conta de Automatização nova.
>  

Este artigo mostra como criar uma conta de Automatização a partir do portal do Azure, atualizar uma conta de Automatização com o Azure PowerShell, gerir a configuração da conta e autenticar nos seus runbooks.

Antes de começar a criar uma conta de Automatização, é importante compreender e ter em conta o seguinte:

* A criação de uma conta de Automatização não afeta as contas de Automatização que possa já ter criado, quer no modelo de implementação Resource Manager ou clássica.
* O processo funciona apenas para contas de Automatização que criar no portal do Azure. Tentar criar uma conta a partir do portal clássico do Azure não replica a configuração da conta Run As.
* Se já tiver runbooks e recursos (como agendas ou variáveis) em funcionamento para gerir recursos clássicos e pretender que os runbooks se autentiquem com a conta Run As Clássica nova, siga um dos procedimentos abaixo:

  * Para criar uma conta Run As Clássica, siga as instruções na secção “Gerir a conta Run As”. 
  * Para atualizar a sua conta já existente, utilize o script do PowerShell na secção “Atualizar a conta de Automatização com o PowerShell”.
* Para autenticar com a conta de Automatização Run As nova e a conta Run As Clássica, tem de modificar os runbooks existentes com o código de exemplo fornecido na secção [Exemplos de código de autenticação](#authentication-code-examples).

    >[!NOTE]
    >A conta Run As destina-se a autenticação em recursos do Resource Manager, mediante a utilização do principal de serviço baseado em certificados. A conta Run As Clássica destina-se a autenticação em recursos da Gestão do Serviço com certificados de gestão.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Criar uma conta de Automatização a partir do portal do Azure
Nesta secção, vai criar uma conta de Automatização do Azure a partir do portal do Azure, o que, por sua vez, criar uma conta Run As e uma conta Run As Clássica.

>[!NOTE]
>Para criar uma conta de Automatização, tem de ser membro da função Administradores de Serviço ou o coadministrador da subscrição que está a conceder acesso à subscrição. Também tem de ser adicionado como utilizador à instância predefinida do Active Directory dessa subscrição. Não é necessário atribuir uma função privilegiada à conta.
>
>Se não for membro da instância do Active Directory da subscrição antes de ser adicionado à função de coadministrador da mesma, será adicionado ao Active Directory como convidado. Neste caso, receberá o aviso "Não tem permissões para criar...", no painel **Adicionar Conta de Automatização**.
>
>Os utilizadores que foram adicionados primeiro à função de coadministrador podem ser removidos da instância do Active Directory da subscrição e adicionados novamente, para que se tornem em Utilizadores completos no Active Directory. Esta situação pode ser verificada a partir do painel **Azure Active Directory**, no portal do Azure, ao selecionar **Utilizadores e grupos**, **Todos os utilizadores** e, depois de selecionar o utilizador específico, selecionar **Perfil**. O valor do atributo **Tipo de utilizador** sob o perfil de utilizadores não deve ser igual a **Convidado**.
>

1. Inicie sessão no portal do Azure com uma conta que seja membro da função de administradores da subscrição e coadministrador da subscrição.

2. Selecione **Contas de Automatização**.

3. No painel **Contas de Automatização**, clique em **Adicionar**.
É aberto o painel **Adicionar Conta de Automatização**.

 ![O painel “Adicionar Conta de Automatização”](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Se a sua conta não for membro da função de administradores da subscrição e coadministradora da subscrição, é apresentado o aviso seguinte no painel **Adicionar Conta de Automatização**:
   >
   >![Adicionar Aviso de Conta de Automatização](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. No painel **Adicionar Conta de Automatização**, na caixa **Nome**, escreva um nome para a sua conta de Automatização nova.

5. Se tiver mais do que uma subscrição, faça o seguinte:

    a. Em **Subscrição**, especifique uma para a conta nova.

    b. Em **Grupo de Recursos**, clique em **Criar nova** ou **Utilizar existente**.

    c. Em **Localização**, especifique um datacenter do Azure.

6. Em **Criar conta Run As do Azure**, selecione **Sim** e, em seguida, clique em **Criar**.

   > [!NOTE]
   > Se selecionar **Não** e optar por não criar a conta Run As, é apresentada uma mensagem de aviso no painel **Adicionar Conta de Automatização**. Embora a conta seja criada no portal do Azure, não tem uma identidade de autenticação correspondente no seu serviço de diretório de subscrições clássico ou do Resource Manager. Consequentemente, a conta não tema cesso aos recursos da sua subscrição. Este cenário impede que qualquer runbook que referencie esta conta se autentique e realize tarefas nos recursos nestes modelos de implementação.
   >
   > ![Mensagem de aviso no painel “Adicionar Conta de Automatização”](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > Além disso, uma vez que o principal de serviço não é criado, a função Contribuinte não é atribuída.
   >

7. Enquanto o Azure cria a conta de automatização, pode acompanhar o progresso em **Notificações** a partir do menu.

### <a name="resources"></a>Recursos
Quando a conta de Automatização é criada com sucesso, são criados vários recursos automaticamente para si. Os recursos estão resumidos nas duas tabelas seguintes:

#### <a name="run-as-account-resources"></a>Recursos da conta Run As

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial | Um runbook gráfico de exemplo que demonstra como fazer a autenticação com a conta Run As e que obtém todos os recursos do Resource Manager. |
| AzureAutomationTutorialScript Runbook | Um runbook do PowerShell de exemplo que demonstra como fazer a autenticação com a conta Run As e que obtém todos os recursos do Resource Manager. |
| AzureRunAsCertificate | O recurso de certificado que é criado automaticamente quando cria uma conta de Automatização ou utiliza o script do PowerShell seguinte para uma conta já existente. O certificado permite-lhe autenticar com o Azure, para que possa gerir recursos do Azure Resource Manager a partir dos runbooks. O certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection | O recurso de ligação que é criado automaticamente quando cria uma conta de Automatização ou utiliza o script do PowerShell para uma conta já existente. |

#### <a name="classic-run-as-account-resources"></a>Recursos da conta Run As Clássica

| Recurso | Descrição |
| --- | --- |
| AzureClassicAutomationTutorial Runbook | Um runbook gráfico de exemplo que obtém todas as VMs que são criadas com o modelo de implementação clássica numa subscrição, utilizando a conta Run As Clássica (certificado) e que, em seguida, escreve o nome e o estado da VM. |
| AzureClassicAutomationTutorial Script Runbook | Um runbook do PowerShell de exemplo que obtém todas as VMs clássicas numa subscrição, utilizando a conta Run As Clássica (certificado) e, em seguida, escreve o nome e o estado da VM. |
| AzureClassicRunAsCertificate | O recurso de certificado criado automaticamente que utiliza para autenticar com o Azure, para que possa gerir os recursos clássicos do Azure a partir de runbooks. O certificado tem um tempo de vida de um ano. |
| AzureClassicRunAsConnection | O recurso de ligação criado automaticamente que utiliza para autenticar com o Azure, para que possa gerir os recursos clássicos do Azure a partir de runbooks. |

## <a name="verify-run-as-authentication"></a>Verificar a autenticação Run As
Faça um pequeno teste para confirmar que consegue autenticar com êxito através da utilização da conta Run As nova.

1. No portal do Azure, abra a conta de Automatização que criou anteriormente.

2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.

3. Selecione o runbook **AzureAutomationTutorialScript** e, em seguida, clique em **Iniciar**, para iniciar o runbook. Ocorrerem os seguintes eventos:
 * É criada uma [tarefa do runbook](automation-runbook-execution.md), é apresentado o painel **Tarefa** e o estado da tarefa é apresentado no mosaico **Resumo da Tarefa**.
 * O estado da tarefa começa como **Em fila**, com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível.
 * Quando uma função de trabalho reivindicar a tarefa, o estado muda para **A iniciar**.
 * Quando a execução do runbook é iniciada, o estado muda para **Em execução**.
 * Depois de concluída a execução da tarefa de runbook, deverá ver o estado **Concluída**.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Para ver os resultados detalhados do runbook, clique no mosaico **Saída**.  
É apresentado o painel **Saída**, que mostra que o runbook foi autenticado com êxito e devolveu uma lista de todos os recursos disponíveis no grupo de recursos.

5. Feche o painel **Saída** para voltar para o painel **Resumo da Tarefa**.

6. Feche o painel **Resumo da Tarefa** e o painel do runbook **AzureAutomationTutorialScript** correspondente.

## <a name="verify-classic-run-as-authentication"></a>Certifique-se de autenticação Run As clássica
Faça um pequeno teste semelhante para confirmar que consegue autenticar com êxito através da utilização da conta Run As Clássica nova.

1. No portal do Azure, abra a conta de Automatização que criou anteriormente.

2. Clique no mosaico **Runbooks** para abrir a lista de runbooks.

3. Selecione o runbook **AzureClassicAutomationTutorialScript** e, em seguida, clique em **Iniciar**, para iniciar o runbook. Ocorrerem os seguintes eventos:

 * É criada uma [tarefa do runbook](automation-runbook-execution.md), é apresentado o painel **Tarefa** e o estado da tarefa é apresentado no mosaico **Resumo da Tarefa**.
 * O estado da tarefa começa como **Em fila**, com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível.
 * Quando uma função de trabalho reivindicar a tarefa, o estado muda para **A iniciar**.
 * Quando a execução do runbook é iniciada, o estado muda para **Em execução**.
 * Depois de concluída a execução da tarefa de runbook, deverá ver o estado **Concluída**.

    ![Teste do Runbook de Principal de Segurança](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Para ver os resultados detalhados do runbook, clique no mosaico **Saída**.  
É apresentado o painel **Saída**, que mostra que o runbook foi autenticado com êxito e devolveu uma lista de todas as VMs clássicas na subscrição.

5. Feche o painel **Saída** para voltar para o painel **Resumo da Tarefa**.

6. Feche o painel **Resumo da Tarefa** e o painel do runbook **AzureAutomationTutorialScript** correspondente.

## <a name="managing-your-run-as-account"></a>Gerir a conta Run As
Num dado momento antes de a sua conta de Automatização expirar, vai ter de renovar o certificado. Se considerar que a conta Run As tiver sido comprometida, pode eliminá-la e voltar a criá-la. Esta secção mostra como executar estas operações.

### <a name="self-signed-certificate-renewal"></a>Renovação de certificado autoassinado
O certificado autoassinado que criou para a conta Run As expira ao fim de um ano após a data de criação. Pode renová-lo em qualquer altura antes de expirar. Quando o renovar, o certificado atual válido é mantido, para garantir que todos os runbooks que se encontram em fila ou ativamente em execução, e que se autenticam com a conta Run As, não são afetados negativamente. O certificado permanece válido até à data de expiração.

> [!NOTE]
> Se tiver configurado a sua conta Run As de Automatização para utilizar um certificado emitido pela sua autoridade de certificação empresarial e utilizar esta opção, esse certificado empresarial será substituído por um certificado autoassinado.

Para renovar o certificado, faça o seguinte:

1. No portal do Azure, abra a conta de Automatização.

2. No painel **Conta de Automatização**, no painel **Propriedades da conta**, em **Definições da Conta**, selecione **Contas Run As**.

    ![Painel Propriedades da conta de automatização](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. No painel de propriedades das **Contas Run As**, selecione a conta Run As ou a conta Run As Clássica para a qual pretende renovar o certificado.

4. No painel **Propriedades** da conta selecionada, clique em **Renovar certificado**.

    ![Renovar certificado da conta Run As](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. Enquanto o certificado estiver a ser renovado, pode acompanhar o progresso em **Notificações** a partir do menu.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminar uma conta Run As ou Run As Clássica
Esta secção descreve como eliminar e recriar uma conta Run As ou Run As Clássica. Quando executar esta ação, a conta de Automatização é mantida. Depois de eliminar uma conta Run As ou Run As Clássica, pode voltar a criá-la no portal do Azure.

1. No portal do Azure, abra a conta de Automatização.

2. No painel **Conta de Automatização**, no painel de propriedades da conta, selecione **Contas Run As**.

3. No painel de propriedades **Contas Run As**, selecione a conta Run As ou a conta Run As Clássico que quer eliminar. Em seguida, no painel **Propriedades** da conta selecionada, clique em **Eliminar**.

 ![Eliminar a conta Run As](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. Enquanto a conta estiver a ser eliminada, pode acompanhar o progresso em **Notificações** a partir do menu.

5. Assim que a conta tiver sido eliminada, pode voltar a criá-la no painel de propriedades **Contas Run As**, ao selecionar a opção de criação **Conta Run As do Azure**.

 ![Recriar a conta Run As de Automatização](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Configuração incorreta
É possível que alguns itens de configuração necessários para a conta Run As ou Run As Clássica funcionar corretamente tenham sido eliminados ou criados de forma incorreta durante a configuração inicial. Os itens incluem:

* Recurso de certificado
* Recurso de ligação
* A conta Run As foi removida da função de contribuinte
* Principal de serviço ou aplicação no Azure AD

Nas configurações incorretas anteriores e noutras, a conta de Automatização deteta as alterações e apresenta o estado *Não concluída*, no painel de propriedades **Contas Run As** da conta.

![Estado de configuração Não concluída da conta Run As](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

Quando selecionar a conta Run As, o painel **Propriedades** da conta apresenta a mensagem de erro seguinte:

![Mensagem de aviso de configuração não concluída de Run As](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

Pode resolver rapidamente estes problemas da conta Run As, ao eliminar e recriar a conta.

## <a name="update-your-automation-account-by-using-powershell"></a>Atualizar a conta de Automatização com o PowerShell
Pode utilizar o PowerShell para atualizar a sua conta de Automatização já existente se:

* Criar uma conta de Automatização, mas rejeitar criar a conta Run As.
* Já tiver uma conta de Automatização para gerir os recursos do Resource Manager e quer atualizá-la para incluir a conta Run As para autenticação de runbooks.
* Já tiver uma conta de automatização para gerir os recursos clássicos e quer atualizá-la para utilizar a Run As Clássica em vez de criar uma nova conta e migrar os runbooks e recursos para a mesma.   
* Quiser criar uma conta Run As e uma conta Run As Clássica mediante a utilização de um certificado emitido pela sua autoridade de certificação empresarial (AC).

O script tem os pré-requisitos seguintes:

* O script só pode ser executado no Windows 10 e no Windows Server 2016 com os módulos do Azure Resource Manager 2.01 e posterior. Não é suportado em versões anteriores do Windows.
* Azure PowerShell 1.0 e posterior. Para obter mais informações sobre a versão do PowerShell 1.0, veja [How to install and configure Azure PowerShell](/powershell/azure/overview) (Como instalar e configurar o Azure PowerShell).
* Uma conta de Automatização, que é referenciada como o valor para os parâmetros *–AutomationAccountName* e *-ApplicationDisplayName* no script do PowerShell seguinte.

Para obter os valores para *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, que são parâmetros necessários para os scripts, faça o seguinte:
1. No portal do Azure, selecione a sua conta de Automatização no painel **Conta de Automatização** e, em seguida, selecione **Todas as definições**. 
2. No painel **Todas as definições**, em **Definições da Conta**, selecione **Propriedades**. 
3. Repare nos valores no painel **Propriedades**.

![O painel “Propriedades” da conta de Automatização](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Criar um script do PowerShell da onta Run As
Este script do PowerShell inclui suporte para as seguintes configurações:

* Utilizar um certificado autoassinado para criar uma conta Run As.
* Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica.
* Utilizar um certificado empresarial para criar uma conta Run As e uma conta Run As Clássica.
* Utilizar um certificado autoassinado na cloud do Azure Government para criar uma conta Run As e uma conta Run As Clássica.

Consoante a opção de configuração que selecionar, o script cria os itens seguintes.

**Para contas Run as:**

* Cria uma aplicação do Azure AD a ser exportada com a chave pública do certificado autoassinado ou empresarial, criar uma conta de principal de serviço para a aplicação no Azure AD e atribui a função Contribuinte à conta na sua subscrição atual. Pode alterar esta definição para Proprietário ou qualquer outra função. Para obter mais informações, veja [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).
* Cria um recurso de certificado da Automatização com o nome *AzureRunAsCertificate* na conta de Automatização especificada. O recurso do certificado contém a chave privada do certificado que a aplicação do Azure AD utiliza.
* Criar um recurso de ligação da Automatização com o nome *AzureRunAsConnection* na conta de Automatização especificada. O recurso de ligação contém o applicationId, o tenantId, o subscriptionId e o thumbprint do certificado.

**Para contas Run As Clássica:**

* Cria um recurso de certificado da Automatização com o nome *AzureClassicRunAsCertificate* na conta de Automatização especificada. O recurso do certificado contém a chave privada do certificado que o certificado de gestão utiliza.
* Cria um recurso de ligação da Automatização com o nome *AzureClassicRunAsConnection* na conta de Automatização especificada. O recurso de ligação contém o nome da subscrição, o subscriptionid e o nome de recurso do certificado.

>[!NOTE]
> Se selecionar uma destas opções para criar uma conta Run As Clássica, após o script ser executado, carregue o certificado público (extensão de nome de ficheiro .cer) para o arquivo de gestão da subscrição na qual a conta de Automatização foi criada.
> 

Para executar o script e carregar o certificado, faça o seguinte:

1. Guarde o seguinte script no seu computador. Neste exemplo, guarde-o com o nome de ficheiro *New-RunAsAccount.ps1*.

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
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. No seu computador, clique em **Iniciar** e inicie o **Windows PowerShell** com direitos de utilizador elevados.

3. A partir da shell da linha de comandos do PowerShell elevada, aceda à pasta que contém o script que criou no passo 1.

4. Execute o script, utilizando os valores de parâmetros da configuração de que precisa.

    **Utilizar um certificado autoassinado para criar uma conta Run As**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Utilizar um certificado autoassinado para criar uma conta Run As e uma conta Run As Clássica**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Utilizar um certificado empresarial para criar uma conta Run As e uma conta Run As Clássica**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Utilizar um certificado autoassinado na cloud do Azure Government para criar uma conta Run As e uma conta Run As Clássica**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Após o script ser executado, ser-lhe-á pedido para autenticar com o Azure. Inicie sessão com uma conta que seja membro da função de administradores da subscrição e coadministrador da subscrição.
    >
    >

Depois de o script ser executado com êxito, tenha em conta o seguinte:
* Se tiver criado uma conta Run As Clássica com um certificado público autoassinado (ficheiro .cer), o script cria-o e guarda-o na pasta de ficheiros temporários no seu computador, no perfil de utilizador *%USERPROFILE%\AppData\Local\Temp* que utilizou para executar a sessão do PowerShell.
* Se tiver criado uma conta Run As Clássica com um certificado público empresarial (ficheiro .cer), utilize esse certificado. Siga as instruções relativas a [carregar certificados de API de gestão para o portal clássico do Azure](../azure-api-management-certs.md) e utilize o [código de exemplo para autenticar com os Recursos da Gestão do Serviço](#sample-code-to-authenticate-with-service-management-resources) para validar a configuração da credencial com recursos da Gestão do Serviço. 
* Se *não* tiver criado uma conta Run As Clássica, autentique com recursos do Resource Manager e utilize o [código de exemplo para autenticar com recursos da Gestão do Serviço](#sample-code-to-authenticate-with-resource-manager-resources) para validar a configuração da credencial.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Código de exemplo para autenticar com recursos do Resource Manager
Pode utilizar o código de exemplo atualizado seguinte, retirado do runbook de exemplo *AzureAutomationTutorialScript*, para autenticar com a conta Run As e gerir os recursos do Resource Manager com os runbooks.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
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

Para o ajudar a alternar facilmente entre várias subscrições, o script inclui duas linhas adicionais de código que suportam a referência a contextos de subscrições. O ID da subscrição está contido num recurso de variável com o nome *SubscriptionId*. A seguir à declaração do cmdlet `Add-AzureRmAccount`, o cmdlet [`Set-AzureRmContext`](/powershell/module/azurerm.profile/set-azurermcontext) é indicado com o parâmetro definido como *-SubscriptionId*. Se o nome da variável for demasiado genérico, pode revê-lo para incluir um prefixo ou outra convenção de nomenclatura para ser mais fácil de identificar. Em alternativa, pode utilizar o parâmetro definido *-SubscriptionName* em vez de *-SubscriptionId* com um recurso de variável correspondente.

O cmdlet que utiliza para autenticar no runbook, `Add-AzureRmAccount`, utiliza o parâmetro definido *ServicePrincipalCertificate*. Para autenticar, utiliza o certificado do principal de serviço, não as credenciais de utilizador.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Código de exemplo para autenticar com recursos do Service Management
Pode utilizar o código de exemplo atualizado seguinte, retirado do runbook de exemplo *AzureClassicAutomationTutorialScript* para autenticar com a conta Run As Clássica para gerir os recursos clássicos com os runbooks.

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

## <a name="next-steps"></a>Passos seguintes
* [Application and service principal objects in Azure AD](../active-directory/active-directory-application-objects.md) (Objetos de aplicação e de principal de serviço no Azure AD)
* [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md)
* [Certificates overview for Azure Cloud Services](../cloud-services/cloud-services-certs-create.md) (Descrição geral dos certificados para os Serviços Cloud do Azure)


<properties
   pageTitle="Configurar Conta de Utilizador do Azure AD | Microsoft Azure"
   description="Este artigo descreve como configurar a credencial da conta de Utilizador do Azure AD para runbooks na Automatização do Azure para efetuar a autenticação no ARM e ASM."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="azure active directory user, azure service management, azure ad user account" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="magoedte" />

# Autenticar Runbooks com a Gestão do Serviço do Azure e o Resource Manager

Este artigo descreve os passos que tem de efetuar para configurar uma conta de Utilizador do Azure AD para runbooks de Automatização do Azure em execução na Gestão do Serviço do Azure (ASM) ou nos recursos do Azure Resource Manager (ARM).  Embora isto continue a ser uma identidade de autenticação suportada para os runbooks com base no ARM, o método recomendado consiste em utilizar a nova conta Run As do Azure.       

## Criar um novo utilizador do Azure Active Directory

1. Inicie sessão no Portal Clássico do Azure como um administrador de serviços para a subscrição do Azure que pretende gerir.
2. Selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.
3. Selecione o separador **Utilizadores** e, na área de comandos, selecione **Adicionar Utilizador**.
4. Na página **Forneça mais informações sobre este utilizador**, em **Tipo de utilizador**, selecione **Novo utilizador na sua organização**.
5. Introduza um nome de utilizador.  
6. Selecione o nome de diretório que está associado à sua subscrição do Azure na página do Active Directory.
7. Na página **perfil de utilizador**, introduza o nome próprio e o apelido, um nome amigável de utilizador e um Utilizador na lista **Funções**.  Não **Ative a Multi-Factor Authentication**.
8. Tenha em atenção o nome completo e a palavra-passe temporária do utilizador.
9. Selecione **Definições > Administradores > Adicionar**.
10. Escreva o nome de utilizador completo do utilizador que criou.
11. Selecione a subscrição que pretende que o utilizador efetue a gestão.
12. Termine sessão no Azure e, em seguida, inicie sessão novamente com a conta que acabou de criar. Será solicitado a alterar a palavra-passe do utilizador.


## Crie uma conta de Automatização no Portal Clássico do Azure
Nesta secção, é necessário executar os seguintes passos para criar uma nova conta de Automatização do Azure no Portal do Azure que será utilizado com os recursos de gestão dos runbooks no modo ASM e ARM.  

>[AZURE.NOTE] As contas de automatização criadas com o Portal Clássico do Azure podem ser geridas tanto pelo Azure Clássico como pelo Portal do Azure e qualquer conjunto de cmdlets. Depois de a conta estar criada, não faz diferença como criar e gerir recursos na conta. Se estiver a planear continuar a utilizar o Portal Clássico do Azure, deve utilizá-lo em vez do Portal do Azure para criar as contas de Automatização.


1. Inicie sessão no Portal Clássico do Azure como um administrador de serviços para a subscrição do Azure que pretende gerir.
2. Selecione **Automatização**.
3. Na página **Automatização**, selecione **Criar uma Conta de Automatização**.
4. Na caixa **Criar uma Conta de Automatização**, escreva um nome para a sua nova conta de Automatização e selecione uma **Região** na lista pendente.  
5. Clique em **OK** para aceitar as suas definições e criar a conta.
6. Depois de ser criada, será apresentada na página **Automatização**.
7. Clique na conta e aparecerá a página do Dashboard.  
8. Na página do Dashboard de Automatização, selecione **Recursos**.
9. Na página **Recursos**, selecione a opção **Adicionar Definições** localizada na parte inferior da página.
10. Na página **Adicionar Definições**, selecione **Adicionar Credencial**.
11. Na página **Definir Credencial**, selecione **Credencial do Windows PowerShell** na lista pendente **Tipo de Credencial** e dê um nome à credencial.
12. Na página seguinte **Definir Credencial**, escreva o nome de utilizador da conta de utilizador do AD criada anteriormente no campo **Nome de Utilizador** e a palavra-passe nos campos **Palavra-passe** e **Confirmar palavra-passe**. Clique em **OK** para guardar as alterações.

## Criar uma conta de Automatização no Portal do Azure

Nesta secção, é necessário executar os seguintes passos para criar uma nova conta de Automatização do Azure no Portal do Azure que será utilizado com os recursos de gestão dos runbooks no modo ARM.  

1. Inicie sessão no portal do Azure como um administrador de serviços para a subscrição do Azure que pretende gerir.
2. Selecione **Contas de Automatização**.
3. No painel Contas de Automatização, clique em **Adicionar**.<br>![Adicionar Conta de Automatização](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
2. No painel **Adicionar Conta de Automatização**, no tipo de caixa **Nome**, escreva um nome para a sua nova conta de Automatização.
5. Se tiver mais do que uma subscrição, especifique a subscrição para a nova conta, bem como um **Grupo de recursos** novo ou existente e uma **localização** do datacenter do Azure.
3. Selecione o valor **Não** para a opção **Criar conta Run As do Azure** e clique no botão **Criar**.  

    >[AZURE.NOTE] Se optar por não criar a conta Run As, selecionando a opção **Não**, será apresentada uma mensagem de aviso no painel **Adicionar Conta de Automatização**.  Enquanto a conta é criada e atribuída à função **Contribuidor** na subscrição, não terá uma identidade de autenticação correspondente dentro do seu serviço de diretório de subscrições e, por isso, sem recursos de acesso na sua subscrição.  Isto irá impedir que todos os runbooks que referenciam esta conta tenham a capacidade de autenticar e executar tarefas relativamente aos recursos do ARM.

    ![Adicionar Aviso de Conta de Automatização](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

4. Enquanto o Azure cria a conta de automatização, pode acompanhar o progresso em **Notificações** a partir do menu.

Quando a criação da credencial estiver concluída, terá de criar um Recurso de Credencial para associar a Conta de Automatização com a conta de Utilizador do AD criada anteriormente.  Lembre-se de que apenas criamos a conta de Automatização e não está associada a uma identidade de autenticação.  Execute os passos descritos no [artigo Recursos da credencial na Automatização do Azure](../automation/automation-credentials.md#creating-a-new-credential) e introduza o valor para o **nome de utilizador** no formato **domínio\utilizador**.

## Utilizar a credencial num runbook

Pode obter a credencial num runbook com a atividade [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e, em seguida, utilizá-la com [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para ligar à sua subscrição do Azure. Se a credencial for um administrador de várias subscrições do Azure, também deve utilizar [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar a correta. Isto é apresentado na amostra do Windows PowerShell que normalmente irá aparecer no topo da maior parte dos runbooks da Automatização do Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Deve repetir estas linhas após qualquer [ponto de verificação](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) no runbook. Se o runbook está suspenso e, em seguida, retoma noutra função de trabalho, será necessário efetuar a autenticação novamente.

## Passos Seguintes
* Reveja os diferentes tipos e passos de runbook para criar os seus próprios runbooks a partir do seguinte artigo [Tipos de runbook de Automatização do Azure](../automation/automation-runbook-types.md)



<!--HONumber=Jun16_HO2-->



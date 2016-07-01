<properties 
   pageTitle="Controlo de acesso baseado em funções na Automatização do Azure | Microsoft Azure"
   description="O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Este artigo descreve como configurar o RBAC na Automatização do Azure."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn"
   keywords="automation rbac, role based access control, azure rbac" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="magoedte;sngun"/>

# Controlo de acesso baseado em funções na Automatização do Azure

## Controlo de acesso baseado em funções

O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Ao utilizar o [RBAC](../active-directory/role-based-access-control-configure.md), pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores, grupos e aplicações que precisam para desempenhar as suas funções. O acesso baseado em funções pode ser concedido aos utilizadores através do portal do Azure, das ferramentas da Linha de Comandos do Azure ou de APIs de Gestão do Azure.

## RBAC em Contas de Automatização

Na Automatização do Azure, é concedido acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações no âmbito da conta de Automatização. Seguem-se as funções incorporadas suportadas por uma conta de Automatização:  

|**Função** | **Descrição** |
|:--- |:---|
| Proprietário | A função de proprietário permite o acesso a todos os recursos e ações dentro de uma conta de Automatização, incluindo fornecer acesso a outros utilizadores, grupos e aplicações para gerir a conta de Automatização. |
| Contribuinte | A função de contribuinte permite-lhe gerir tudo, exceto modificar as permissões de acesso de outro utilizador para uma conta de Automatização. |
| Leitor | A função do leitor permite-lhe ver todos os recursos numa conta de Automatização, mas não permite efetuar quaisquer alterações. |
| Operador de Automatização | A função de operador de Automatização permite-lhe realizar tarefas operacionais, tais como iniciar, parar, suspender, retomar e agendar tarefas. Esta função é útil se pretender proteger os seus recursos da Conta de Automatização como recursos de credenciais e runbooks contra a visualização ou modificação, mas ainda permite que os membros da sua organização executem estes runbooks. As [Ações de operador de automatização](../active-directory/role-based-access-built-in-roles.md#automation-operator) listam as ações suportadas pela função de operador de Automatização na conta de Automatização e os respetivos recursos. |
| Administrador de acesso de utilizador | A função de administrador de acesso de utilizador permite-lhe gerir o acesso de utilizador para as contas de Automatização do Azure. |

Neste artigo, ajudá-lo-emos a configurar o RBAC na Automatização do Azure. 

## Configurar o RBAC para a sua Conta de Automatização com o Portal do Azure

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/) e abra a sua conta de Automatização a partir do painel de Contas de Automatização.  

2.  Clique no controlo **Acesso** no canto superior direito. Esta ação abre o painel **Utilizadores**, onde pode adicionar novos utilizadores, grupos e aplicações para gerir a sua conta de Automatização e ver funções existentes que podem ser configuradas para a Conta de Automatização.  

    ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)  

>[AZURE.NOTE]  **Os administradores da subscrição** já existem como o utilizador predefinido. O grupo do Active Directory dos administradores da subscrição inclui os administradores e coadministradores de serviço para a sua subscrição do Azure. O administrador de Serviço é o proprietário da sua subscrição do Azure e dos respetivos recursos e também terá a função de proprietário herdada para as contas de automatização. Isto significa que o acesso é **Herdado** para **os administradores e coadministradores de serviço** de uma subscrição e está **Atribuído** para todos os outros utilizadores. Clique em **Administradores da subscrição** para ver mais detalhes sobre as respetivas permissões.  

### Adicionar um novo utilizador e atribuir uma função

1.  O painel de Utilizadores, clique em **Adicionar** para abrir o **painel Adicionar acesso** onde pode adicionar um utilizador, grupo ou aplicação e atribuir-lhes uma função.  

    ![Adicionar utilizador](media/automation-role-based-access-control/automation-02-add-user.png)  

2.  Selecione uma função na lista de funções disponíveis. Vamos escolher a função de **Leitor**, mas pode escolher qualquer uma das funções incorporadas disponíveis que uma Conta de Automatização suportar ou qualquer função personalizada que pode ter definido.  

    ![Selecionar função](media/automation-role-based-access-control/automation-03-select-role.png)  

3.  Clique em **Adicionar utilizadores** para abrir o painel **Adicionar utilizadores**. Se tiver adicionado quaisquer utilizadores, grupos ou aplicações para gerir a sua subscrição, esses utilizadores estão listados e pode selecioná-los para adicionar o acesso. Se não existirem quaisquer utilizadores listados ou se o utilizador no qual está interessado não estiver listado, clique em **convidar** para abrir o painel **Convidar um convidado**, onde pode convidar um utilizador com um endereço de e-mail de uma conta Microsoft válida como o Outlook.com, OneDrive ou Xbox Live IDs. Depois de introduzir o endereço de e-mail do utilizador, clique em **Selecionar** para adicionar o utilizador e, em seguida, clique em **OK**. 

    ![Adicionar utilizadores](media/automation-role-based-access-control/automation-04-add-users.png)  
 
Agora, deve conseguir ver o utilizador adicionado ao painel **Utilizadores** com a função **Leitor** atribuída.  

![Listar utilizadores](media/automation-role-based-access-control/automation-05-list-users.png)  

Também pode atribuir uma função ao utilizador a partir do painel **Funções**. Clique em **Funções** a partir do painel de Utilizadores para abrir o **painel Funções**. A partir deste painel, pode ver o nome da função, o número de utilizadores e os grupos atribuídos a essa função.

![Atribuir função a partir do painel de utilizadores](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
>[AZURE.NOTE] O Controlo de acesso baseado em funções só pode ser definido ao nível da Conta de Automatização e não em qualquer recurso abaixo a Conta de Automatização.

Pode atribuir mais de uma função a um utilizador, grupo ou aplicação. Por exemplo, se adicionarmos a função **Operador de Automatização** juntamente com a **função Leitor** ao utilizador, é possível ver todos os recursos de Automatização, bem como executar as tarefas de runbook. Pode expandir o menu pendente para ver uma lista das funções atribuídas ao utilizador.  

![Ver várias funções](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  
 
### Remover um utilizador

Pode remover a permissão de acesso de um utilizador que não está a gerir a Conta de Automatização ou que já não trabalha para a organização. Seguem-se os passos para remover um utilizador: 

1.  A partir do painel **Utilizadores**, selecione a atribuição de função que pretende remover.

2.  Clique no botão **Remover** no painel de detalhes de atribuição.

3.  Clique em **Sim** para confirmar a remoção. 

    ![Remover utilizadores](media/automation-role-based-access-control/automation-08-remove-users.png)  

## Utilizador com Função Atribuída

Quando um utilizador atribuído a uma função iniciar sessão na respetiva conta de Automatização, pode ver a conta do proprietário listada na lista de **Diretórios Predefinidos**. Para poder ver a conta de Automatização à qual foi adicionado, o utilizador tem de alternar o diretório predefinido para o diretório predefinido do proprietário.  

![Diretório predefinido](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### Experiência de utilizador para a função de operador de Automatização

Quando um utilizador atribuído à função de Operador de Automatização vê a conta de Automatização à qual é atribuído, só pode ver a lista de runbooks, tarefas de runbook e agendas criadas da conta de Automatização, mas não pode ver a respetiva definição. Pode iniciar, parar, suspender, retomar ou agendar a tarefa de runbook. O utilizador não terá acesso a outros recursos de Automatização, tais como configurações, grupos de função de trabalho híbrida ou nós de DSC.  

![Sem acesso a recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Quando o utilizador clica no runbook, os comandos para ver a origem ou editar o runbook não são fornecidos, uma vez que a função de operador de Automatização não permite aceder aos mesmos.  

![Sem acesso para editar o runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

O utilizador terá acesso para ver e criar agendas, mas não terá acesso a qualquer outro tipo de recurso.  

![Sem acesso aos recursos](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

Este utilizador também não tem acesso para ver os webhooks associados a um runbook

![Sem acesso aos webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## Configurar o RBAC para a sua Conta de Automatização com o Azure PowerShell

O acesso baseado em funções também pode ser configurado para uma Conta de Automatização utilizando os seguintes [cmdlets do Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) lista todas as funções que estão disponíveis no RBAC do Azure Active Directory. Pode utilizar este comando juntamente com a propriedade **Nome** para listar todos os utilizadores com uma função específica.  
    **Exemplo:**  
    ![Obter a definição de função](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) lista atribuições de funções do RBAC do Azure no âmbito especificado. Sem quaisquer parâmetros, este comando devolve todas as atribuições de funções efetuadas sob a subscrição. Utilize o parâmetro **ExpandPrincipalGroups** para listar atribuições de acesso para o utilizador especificado, bem como para os grupos dos quais o utilizador é membro.  
    **Exemplo:** utilize o seguinte comando para listar todos os utilizadores e as respetivas funções dentro de uma conta de automatização.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Obter a atribuição de função](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) para conceder acesso a utilizadores, grupos e aplicações num determinado âmbito.  
    **Exemplo:** utilize o seguinte comando para criar uma nova função de "Operador de Automatização" para um utilizador no âmbito da Conta de Automatização.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nova atribuição de função](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Utilize [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para remover o acesso ao utilizador especificado, grupo ou aplicação num determinado âmbito.
    **Exemplo:** utilize o seguinte comando para criar uma nova função de "Operador de Automatização" para um utilizador no âmbito da Conta de Automatização.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Nos cmdlets acima, substitua o nome de início de sessão, o ID de subscrição, o nome do grupo de recursos e o nome da conta de Automatização pelos detalhes da sua conta. Escolha **sim** quando lhe for pedido para prosseguir com a eliminação da atribuição da função.   


## Passos Seguintes
-  Para obter informações sobre formas diferentes de configurar o RBAC para a Automatização do Azure, consulte [gerir o RBAC com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Para obter detalhes sobre diferentes formas de iniciar um runbook, consulte o artigo [Iniciar um runbook](automation-starting-a-runbook.md)
- Para obter informações sobre diferentes tipos, consulte [Tipos de runbook de Automatização do Azure](automation-runbook-types.md)




<!--HONumber=Jun16_HO2-->



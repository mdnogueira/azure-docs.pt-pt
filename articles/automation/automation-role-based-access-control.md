---
title: "Controlo de acesso baseado em funções na Automatização do Azure | Microsoft Docs"
description: "O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Este artigo descreve como configurar o RBAC na Automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
keywords: "rbac de automatização, controlo de acesso baseado em funções , rbac do azure"
ms.assetid: 04b5625e-0ee8-4b5b-85cd-7734c1b3d4a3
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: magoedte;sngun
ms.openlocfilehash: 946d80d40ac0566db72c787f260f2d4faff01e6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="role-based-access-control-in-azure-automation"></a>Controlo de acesso baseado em funções na Automatização do Azure
## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Ao utilizar o [RBAC](../active-directory/role-based-access-control-configure.md), pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores, grupos e aplicações que precisam para desempenhar as suas funções. O acesso baseado em funções pode ser concedido aos utilizadores através do portal do Azure, das ferramentas da Linha de Comandos do Azure ou de APIs de Gestão do Azure.

## <a name="rbac-in-automation-accounts"></a>RBAC em Contas de Automatização
Na Automatização do Azure, é concedido acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações no âmbito da conta de Automatização. Seguem-se as funções incorporadas suportadas por uma conta de Automatização:  

| **Função** | **Descrição** |
|:--- |:--- |
| Proprietário |A função de Proprietário permite o acesso a todos os recursos e ações numa conta de Automatização, incluindo fornecer acesso a outros utilizadores, grupos e aplicações para gerir a conta de Automatização. |
| Contribuinte |A função de Contribuidor permite-lhe gerir tudo, exceto modificar as permissões de acesso de outro utilizador para uma conta de Automatização. |
| Leitor |A função de Leitor permite-lhe ver todos os recursos numa conta de Automatização, mas não permite efetuar quaisquer alterações. |
| Operador de Automatização |A função de Operador de Automatização permite-lhe realizar tarefas operacionais, tais como iniciar, parar, suspender, retomar e agendar tarefas. Esta função é útil se pretender proteger os seus recursos da Conta de Automatização como recursos de credenciais e runbooks contra a visualização ou modificação, mas ainda permite que os membros da sua organização executem estes runbooks. |
| Administrador de Acesso de Utilizador |A função de Administrador de Acesso de Utilizador permite-lhe gerir o acesso de utilizador para as contas de Automatização do Azure. |

> [!NOTE]
> Não é possível conceder direitos de acesso a um runbook ou runbooks específicos, apenas para os recursos e ações na conta de Automatização.  
> 
> 

Neste artigo, ajudamo-lo a configurar o RBAC na Automatização do Azure. Mas, primeiro, vamos analisar com mais atenção as permissões individuais concedidas ao Contribuidor, Leitor, Operador de Automatização e Administrador de Acesso de Utilizador para que possamos ter uma boa compreensão antes de conceder a alguém direitos à conta de Automatização.  Caso contrário, poderão resultar em consequências involuntárias ou indesejáveis.     

## <a name="contributor-role-permissions"></a>Permissões de Função de Contribuidor
A tabela seguinte apresenta as ações específicas que podem ser efetuadas pela função de Contribuidor na Automatização.

| **Tipo de Recurso** | **Leitura** | **Escrita** | **Eliminar** | **Outras Ações** |
|:--- |:--- |:--- |:--- |:--- |
| Adicionar Conta de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de Certificado de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de Ligação de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de Tipo de Ligação de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de Credenciais de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de Agenda da Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de Variável de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Configuração do Estado Pretendido de Automatização | | | |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Tipo de Recurso da Função de Trabalho de Runbook Híbrida |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Tarefa de Automatização do Azure |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Fluxo de Trabalho de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Plano de Trabalho da Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Módulo de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Runbook da Automatização do Azure |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Rascunho do Runbook da Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Tarefa de Teste de Rascunho do Runbook de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Webhook de automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |

## <a name="reader-role-permissions"></a>Permissões de função de leitor
A tabela seguinte apresenta as ações específicas que podem ser efetuadas pela função de Leitor na Automatização.

| **Tipo de Recurso** | **Leitura** | **Escrita** | **Eliminar** | **Outras Ações** |
|:--- |:--- |:--- |:--- |:--- |
| Administrador de subscrição clássica |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Bloqueio de gestão |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Permissão |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Operações de fornecedor |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Atribuição de função |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Definição de função |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="automation-operator-role-permissions"></a>Permissões de função do Operador de Automatização
A tabela seguinte apresenta as ações específicas que podem ser efetuadas pela função de Operador de Automatização em Automatização.

| **Tipo de Recurso** | **Leitura** | **Escrita** | **Eliminar** | **Outras Ações** |
|:--- |:--- |:--- |:--- |:--- |
| Adicionar Conta de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de Certificado de Automatização | | | | |
| Recurso de Ligação de Automatização | | | | |
| Recurso de Tipo de Ligação de Automatização | | | | |
| Recurso de Credenciais de Automatização | | | | |
| Recurso de Agenda da Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Recurso de Variável de Automatização | | | | |
| Configuração do Estado Pretendido de Automatização | | | | |
| Tipo de Recurso da Função de Trabalho de Runbook Híbrida | | | | |
| Tarefa de Automatização do Azure |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Fluxo de Trabalho de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Plano de Trabalho da Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Módulo de Automatização | | | | |
| Runbook da Automatização do Azure |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Rascunho do Runbook da Automatização | | | | |
| Tarefa de Teste de Rascunho do Runbook de Automatização | | | | |
| Webhook de automatização | | | | |

Para obter mais informações, [Ações de operador de automatização](../active-directory/role-based-access-built-in-roles.md#automation-operator) lista as ações suportadas pela função de Operador de automatização na conta Automatização e respetivos recursos.

## <a name="user-access-administrator-role-permissions"></a>Permissões da função Administrador de Acesso de Utilizador
A tabela seguinte apresenta as ações específicas que podem ser efetuadas pela função Operador de Administrador de Acesso do Utilizador em Automatização.

| **Tipo de Recurso** | **Leitura** | **Escrita** | **Eliminar** | **Outras Ações** |
|:--- |:--- |:--- |:--- |:--- |
| Adicionar Conta de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de Certificado de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de Ligação de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de Tipo de Ligação de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de Credenciais de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de Agenda da Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de Variável de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Configuração do Estado Pretendido de Automatização | | | | |
| Tipo de Recurso da Função de Trabalho de Runbook Híbrida |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Tarefa de Automatização do Azure |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Fluxo de Trabalho de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Plano de Trabalho da Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Módulo de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Runbook da Automatização do Azure |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Rascunho do Runbook da Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Tarefa de Teste de Rascunho do Runbook de Automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Webhook de automatização |![Estado Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Configurar o RBAC para a sua Conta de Automatização com o portal do Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e abra a sua conta de Automatização a partir da página Contas de Automatização.  
2. Clique no controlo **Acesso** no canto superior direito. Esta ação abre a página **Utilizadores**, onde pode adicionar novos utilizadores, grupos e aplicações para gerir a sua conta de Automatização e ver funções existentes que podem ser configuradas para a Conta de Automatização.  
   
   ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)  

> [!NOTE]
> **Os administradores da subscrição** já existem como o utilizador predefinido. O grupo do Active Directory dos administradores da subscrição inclui os administradores e coadministradores de serviço para a sua subscrição do Azure. O administrador de Serviço é o proprietário da sua subscrição do Azure e dos respetivos recursos e também terá a função de proprietário herdada para as contas de automatização. Isto significa que o acesso é **Herdado** para **os administradores e coadministradores de serviço** de uma subscrição e está **Atribuído** para todos os outros utilizadores. Clique em **Administradores da subscrição** para ver mais detalhes sobre as respetivas permissões.  
> 
> 

### <a name="add-a-new-user-and-assign-a-role"></a>Adicionar um novo utilizador e atribuir uma função
1. Na página Utilizadores, clique em **Adicionar** para abrir a página **Adicionar acesso**, onde pode adicionar um utilizador, grupo ou aplicação e atribuir-lhes uma função.  
   
   ![Adicionar utilizador](media/automation-role-based-access-control/automation-02-add-user.png)  
2. Selecione uma função na lista de funções disponíveis. Vamos escolher a função de **Leitor**, mas pode escolher qualquer uma das funções incorporadas disponíveis que uma Conta de Automatização suportar ou qualquer função personalizada que pode ter definido.  
   
   ![Selecionar função](media/automation-role-based-access-control/automation-03-select-role.png)  
3. Clique em **Adicionar utilizadores** para abrir a página **Adicionar utilizadores**. Se tiver adicionado quaisquer utilizadores, grupos ou aplicações para gerir a sua subscrição, esses utilizadores estão listados e pode selecioná-los para adicionar o acesso. Se não existirem quaisquer utilizadores listados ou se o utilizador no qual está interessado não estiver listado, clique em **convidar** para abrir a página **Convidar um convidado**, onde pode convidar um utilizador com um endereço de e-mail de uma conta Microsoft válida como o Outlook.com, OneDrive ou Xbox Live IDs. Depois de introduzir o endereço de e-mail do utilizador, clique em **Selecionar** para adicionar o utilizador e, em seguida, clique em **OK**. 
   
   ![Adicionar utilizadores](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   Agora, deve conseguir ver o utilizador adicionado à página **Utilizadores** com a função **Leitor** atribuída.  
   
   ![Listar utilizadores](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   Também pode atribuir uma função ao utilizador a partir da página **Funções**. 
4. Clique em **Funções** a partir da página Utilizadores para abrir a página **Funções**. A partir daí, pode ver o nome da função, o número de utilizadores e os grupos atribuídos a essa função.
   
    ![Atribuir função a partir da página Utilizadores](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > O Controlo de acesso baseado em funções só pode ser definido ao nível da Conta de Automatização e não em qualquer recurso abaixo a Conta de Automatização.
   > 
   > 
   
    Pode atribuir mais de uma função a um utilizador, grupo ou aplicação. Por exemplo, se adicionarmos a função **Operador de Automatização** juntamente com a **função Leitor** ao utilizador, é possível ver todos os recursos de Automatização, bem como executar as tarefas de runbook. Pode expandir o menu pendente para ver uma lista das funções atribuídas ao utilizador.  
   
    ![Ver várias funções](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  

### <a name="remove-a-user"></a>Remover um utilizador
Pode remover a permissão de acesso de um utilizador que não está a gerir a Conta de Automatização ou que já não trabalha para a organização. Seguem-se os passos para remover um utilizador: 

1. A partir da página **Utilizadores**, selecione a atribuição de função que quer remover.
2. Clique no botão **Remover** na página de detalhes de atribuição.
3. Clique em **Sim** para confirmar a remoção. 
   
   ![Remover utilizadores](media/automation-role-based-access-control/automation-08-remove-users.png)  

## <a name="role-assigned-user"></a>Utilizador com Função Atribuída
Quando um utilizador atribuído a uma função iniciar sessão na respetiva conta de Automatização, pode ver a conta do proprietário listada na lista de **Diretórios Predefinidos**. Para poder ver a conta de Automatização à qual foi adicionado, o utilizador tem de alternar o diretório predefinido para o diretório predefinido do proprietário.  

![Diretório predefinido](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### <a name="user-experience-for-automation-operator-role"></a>Experiência de utilizador para a função de operador de Automatização
Quando um utilizador, a quem é atribuída a função de Operador de Automatização vê a conta de Automatização à qual é atribuído, pode ver a lista de runbooks, tarefas de runbook e agendas criadas da conta de Automatização, mas não pode ver a respetiva definição. Pode iniciar, parar, suspender, retomar ou agendar a tarefa de runbook. O utilizador não tem acesso a outros recursos de Automatização, tais como configurações, grupos de função de trabalho híbrida ou nós de DSC.  

![Sem acesso a recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Quando o utilizador clica no runbook, os comandos para ver a origem ou editar o runbook não são fornecidos, uma vez que a função de operador de Automatização não permite aceder aos mesmos.  

![Sem acesso para editar o runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

O utilizador tem acesso para ver e criar agendas, mas não tem acesso a qualquer outro tipo de recurso.  

![Sem acesso aos recursos](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

Este utilizador também não tem acesso para ver os webhooks associados a um runbook

![Sem acesso aos webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Configurar o RBAC para a sua Conta de Automatização com o Azure PowerShell
O acesso baseado em funções também pode ser configurado para uma Conta de Automatização utilizando os seguintes [cmdlets do Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) lista todas as funções do RBAC que estão disponíveis no Azure Active Directory. Pode utilizar este comando juntamente com a propriedade **Nome** para listar todas as ações que podem ser utilizadas com uma função específica.  
    **Exemplo:**  
    ![Obter a definição de função](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) lista atribuições de funções de RBAC do Azure AD no âmbito especificado. Sem quaisquer parâmetros, este comando devolve todas as atribuições de funções efetuadas sob a subscrição. Utilize o parâmetro **ExpandPrincipalGroups** para listar atribuições de acesso para o utilizador especificado, bem como para os grupos dos quais o utilizador é membro.  
    **Exemplo:** utilize o seguinte comando para listar todos os utilizadores e as respetivas funções dentro de uma conta de automatização.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Obter a atribuição de função](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) para conceder acesso a utilizadores, grupos e aplicações num determinado âmbito.  
    **Exemplo:** utilize o seguinte comando para atribuir a função de "Operador de Automatização" a um utilizador no âmbito da Conta de Automatização.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nova atribuição de função](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Utilize [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para remover o acesso de um utilizador, grupo ou aplicação especificados de um âmbito específico.  
    **Exemplo:** utilize o seguinte comando para remover o utilizador da função "Operador de Automatização" no âmbito da Conta de Automatização.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Nos exemplos indicados acima, substitua o **ID de início de sessão**, o **ID de subscrição**, o **nome do grupo de recursos** e o **Nome da conta de automatização** pelos detalhes da sua conta. Escolha **Sim** quando lhe for solicitado para confirmar antes de continuar para remover a atribuição de função de utilizador.   

## <a name="next-steps"></a>Passos Seguintes
* Para obter informações sobre diferentes maneiras de configurar o RBAC para a Automatização do Azure, veja [Gerir o RBAC com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
* Para obter detalhes sobre diferentes formas de iniciar um runbook, consulte o artigo [Iniciar um runbook](automation-starting-a-runbook.md)
* Para obter informações sobre os vários tipos de runbook, veja [Tipos de runbook de Automatização do Azure](automation-runbook-types.md)


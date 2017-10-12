---
title: "Criar Conta de Automatização do Azure autónoma | Microsoft Docs"
description: "Tutorial que o orienta através da criação, teste e exemplo de utilização da autenticação principal de segurança na Automatização do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: e3c18c7886c8338efc6168464b63a9557909a769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-standalone-azure-automation-account"></a>Criar uma conta de Automatização do Azure autónoma
Este tópico mostra-lhe como criar uma conta de Automatização a partir do portal do Azure se pretender avaliar e aprender mais sobre a Automatização do Azure, sem incluir as soluções de gestão adicionais ou a integração com o Log Analytics do OMS para fornecer monitorização avançada de tarefas de runbook.  Pode adicionar essas soluções de gestão ou integrar com o Log Analytics a qualquer momento no futuro.  Com a conta de Automatização, pode autenticar runbooks que gerem recursos no Azure Resource Manager ou na implementação clássica do Azure.

Quando cria uma conta de Automatização no portal do Azure, este cria automaticamente:

* Uma conta Run As, que cria um novo principal de serviço no Azure Active Directory, um certificado e atribui o controlo de acesso baseado em funções (RBAC) de Contribuidor, que é utilizado para gerir recursos do Gestor de Recursos através de runbooks.   
* A conta Run As clássica através do carregamento de um certificado de gestão, que é utilizada para gerir recursos clássicos através de runbooks.  

Isto simplifica o processo para si e ajuda-o a começar rapidamente a criar e implementar runbooks para suportar as suas necessidades de automatização.  

## <a name="permissions-required-to-create-automation-account"></a>Permissões necessárias para criar uma conta de Automatização
Para criar ou atualizar uma conta de Automatização, tem de ter os seguintes privilégios específicos e as permissões necessárias para concluir este tópico.   
 
* Para criar uma conta de Automatização, a sua conta de utilizador do AD tem de ser adicionada a uma função com permissões equivalentes à função de Proprietário para recursos Microsoft.Automation, conforme descrito no artigo [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).  
* Se a definição dos registos da Aplicação for **Sim**, os utilizadores não administradores no inquilino do Azure AD podem [registar aplicações do AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Se a definição dos registos da aplicação for **Não**, o utilizador que executa esta ação tem de ser um administrador global no Azure AD. 

Se não for membro da instância do Active Directory da subscrição antes de ser adicionado à função de administrador global/coadministrador da mesma, é adicionado ao Active Directory como convidado. Nesta situação, recebe o aviso "Não tem permissões para criar..." no painel **Adicionar Conta de Automatização**. Os utilizadores que foram adicionados primeiro à função de administrador global/coadministrador podem ser removidos da instância do Active Directory da subscrição e adicionados novamente, para que se tornem em Utilizadores completos no Active Directory. Para verificar esta situação, no painel **Azure Active Directory**, no portal do Azure, selecione **Utilizadores e grupos**, **Todos os utilizadores** e, depois de selecionar o utilizador específico, selecione **Perfil**. O valor do atributo **Tipo de utilizador** sob o perfil de utilizadores não deve ser igual a **Convidado**.

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Criar uma nova Conta de Automatização a partir do portal do Azure
Nesta secção, executa os seguintes passos para criar uma conta de Automatização do Azure no portal do Azure.    

1. Inicie sessão no portal do Azure com uma conta que seja membro da função Administradores da Subscrição e coadministrador da subscrição.
2. Clique em **Novo**.<br><br> ![Selecione a Nova opção no portal do Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Procure a **Automatização** e, em seguida, nos resultados da pesquisa selecione **Automatização & Controlo***.<br><br> ![Procure e selecione Automatização a partir do Mercado](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. No painel Contas de Automatização, clique em **Adicionar**.<br><br>![Adicionar Conta de Automatização](media/automation-create-standalone-account/automation-create-automationacct-properties.png)


   > [!NOTE]
   > Se vir o seguinte aviso no painel **Adicionar Conta de Automatização**, isto acontece porque a conta não é membro da função de Administradores da Subscrição nem coadministradora da subscrição.<br><br>![Adicionar Aviso de Conta de Automatização](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. No painel **Adicionar Conta de Automatização**, no tipo de caixa **Nome**, escreva um nome para a sua nova conta de Automatização.
5. Se tiver mais do que uma subscrição, especifique uma para a nova conta, um **Grupo de recursos** novo ou existente e uma **Localização** do datacenter do Azure.
6. Verifique se valor **Sim** está selecionado para a opção **Criar conta Run As do Azure** e clique no botão **Criar**.  
   
   > [!NOTE]
   > Se optar por não criar a conta Run As, selecionando a opção **Não**, é apresentada uma mensagem de aviso no painel **Adicionar Conta de Automatização**.  Enquanto a conta é criada no portal do Azure, não tem uma identidade de autenticação correspondente no seu serviço de diretório de subscrições clássico ou do Gestor de Recursos e, por isso, não terá acesso a recursos na sua subscrição.  Isto impede que todos os runbooks relacionados com esta conta tenham capacidade para autenticar e executar tarefas relativamente no que respeita a recursos nesses modelos de implementação.
   > 
   > ![Adicionar Aviso de Conta de Automatização](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > Se o principal de serviço não for criado, a função Contribuidor não é atribuída.
   > 

7. Enquanto o Azure cria a conta de automatização, pode acompanhar o progresso em **Notificações** a partir do menu.

### <a name="resources-included"></a>Recursos incluídos
Quando a conta de Automatização é criada com sucesso, são criados vários recursos automaticamente para si.  A tabela seguinte resume os recursos para a conta Run As.<br>

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial |Um runbook gráfico de exemplo que demonstra como efetuar a autenticação com a conta Run As e obtém todos os recursos do Resource Manager. |
| AzureAutomationTutorialScript Runbook |Um runbook do PowerShell de exemplo que demonstra como efetuar a autenticação com a conta Run As e obtém todos os recursos do Gestor de Recursos. |
| Runbook AzureAutomationTutorialPython2 |Um runbook python de exemplo que demonstra como autenticar através da utilização da conta Run As e que, em seguida, lista os grupos de recursos presentes na subscrição especificada. |
| AzureRunAsCertificate |Recurso de certificado criado automaticamente durante a criação da conta de Automatização ou utilizando o script do PowerShell abaixo para uma conta existente.  Permite-lhe autenticar com o Azure, para que possa gerir recursos do Azure Resource Manager a partir dos runbooks.  Este certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Recurso de ligação criado automaticamente durante a criação da conta de Automatização ou utilizando o script do PowerShell abaixo para uma conta existente. |

A tabela seguinte resume os recursos da conta Run As clássica.<br>

| Recurso | Descrição |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |Um runbook gráfico de exemplo que obtém todas as VMs clássicas numa subscrição utilizando a conta Run As clássica (certificado) e, em seguida, devolve o nome e o estado da VM. |
| AzureClassicAutomationTutorial Script Runbook |Um runbook do PowerShell de exemplo que obtém todas as VMs clássicas numa subscrição utilizando a conta Run As clássica (certificado) e, em seguida, devolve o nome e o estado da VM. |
| AzureClassicRunAsCertificate |Recurso de certificado criado automaticamente utilizado para autenticar com o Azure, para que possa gerir os recursos clássicos do Azure a partir de runbooks.  Este certificado tem um tempo de vida de um ano. |
| AzureClassicRunAsConnection |Recurso de ligação criado automaticamente, que é utilizado para autenticar com o Azure, para que possa gerir os recursos clássicos do Azure a partir de runbooks. |


## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre a Criação de Gráficos, consulte [Graphical authoring in Azure Automation (Criação de gráficos na Automatização do Azure)](automation-graphical-authoring-intro.md).
* Para começar com runbooks do PowerShell, consulte [My first PowerShell runbook (O meu primeiro runbook do PowerShell)](automation-first-runbook-textual-powershell.md).
* Para começar com runbooks do fluxo de trabalho do PowerShell, veja [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para obter uma introdução aos runbooks Python2, veja [My first Python2 runbook](automation-first-runbook-textual-python2.md) (O meu primeiro runbook Python2).

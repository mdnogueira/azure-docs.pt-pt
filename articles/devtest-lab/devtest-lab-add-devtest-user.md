---
title: "Adicionar utilizadores e proprietários no Azure DevTest Labs | Microsoft Docs"
description: "Adicionar utilizadores e proprietários no Azure DevTest Labs utilizando o portal do Azure ou o PowerShell"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
ms.openlocfilehash: d67fa257574d6cb4ad4b18521900374fb51da290
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Adicionar utilizadores e proprietários no Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

O acesso no Azure DevTest Labs é controlado pelo [controlo de acesso em funções do Azure (RBAC)](../active-directory/role-based-access-control-what-is.md). Utilizar o RBAC, pode segregar funções na sua equipa no *funções* onde pode conceder apenas a quantidade de acesso necessário para os utilizadores para desempenhar as suas funções. Três destas funções do RBAC são *proprietário*, *DevTest Labs utilizador*, e *contribuinte*. Neste artigo, saiba o que ações podem ser efetuadas em cada uma das três funções RBAC principais. A partir daí, saiba como adicionar utilizadores a um laboratório - através do portal e através de um script do PowerShell e como adicionar utilizadores ao nível da subscrição.

## <a name="actions-that-can-be-performed-in-each-role"></a>Ações que podem ser executadas em cada função
Existem três funções principais que pode atribuir um utilizador:

* Proprietário
* DevTest Labs utilizador
* Contribuinte

A tabela seguinte ilustra as ações que podem ser efetuadas pelos utilizadores em cada uma destas funções:

| **Podem efetuar ações os utilizadores com esta função** | **DevTest Labs utilizador** | **Proprietário** | **Contribuinte** |
| --- | --- | --- | --- |
| **Tarefas de laboratório** | | | |
| Adicionar utilizadores a um laboratório |Não |Sim |Não |
| Atualizar as definições de custos |Não |Sim |Sim |
| **Tarefas de base de VM** | | | |
| Adicionar e remover imagens personalizadas |Não |Sim |Sim |
| Adicionar, atualizar e eliminar as fórmulas |Sim |Sim |Sim |
| Imagens da lista branca Azure Marketplace |Não |Sim |Sim |
| **Tarefas VM** | | | |
| Criar VMs |Sim |Sim |Sim |
| Iniciar, parar e eliminar as VMs |Apenas as VMs criadas pelo utilizador |Sim |Sim |
| Atualizar as políticas de VM |Não |Sim |Sim |
| Adicionar/remover discos de dados do VMs |Apenas as VMs criadas pelo utilizador |Sim |Sim |
| **Tarefas de artefactos** | | | |
| Adicionar e remover repositórios de artefactos |Não |Sim |Sim |
| Aplicam-se de artefactos |Sim |Sim |Sim |

> [!NOTE]
> Quando um utilizador cria uma VM, esse utilizador é atribuído automaticamente para o **proprietário** função da VM criada.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Adicionar um proprietário ou o utilizador ao nível do laboratório
Os proprietários e os utilizadores podem ser adicionados ao nível do laboratório através do portal do Azure. Isto inclui os utilizadores externos com um [conta Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Os seguintes passos guiá-lo durante o processo de adicionar um utilizador ou proprietário para um laboratório no Azure DevTest Labs:

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais serviços**, e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.
4. No painel do laboratório, selecione **configuração**. 
5. No **configuração** painel, selecione **utilizadores**.
6. No **utilizadores** painel, selecione **+ adicionar**.
   
    ![Adicionar utilizador](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
7. No **selecionar uma função** painel, selecione a função pretendida. A secção [ações que podem ser executadas em cada função](#actions-that-can-be-performed-in-each-role) lista as várias ações que podem ser efetuadas pelos utilizadores nas funções de proprietário, utilizador de DevTest e contribuinte.
8. No **adicionar utilizadores** painel, introduza o endereço de e-mail ou nome do utilizador que pretende adicionar a função especificada. Se o utilizador não for encontrado, uma mensagem de erro explica o problema. Se o utilizador for encontrado, esse utilizador é listado e seleccionado. 
9. Selecione **selecione**.
10. Selecione **OK** para fechar o **adicionar acesso** painel.
11. Quando regressar ao **utilizadores** painel, o utilizador foi adicionado.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Adicionar um utilizador externo a um laboratório com o PowerShell
Para além de adicionar utilizadores no portal do Azure, pode adicionar um utilizador externo para o laboratório utilizar um script do PowerShell. No exemplo seguinte, basta modificar os valores de parâmetros nos **valores para alterar** comentário.
Pode obter o `subscriptionId`, `labResourceGroup`, e `labName` valores a partir do painel de laboratório no portal do Azure.

> [!NOTE]
> O script de exemplo assume que o utilizador especificado foi adicionado como convidado ao Active Directory e irá falhar se não for esse o caso. Para adicionar um utilizador não no Active Directory para um laboratório, utilize o portal do Azure para atribuir o utilizador a uma função, conforme ilustrado na secção, [adicionar um proprietário ou o utilizador ao nível do laboratório](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Adicionar um proprietário ou o utilizador ao nível da subscrição
Permissões do Azure são propagadas do âmbito principal para o âmbito subordinado no Azure. Por conseguinte, os proprietários de uma subscrição do Azure que contém laboratórios são automaticamente os proprietários desses laboratórios. Também possuem as VMs e outros recursos criados por utilizadores o laboratório e o serviço de Azure DevTest Labs. 

Pode adicionar proprietários adicionais a um laboratório através do painel o laboratório no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). No entanto, o âmbito do proprietário adicionado da administração é mais restritos ao âmbito do proprietário da subscrição. Por exemplo, os proprietários adicionados não têm acesso total a alguns recursos que são criados pelo serviço DevTest Labs na subscrição. 

Para adicionar um proprietário para uma subscrição do Azure, siga estes passos:

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **subscrições** da lista.
3. Selecione a subscrição pretendida.
4. Selecione **acesso** ícone. 
   
    ![Utilizadores de acesso](./media/devtest-lab-add-devtest-user/access-users.png)
5. No **utilizadores** painel, selecione **adicionar**.
   
    ![Adicionar utilizador](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. No **selecionar uma função** painel, selecione **proprietário**.
7. No **adicionar utilizadores** painel, introduza o endereço de e-mail ou nome do utilizador que pretende adicionar como um proprietário. Se o utilizador não for encontrado, receberá uma mensagem de erro explicar o problema. Se o utilizador for encontrado, esse utilizador é apresentado no **utilizador** caixa de texto.
8. Selecione o nome de utilizador localizado.
9. Selecione **selecione**.
10. Selecione **OK** para fechar o **adicionar acesso** painel.
11. Quando regressar ao **utilizadores** painel, o utilizador foi adicionado como um proprietário. Este utilizador é agora um proprietário de quaisquer laboratórios criado pertencentes a esta subscrição e, por conseguinte, ser capazes de efetuar tarefas de proprietário. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]


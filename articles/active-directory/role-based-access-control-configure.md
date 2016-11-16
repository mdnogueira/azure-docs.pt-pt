---
title: "Utilizar o Controlo de Acesso Baseado em Funções no Portal do Azure | Microsoft Docs"
description: "Introdução à gestão de acessos com Controlo de Acesso Baseado em Funções no Portal do Azure. Utilize atribuições de funções para atribuir permissões aos seus recursos."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b930fe0a4a47cffebb579be43a77e87548549cfd


---
# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure
> [!div class="op_single_selector"]
> * [Gerir o acesso por utilizador ou grupo](role-based-access-control-manage-assignments.md)
> * [Gerir o acesso por recurso](role-based-access-control-configure.md)
> 
> 

O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso precisa aos utilizadores para realizar os trabalhos. Este artigo ajuda-o a começar a utilizar o RBAC no Portal do Azure. Se quiser saber mais sobre como o RBAC pode ajudá-lo a gerir o acesso, consulte o artigo [O que é o Controlo de Acesso Baseado em Funções](role-based-access-control-what-is.md).

## <a name="view-access"></a>Visualizar o acesso
Pode ver quem tem acesso a um recurso, grupo de recursos ou subscrição, a partir do respetivo painel principal no [Portal do Azure](https://portal.azure.com). Por exemplo, queremos ver quem tem acesso a um dos nossos grupos de recursos:

1. Selecione **Grupos de recursos** na barra de navegação à esquerda.  
    ![Grupos de recursos - ícone](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Selecione o nome do grupo de recursos no painel **Grupos de recursos**.
3. Selecione **Controlo de acesso (IAM)** no menu da esquerda.  
4. O painel Controlo de acesso lista todos os utilizadores, grupos e aplicações com acesso ao grupo de recursos.  
   
    ![Captura de ecrã Painel de utilizadores – acesso herdado por oposição a atribuído](./media/role-based-access-control-configure/view-access.png)

Tenha em atenção que alguns utilizadores tiveram acesso **Atribuído** enquanto outros **Herdaram**-no. O acesso é atribuído especificamente ao grupo de recursos ou herdado de uma atribuição à subscrição principal.

> [!NOTE]
> Os administradores e coadministradores de subscrição clássica são considerados proprietários da subscrição no novo modelo RBAC.
> 
> 

## <a name="add-access"></a>Adicionar Acesso
Pode conceder acesso de dentro do recurso, grupo de recursos ou subscrição, que é o âmbito da atribuição de função.

1. Selecione **Adicionar** no painel Controlo de acesso.  
2. Selecione a função que pretende atribuir no painel **Selecionar uma função**.
3. Selecione o utilizador, grupo ou aplicação no diretório para o qual pretende dar acesso. Pode procurar o diretório com os nomes a apresentar, endereços de correio eletrónico e identificadores de objetos.  
   
    ![Captura de ecrã Painel adicionar utilizadores – pesquisa](./media/role-based-access-control-configure/grant-access2.png)
4. Selecione **OK** para criar a atribuição. O pop-up **Adicionar utilizador** controla o progresso.  
    ![Captura de ecrã – barra de progresso de adicionar utilizador](./media/role-based-access-control-configure/addinguser_popup.png)

Depois de adicionar com êxito uma atribuição de função, esta será apresentada no painel **Utilizadores**.

## <a name="remove-access"></a>Remover o Acesso
1. Selecione a atribuição de funções no painel Controlo de acesso.
2. Selecione **Remover** no painel de detalhes de atribuição.  
3. Selecione **sim** para confirmar a remoção.  
    ![Captura de ecrã Painel de utilizadores – remover de função](./media/role-based-access-control-configure/remove-access1.png)

Não foi possível remover atribuições herdadas. Repare na imagem abaixo onde o botão Remover está a cinzento. Em vez disso, repare no detalhe **Atribuído Em**. Ir para o recurso aí listado para remover a atribuição de função.

![Painel de utilizadores – captura de ecrã do botão de acesso herdado desativa remover](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Outras ferramentas para gerir acessos
Pode atribuir funções e gerir acessos com os comandos de Azure RBAC noutras ferramentas diferentes do Portal do Azure.  Siga as ligações para obter mais informações sobre os pré-requisitos e começar a utilizar os comandos do RBAC do Azure.

* [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
* [Interface de Linha de Comandos do Azure](role-based-access-control-manage-access-azure-cli.md)
* [API REST](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Passos Seguintes
* [Criar um relatório de histórico de alteração do acesso](role-based-access-control-access-change-history-report.md)
* Consulte as [Funções incorporadas do RBAC](role-based-access-built-in-roles.md)
* Definir as suas [Funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md)




<!--HONumber=Nov16_HO2-->



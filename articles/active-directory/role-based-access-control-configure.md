---
title: "Controlo de Acesso Baseado em Funções no portal do Azure | Microsoft Docs"
description: "Introdução à gestão de acessos com Controlo de Acesso Baseado em Funções no Portal do Azure. Utilize atribuições de funções para atribuir permissões aos seus recursos."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 9df7f7851ef1fc6b4ed03b981aa5062d6b0913ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-role-based-access-control-to-manage-access-to-your-azure-subscription-resources"></a>Utilize o Controlo de Acesso Baseado em Função para gerir o acesso aos recursos de subscrição do Azure
> [!div class="op_single_selector"]
> * [Gerir o acesso por utilizador ou grupo](role-based-access-control-manage-assignments.md)
> * [Gerir o acesso por recurso](role-based-access-control-configure.md)

O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso precisa aos utilizadores para realizar os trabalhos. Este artigo ajuda-o a começar a utilizar o RBAC no Portal do Azure. Se quiser saber mais sobre como o RBAC pode ajudá-lo a gerir o acesso, consulte o artigo [O que é o Controlo de Acesso Baseado em Funções](role-based-access-control-what-is.md).

Pode conceder até duas mil atribuições de funções em cada subscrição. 

## <a name="view-access"></a>Visualizar o acesso
Pode ver quem tem acesso a um recurso, grupo de recursos ou subscrição, a partir do respetivo painel principal no [Portal do Azure](https://portal.azure.com). Por exemplo, queremos ver quem tem acesso a um dos nossos grupos de recursos:

1. Selecione **Grupos de recursos** na barra de navegação à esquerda.  
    ![Grupos de recursos - ícone](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Selecione o nome do grupo de recursos no painel **Grupos de recursos**.
3. Selecione **Controlo de acesso (IAM)** no menu da esquerda.  
4. O painel Controlo de acesso lista todos os utilizadores, grupos e aplicações com acesso ao grupo de recursos.  
   
    ![Captura de ecrã Painel de utilizadores – acesso herdado por oposição a atribuído](./media/role-based-access-control-configure/view-access.png)

Tenha em conta que algumas funções têm o âmbito **Este recurso**, ao passo que outras são **herdadas** de outro âmbito. O acesso é atribuído especificamente ao grupo de recursos ou herdado de uma atribuição à subscrição principal.

> [!NOTE]
> Os administradores e coadministradores de subscrição clássica são considerados proprietários da subscrição no novo modelo RBAC.

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
1. Coloque o cursor sobre o nome da atribuição que pretende remover. É apresentada uma caixa de verificação junto ao nome.
2. Utilize as caixas de verificação para selecionar uma ou mais atribuições de funções.
2. Selecione **Remover**.  
3. Selecione **Sim** para confirmar a remoção.

Não foi possível remover atribuições herdadas. Se precisar de remover uma atribuição herdada, terá de o fazer no âmbito em que a atribuição da função foi criada. Na coluna **Âmbito**, junto a **Herdado**, existe uma ligação que o leva aos recursos onde esta função foi atribuída. Ir para o recurso aí listado para remover a atribuição de função.

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


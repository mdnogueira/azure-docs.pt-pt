---
title: Partilhar dashboards de portais do Azure utilizando o RBAC | Microsoft Docs
description: "Este artigo explica como partilhar um dashboard no portal do Azure utilizando o controlo de acesso baseado em funções."
services: azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: multiple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: ea0cf7ad074f95c2b49a92f9a8e32270a1d39b3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Partilhar dashboards do Azure utilizando o controlo de acesso baseado em funções
Depois de configurar um dashboard, pode publicá-lo e partilhá-las com outros utilizadores na sua organização. Permitir que os outros possam ver o dashboard através do Azure [controlo de acesso baseado em funções](../active-directory/role-based-access-control-configure.md). Atribuir um utilizador ou grupo de utilizadores a uma função e essa função define se esses utilizadores podem ver ou modificar o dashboard publicado. 

Todos os dashboards publicados são implementados como recursos do Azure, o que significa que existem como itens geridos na sua subscrição e estão contidos num grupo de recursos.  De uma perspetiva de controlo de acesso, os dashboards são não diferentes a outros recursos, tais como uma máquina virtual ou uma conta de armazenamento.

> [!TIP]
> Individuais mosaicos no dashboard impõem os seus próprios requisitos de controlo de acesso baseados nos recursos que medida que são apresentados.  Por conseguinte, pode criar um dashboard que é partilhado amplamente mantendo a proteção de dados nos mosaicos individuais.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Controlo de acesso de compreender para dashboards
Com baseada em funções controlo de acesso (RBAC), pode atribuir utilizadores a funções de três níveis diferentes de âmbito:

* subscrição
* grupo de recursos
* Recursos

As permissões que atribuir são herdadas da subscrição para o recurso. O dashboard publicado é um recurso. Por conseguinte, pode já ter utilizadores atribuídos a funções para a subscrição que também funcionam para o dashboard publicado. 

Eis um exemplo.  Vamos supor que tiver uma subscrição do Azure e vários membros da sua equipa foram atribuídos as funções de **proprietário**, **contribuinte**, ou **leitor** da subscrição. Os utilizadores que são proprietários ou contribuintes são capazes de lista, ver, criar, modificar ou eliminar dashboards dentro da subscrição.  Os utilizadores que são leitores são capazes de lista e vista dashboards, mas não é possível modificar ou eliminar.  Os utilizadores com acesso de leitor são capazes de efetuar edições de locais para um dashboard publicado (tal como quando um problema de resolução de problemas), mas não conseguem publicar as alterações no servidor.  Terá a opção para efetuar uma cópia privada do dashboard para si próprios

No entanto, também pode atribuir permissões para o grupo de recursos que contém vários dashboards ou a um dashboard individuais. Por exemplo, pode decidir que um grupo de utilizadores deve limitada permissões em toda a subscrição mas maior aceder a um dashboard específico. Atribuir aos utilizadores a uma função para esse dashboard. 

## <a name="publish-dashboard"></a>Publicar o dashboard
Vamos imaginar que tiver concluído a configuração de um dashboard que pretende partilhar com um grupo de utilizadores na sua subscrição. Os passos abaixo depict um grupo personalizado denominado gestores de armazenamento, mas pode atribuir o nome do grupo que gostaria de. Para obter informações sobre como criar um grupo do Active Directory e adicionar utilizadores a esse grupo, consulte [gerir grupos no Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

1. No dashboard, selecione **partilha**.
   
     ![Selecione a partilha](./media/azure-portal-dashboard-share-access/select-share.png)
2. Antes de atribuir acesso, tem de publicar o dashboard. Por predefinição, o dashboard será publicado num grupo de recursos com o nome **dashboards**. Selecione **publicar**.
   
     ![publicar](./media/azure-portal-dashboard-share-access/publish.png)

O dashboard agora é publicado. Se as permissões herdadas da subscrição são adequadas, não terá de fazer mais nada. Outros utilizadores na sua organização poderá aceder e modificar o dashboard com base na respetiva função de nível de subscrição. No entanto, para este tutorial, vamos atribuir um grupo de utilizadores a uma função para esse dashboard.

## <a name="assign-access-to-a-dashboard"></a>Atribuir acesso a um dashboard
1. Depois de publicar o dashboard, selecione **gerir utilizadores**.
   
     ![gerir utilizadores](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Verá uma lista de utilizadores existentes que já estão atribuídos uma função para este dashboard. A lista de utilizadores existentes serão diferente que a imagem abaixo. Provavelmente, as atribuições são herdadas da subscrição. Para adicionar um novo utilizador ou grupo, selecione **adicionar**.
   
     ![Adicionar utilizador](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Selecione a função que representa as permissões que pretende conceder. Para este exemplo, selecione **contribuinte**.
   
     ![Selecione a função](./media/azure-portal-dashboard-share-access/select-role.png)
4. Selecione o utilizador ou grupo que pretende atribuir à função. Se não vir o utilizador ou grupo que procura na lista, utilize a caixa de pesquisa. A lista de grupos disponíveis dependerão dos grupos que criou no Active Directory.
   
     ![Selecionar utilizador](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Quando tiver concluído a adição de utilizadores ou grupos, selecione **OK**. 
6. A nova atribuição é adicionada à lista de utilizadores. Tenha em atenção que o **acesso** está listado como **atribuído** vez **herdado**.
   
     ![funções atribuídas](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Passos seguintes
* Para obter uma lista de funções, consulte [RBAC: funções incorporadas](../active-directory/role-based-access-built-in-roles.md).
* Para saber mais sobre a gestão de recursos, consulte [recursos do Azure de gerir através do portal](resource-group-portal.md).


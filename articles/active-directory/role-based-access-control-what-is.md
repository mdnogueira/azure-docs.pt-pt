---
title: "Gerir o acesso e permissões com RBAC - RBAC do Azure | Microsoft Docs"
description: "Introdução à gestão de acessos com controlo de acesso baseado em funções do Azure no Portal do Azure. Utilize atribuições de funções para atribuir permissões no diretório."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 0462fe8ff75bdda397decb301c459795886e9e58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Introdução ao controlo de acesso baseado em funções no portal do Azure
Segurança e orientado para empresas devem focar-se em fornecer aos funcionários as permissões exatas que precisam. Demasiados permissões podem expor uma conta para os atacantes. Permissões insuficientes significa que os funcionários não é possível obter o trabalho feito de forma eficiente. Azure baseada em funções controlo de acesso (RBAC) ajuda a resolver este problema, oferecendo gestão de acesso detalhada para o Azure.

Utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso aos utilizadores que precisam para desempenhar as suas funções. Em vez de dar everybody sem restrições permissões na sua subscrição do Azure ou recursos, pode permitir que apenas determinadas ações. Por exemplo, utilize o RBAC para permitir que um empregado gerir máquinas virtuais numa subscrição, enquanto outro pode gerir bases de dados SQL dentro da mesma subscrição.

## <a name="basics-of-access-management-in-azure"></a>Noções básicas de gestão de acesso no Azure
Cada subscrição do Azure está associada um diretório do Azure Active Directory (AD). Os utilizadores, grupos e a esse diretório de aplicações podem gerir os recursos na subscrição do Azure. Atribua estes direitos de acesso utilizando o portal do Azure, as ferramentas da linha de comandos do Azure e APIs de gestão do Azure.

Conceder acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações num determinado âmbito. O âmbito de uma atribuição de função pode ser uma subscrição, um grupo de recursos ou um único recurso. Uma função atribuída a um âmbito principal também concede acesso a subordinados contidos. Por exemplo, um utilizador com acesso a um grupo de recursos pode gerir todos os recursos que contém, como Web sites, sub-redes e máquinas virtuais.

![Relação entre elementos do Azure Active Directory - diagrama](./media/role-based-access-control-what-is/rbac_aad.png)

A função RBAC que atribuir determinam os recursos que o utilizador, grupo ou aplicação pode gerir esse âmbito.

## <a name="built-in-roles"></a>Funções incorporadas
RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recursos:

* **Proprietário** tem acesso total a todos os recursos, incluindo o direito para delegar o acesso a outras pessoas.
* **Contribuidor** pode criar e gerir todos os tipos de recursos do Azure, mas não é possível conceder acesso a outras pessoas.
* **Leitor** pode ver os recursos do Azure existentes.

O resto das funções do RBAC do Azure permite a gestão de recursos do Azure específicos. Por exemplo, a função de contribuinte de Máquina Virtual permite ao utilizador criar e gerir máquinas virtuais. Se não atribuir-lhes acesso para a rede virtual ou a sub-rede que a máquina virtual estabelece ligação ao. 

[Funções incorporadas do RBAC](role-based-access-built-in-roles.md) lista as funções disponíveis no Azure. Especifica as operações e o âmbito de cada função incorporada concede a utilizadores. Se estiver à procura para definir as suas próprias funções para o controlo ainda mais, consulte como criar [funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Herança de recursos de acesso e de hierarquia
* Cada **subscrição** no Azure pertence a um só diretório. (Mas cada diretório pode ter mais do que uma subscrição).
* Cada **grupo de recursos** pertence a apenas uma subscrição.
* Cada **recursos** pertence a apenas um grupo de recursos.

Acesso que conceda em âmbitos principal é herdado, os âmbitos subordinados. Por exemplo:

* Atribuir a função de leitor a um grupo do Azure AD no âmbito de subscrição. Os membros desse grupo podem ver cada grupo de recursos e recursos na subscrição.
* Atribuir a função de contribuinte a uma aplicação no âmbito do grupo de recursos. -Pode gerir os recursos de todos os tipos nesse grupo de recursos, mas não outros grupos de recursos na subscrição.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>RBAC do Azure vs. os administradores da subscrição clássica
Os administradores da subscrição clássico e coadministradores têm acesso total à subscrição do Azure. Podem gerir recursos com o [portal do Azure](https://portal.azure.com) com APIs do Azure Resource Manager, ou o [portal clássico do Azure](https://manage.windowsazure.com) e o modelo de implementação clássico do Azure. No modelo RBAC, os administradores clássicos são atribuídos a função de proprietário no âmbito de subscrição.

Apenas o portal do Azure e as novas APIs do Azure Resource Manager suportam RBAC do Azure. Utilizadores e aplicações que são atribuídas a funções RBAC não é possível utilizar o portal de gestão clássico e o modelo de implementação clássico do Azure.

## <a name="authorization-for-management-vs-data-operations"></a>Autorização para gestão versus operações de dados
RBAC do Azure suporta apenas operações de gestão de recursos do Azure no portal do Azure e APIs do Azure Resource Manager. Não é possível autorizar o todas as operações de nível de dados para recursos do Azure. Por exemplo, podem autorizar alguém para gerir contas de armazenamento, mas não os blobs ou tabelas dentro de uma conta de armazenamento. Da mesma forma, uma base de dados do SQL Server pode ser gerido, mas não as tabelas dentro da mesma.

## <a name="next-steps"></a>Passos Seguintes
* Introdução ao [controlo de acesso baseado em funções no portal do Azure](role-based-access-control-configure.md).
* Consulte as [Funções incorporadas do RBAC](role-based-access-built-in-roles.md)
* Definir as suas [Funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md)

---
title: Gerir grupos no Azure Active Directory | Microsoft Docs
description: Como criar e administrar grupos para gerir utilizadores do Azure com o Azure Active Directory.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d1f5451c-3807-423c-8bac-2822d27b893f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: a29bc68e966a3706af557af0c626d369d04149aa
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-groups-in-azure-active-directory"></a>Gerir grupos no Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Uma das funcionalidades da gestão de utilizadores do Azure Active Directory (Azure AD) é a capacidade de criar grupos de utilizadores. Pode utilizar um grupo para efetuar tarefas de gestão, tais como atribuir licenças ou permissões a vários utilizadores em simultâneo. Pode também utilizar grupos para atribuir permissão de acesso a

* Recursos como objetos no diretório
* Recursos externos ao diretório, como aplicações SaaS, serviços do Azure, sites SharePoint ou recursos no local

Além disso, um proprietário de recursos também pode atribuir acesso de um recurso a um grupo do Azure AD de outra pessoa. Esta atribuição concede aos membros desse grupo acesso ao recurso. O proprietário do grupo gere, então, a filiação no grupo. Efetivamente, o proprietário do recurso delega no proprietário do grupo a permissão de atribuir utilizadores ao seu recurso.

> [!IMPORTANT]
> A Microsoft recomenda que faça a gestão do Azure AD com o [centro de administração do Azure AD](https://aad.portal.azure.com) no portal do Azure em vez de utilizar o portal clássico do Azure referenciado neste artigo. Para saber como gerir grupos no centro de administração do Azure AD, veja [Create a group and add members in Azure Active Directory](active-directory-groups-create-azure-portal.md) (Criar um grupo e adicionar membros no Azure Active Directory).

## <a name="how-do-i-create-a-group"></a>Como crio um grupo?
Dependendo dos serviços para os quais a sua organização tiver subscrito, pode criar um grupo utilizando um dos seguintes procedimentos:

* Portal clássico do Azure
* portal de contas do Office 365
* portal de contas do Windows Intune

Vamos descrever as tarefas, tal como foram executadas no portal clássico do Azure. Para obter mais informações sobre como utilizar portais que não sejam do Azure para gerir o seu diretório do Azure AD,veja o artigo [Administrar o seu diretório do Azure AD](active-directory-administer.md).

1. No [Portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.
2. Selecione o separador **Grupos**.
3. Selecione **Adicionar Grupo**.
4. Na janela **Adicionar Grupo**, especifique o nome e a descrição de um grupo.

## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Como é que adiciono ou removo utilizadores individuais num grupo de segurança?
**Para adicionar um utilizador individual a um grupo**

1. No [Portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.
2. Selecione o separador **Grupos**.
3. Abra o grupo a que pretende adicionar membros. Abra o separador **Membros** do grupo selecionado se ainda estiver apresentado.
4. Selecione **Adicionar membros**.
5. Na página **Adicionar Membros**, selecione o nome do utilizador ou de um grupo que pretende adicionar como membro deste grupo. Certifique-se de que este nome é adicionado ao painel **Selecionado**.

**Para remover um utilizador individual de um grupo**

1. No [Portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.
2. Selecione o separador **Grupos**.
3. Abra o grupo de onde pretende remover membros.
4. Selecione o separador**Membros**, selecione o nome do membro que pretende remover deste grupo e clique em **Remover**.
5. Confirme na linha de comandos que pretende remover este membro do grupo.

## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Como posso gerir dinamicamente a filiação de um grupo?
No Azure AD, pode configurar muito facilmente uma regra simples para determinar que utilizadores são membros do grupo. Uma regra simples é aquela que faz apenas uma única comparação. Por exemplo, se um grupo for atribuído a uma aplicação SaaS, pode configurar uma regra para adicionar utilizadores que tenham um cargo de "Representante de Vendas" Esta regra, em seguida, concede acesso a esta aplicação SaaS a todos os utilizadores com esse cargo no seu diretório.

Quando os atributos de um utilizador mudam, o sistema avalia todas as regras de grupos dinâmicos num diretório para ver se a alteração do atributo do utilizador acionará adições ou remoções no grupo. Se um utilizador cumprir uma regra num grupo, é adicionado como membro a esse grupo. Se deixar de cumprir a regra de um grupo do qual é membro, o utilizador é removido do mesmo.

> [!NOTE]
> Pode configurar uma regra de filiação dinâmica em grupos de segurança ou grupos do Office 365. As filiações aninhadas em grupos não são suportadas atualmente para uma atribuição baseada em grupos para aplicações .
>
> A filiação dinâmica para grupos exige uma licença do Azure AD Premium a ser atribuída
>
> * Ao administrador que gere a regra num grupo
> * Todos os membros do grupo
>
>

**Para ativar a associação de grupo dinâmica num grupo**

1. No [Portal clássico do Azure](https://manage.windowsazure.com), selecione **Active Directory** e, em seguida, selecione o nome do diretório da sua organização.
2. Selecione o separador **Grupos** e abra o grupo que pretende editar.
3. Selecione o separador **Configurar** e defina **Ativar Filiação Dinâmica** para **Sim**.
4. Configure uma única regra simples para o grupo, que controla o modo como a filiação dinâmica funciona para este grupo. Certifique-se de que a opção **Adicionar utilizadores onde** está selecionada e, em seguida, selecione na lista uma propriedade de utilizador (por exemplo, departamento, jobTitle, etc.),
5. Em seguida, selecione uma condição (Não é Igual a, É Igual a, Não Começa Por, Começa Por, Não Contém, Contém, Não Corresponde a, Corresponde a).
6. Especifique um valor de comparação para a propriedade de utilizador selecionada.

Para saber mais sobre como criar regras *avançadas* (regras que podem conter várias comparações) para filiação dinâmica em grupos, consulte o artigo [Utilizar atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Informações adicionais
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [O que é o Azure Active Directory?](active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

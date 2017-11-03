---
title: Dedicado grupos no Azure Active Directory | Microsoft Docs
description: "Descrição geral de como dedicado de grupos de trabalho no Azure Active Directory e como são criados."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 86158909-083a-41fe-8090-955e96ad1865
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;oldportal
ms.openlocfilehash: 992f4563064d7a292cf4fdd90a9a3c84cdec91c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dedicated-groups-in-azure-active-directory"></a>Grupos dedicados no Azure Active Directory
No Azure Active Directory (Azure AD), a funcionalidade de grupos dedicados cria e povoa automaticamente a associação de grupos do Azure AD predefinido. Membros dos grupos dedicados não podem ser adicionados ou removidos utilizando o portal clássico do Azure, os cmdlets do Windows PowerShell, ou através de programação.

> [!NOTE]
> Grupos dedicados requerem que está atribuída uma licença do Azure AD Premium
>
> * O administrador que gere a regra num grupo
> * todos os utilizadores que estão selecionados pela regra seja um membro do grupo
>
>

**Para ativar grupos dedicados**

1. No [portal do Azure](https://portal.azure.com), selecione **do Active Directory**e, em seguida, abra o diretório da sua organização.
2. Selecione o **grupos** separador e, em seguida, abra o grupo que pretende editar.
3. Selecione o **configurar** separador e, em seguida, defina **ative os grupos dedicados** para **Sim**.

Assim que o comutador de ativar grupos dedicados está definido como **Sim**, ainda pode ativar o diretório criar automaticamente o grupo de dedicada de todos os utilizadores, definindo o **ativar "Todos os utilizadores" grupo** mudar para  **Sim**. Em seguida, também pode editar o nome deste grupo dedicado escrevendo o mesmo a **nome a apresentar para "Todos os utilizadores" grupo** campo.

O grupo de todos os utilizadores pode ser utilizado para atribuir as mesmas permissões para todos os utilizadores no seu diretório. Por exemplo, pode conceder todos os utilizadores o acesso de diretório para uma aplicação SaaS através da atribuição de acesso para o grupo de dedicada de todos os utilizadores para esta aplicação.

O grupo de todos os utilizadores dedicado inclui todos os utilizadores no diretório, incluindo utilizadores externos e de convidados. Se precisar de um grupo que exclui os utilizadores externos, em seguida, pode conseguir isto ao criar um grupo com uma regra dinâmica baseadas em atributos como as seguintes:

                (user.userPrincipalName -notContains "#EXT#@")

Para um grupo que exclua todos os convidados, utilize uma regra, como o seguinte:

                (user.userType -ne "Guest")

Para saber mais sobre como criar regras *avançadas* (regras que podem conter várias comparações) para filiação dinâmica em grupos, consulte o artigo [Utilizar atributos para criar regras avançadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

### <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [O que é o Azure Active Directory?](active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

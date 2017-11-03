---
title: "Migrar políticas clássicas no portal do Azure | Microsoft Docs"
description: "Migre políticas clássicas no portal do Azure."
services: active-directory
keywords: "acesso condicional para aplicações, o acesso condicional com o Azure AD, o acesso seguro aos recursos da empresa, as políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migrar políticas clássicas no portal do Azure 


[Acesso condicional](active-directory-conditional-access-azure-portal.md) é uma funcionalidade do Azure Active directory (Azure AD), que lhe permite controlar como autorizado os utilizadores acedam as aplicações na nuvem. Enquanto o objetivo ainda for o mesmo, a versão do portal do Azure também trouxe melhorias significativas para condicional como funciona o acesso. As políticas de acesso condicional que configurou fora do portal do Azure podem coexistir com as novas políticas que está a criar no portal do Azure. Desde que não são desativação ou removê-los, ainda são aplicadas no seu ambiente. No entanto, recomendamos que migre as políticas de clássicas para o novo Azure AD condicional políticas de acesso porque:

- As novas políticas permitem-lhe para cenários de endereço que não foi possível processar com as políticas de clássicas.

- Pode reduzir o número de políticas, que tem de gerir por consolidá-los.   

Este tópico ajuda a a migração das políticas de clássicas existentes para o novo novo Azure políticas de acesso condicional do AD.


## <a name="classic-policies"></a>Políticas clássicas

As políticas de acesso condicional para o Azure AD e o Intune não tiver criado no portal do Azure são também conhecidos como **políticas clássicas**. Para migrar as suas políticas clássicas, não precisa de ter acesso ao portal clássico do Azure. O portal do Azure fornece-lhe um [ **políticas clássico (pré-visualização)** vista](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) que permite-lhe rever as suas políticas clássicas.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>Abra uma política clássica

**Para abrir uma política clássica:**

1. No [portal do Azure](https://portal.azure.com), navbar esquerdo, clique em **do Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. No **do Azure Active Directory** na página de **gerir** secção, clique em **acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/02.png)
 
2. No **acesso condicional - políticas** na página de **gerir** secção, clique em **políticas clássico (pré-visualização)**.

3. Na lista de políticas clássicas, selecione a política que mais lhe interessam.   

    ![Acesso condicional](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Políticas de acesso condicional do Azure AD

Este tópico fornece-lhe os passos detalhados que lhe permite migrar as suas políticas clássicas sem estar familiarizado com acesso condicional do Azure AD políticas. No entanto, que está a ser familiarizado com os conceitos básicos e terminologia do Azure AD ajuda-o acesso condicional para melhorar a sua experiência de migração.

Consulte:

- [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md) para saber mais sobre os conceitos básicos e a terminologia

- [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) para se familiarizar com a interface de utilizador no portal do Azure


 





## <a name="multi-factor-authentication-policy"></a>Política de autenticação multifator 

Este exemplo mostra como migrar uma política de clássica requer multi-factor authentication * * para uma aplicação de nuvem. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**Para migrar uma política clássica:**

1. [Abrir a política clássica](#open-a-classic-policy) para obter as definições de configuração.
2. Criar uma nova política de acesso condicional do Azure AD para substituir a política de clássica. 


### <a name="create-a-new-conditional-access-policy"></a>Criar uma nova política de acesso condicional


1. No [portal do Azure](https://portal.azure.com), navbar esquerdo, clique em **do Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. No **do Azure Active Directory** na página de **gerir** secção, clique em **acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/02.png)



3. No **acesso condicional** página, para abrir o **novo** página, na barra de ferramentas na parte superior, clique em **adicionar**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/03.png)

4. No **novo** na página de **nome** caixa de texto, escreva um nome para a sua política.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/29.png)

5. No **atribuições** secção, clique em **utilizadores e grupos**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/05.png)

    a. Se tiver de todos os utilizadores selecionados na sua política clássica, clique em **todos os utilizadores**. 

    ![Acesso condicional](./media/active-directory-conditional-access-migration/35.png)

    b. Se tiver selecionados na sua política clássica de grupos, clique em **selecionar utilizadores e grupos**e, em seguida, selecione os utilizadores necessários e grupos.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/36.png)

    c. Se tiver os grupos excluídos, clique em de **excluir** separador e, em seguida, selecione os utilizadores necessários e grupos. 

    ![Acesso condicional](./media/active-directory-conditional-access-migration/37.png)

6. No **novo** página, para abrir o **aplicações em nuvem** na página de **atribuição** secção, clique em **aplicações em nuvem**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. No **aplicações em nuvem** página, execute os seguintes passos:

    ![Acesso condicional](./media/active-directory-conditional-access-migration/08.png)

    a. Clique em **selecionar aplicações**.

    b. Clique em **Selecionar**.

    c. No **selecione** página, selecione a sua aplicação de nuvem e, em seguida, clique em **selecione**.

    d. No **aplicações em nuvem** página, clique em **feito**.



9. Se tiver **requer autenticação multifator** selecionado:

    ![Acesso condicional](./media/active-directory-conditional-access-migration/26.png)

    a. No **controlos de acesso** secção, clique em **conceder**.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/27.png)

    b. No **conceder** página, clique em **conceder acesso**e, em seguida, clique em **requer autenticação multifator**.

    c. Clique em **Selecionar**.


10. Clique em **no** para ativar a política.

    ![Acesso condicional](./media/active-directory-conditional-access-migration/30.png)

11. Desative a política clássica. 

    ![Acesso condicional](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>Passos seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte [introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o [melhores práticas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 

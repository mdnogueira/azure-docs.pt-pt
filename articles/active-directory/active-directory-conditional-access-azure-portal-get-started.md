---
title: "Introdução ao acesso condicional no Azure Active Directory | Microsoft Docs"
description: "Acesso condicional através de uma condição de localização de teste."
services: active-directory
keywords: "acesso condicional para aplicações, o acesso condicional com o Azure AD, o acesso seguro aos recursos da empresa, as políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: b9a95cea57f4d0a4a927679cd4802bb56420887a
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Introdução ao acesso condicional no Azure Active Directory

Acesso condicional é uma funcionalidade do Azure Active Directory permite-lhe definir condições sob as quais os utilizadores autorizados podem aceder às suas aplicações. 

Este tópico fornece instruções para um acesso condicional com base numa condição localização no seu ambiente de teste.  


## <a name="scenario-description"></a>Descrição do cenário

É um requisito comuns em muitas organizações para apenas exigir autenticação multifator para acesso a aplicações que não é efetuado a partir da intranet da empresa. Com o Azure Active Directory, pode facilmente de concretizar este objetivo ao configurar uma política de acesso condicional baseado na localização. Este tópico fornece instruções detalhadas para configurar uma política relacionada. A política tira partido da [IPs fidedignos](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) distinguir entre tentativas de acesso da empresa da intranet e todas as outras localizações.


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tópico parte do princípio de que está familiarizado com os conceitos descritos na [acesso condicional do Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Para testar este cenário, tem de:

- Criar um utilizador de teste 

- Atribuir uma licença do Azure AD Premium para o utilizador de teste

- Configurar uma aplicação gerida e atribua o utilizador de teste ao mesmo

- Configurar IPs fidedignos

Se precisar de mais detalhes sobre os IPs fidedignos, consulte [IPs fidedignos](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Passos de configuração de política

**Para configurar a política de acesso condicional, efetue:**

1. No portal do Azure, no navbar esquerdo, clique em **do Azure Active Directory**. 

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. No **do Azure Active Directory** painel, no **gerir** secção, clique em **acesso condicional**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. No **acesso condicional** painel, para abrir o **novo** painel, na barra de ferramentas na parte superior, clique em **adicionar**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. No **novo** painel, no **nome** caixa de texto, escreva um nome para a sua política.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. No **atribuição** secção, clique em **utilizadores e grupos**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. No **utilizadores e grupos** painel, execute os seguintes passos:

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Clique em **selecionar utilizadores e grupos**.

    b. Clique em **Selecionar**.

    c. No **selecione** painel, selecione o utilizador de teste e, em seguida, clique em **selecione**.

    d. No **utilizadores e grupos** painel, clique em **feito**.

7. No **novo** painel, para abrir o **aplicações em nuvem** painel, no **atribuição** secção, clique em **aplicações em nuvem**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. No **aplicações em nuvem** painel, execute os seguintes passos:

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Clique em **selecionar aplicações**.

    b. Clique em **Selecionar**.

    c. No **selecione** painel, selecione a aplicação de nuvem e, em seguida, clique em **selecione**.

    d. No **aplicações em nuvem** painel, clique em **feito**.

9. No **novo** painel, para abrir o **condições** painel, no **atribuição** secção, clique em **condições**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. No **condições** painel, para abrir o **localizações** painel, clique em **localizações**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. No **localizações** painel, execute os seguintes passos:

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. Em **configurar**, clique em **Sim**.

    b. Em **incluir**, clique em **todas as localizações**.

    c. Clique em **excluir**e, em seguida, clique em **todos os IPs fidedignos**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Clique em **Concluído**.

12. No **condições** painel, clique em **feito**.

13. No **novo** painel, para abrir o **conceder** painel, no **controlos** secção, clique em **conceder**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. No **conceder** painel, execute os seguintes passos:

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Selecione **requer autenticação multifator**.

    b. Clique em **Selecionar**.

15. No **novo** painel, em **ativar política de**, clique em **no**.

    ![Acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. No **novo** painel, clique em **criar**.


## <a name="testing-the-policy"></a>Testar a política

Para testar a política, deve aceder a aplicação a partir de um dispositivo que: 

1. Tem um endereço IP que esteja dentro do seu configurados IPs fidedignos 

1. Tem um endereço IP que não está dentro do seu configurados IPs fidedignos

Autenticação multifator só deve ser necessária durante uma tentativa de ligação que foi efetuada a partir de um dispositivo que não está dentro do seu configurados IPs fidedignos. 


## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o acesso condicional, consulte o artigo [acesso condicional do Azure Active Directory](active-directory-conditional-access-azure-portal.md).


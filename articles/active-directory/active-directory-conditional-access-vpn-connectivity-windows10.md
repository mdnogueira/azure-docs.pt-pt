---
title: "Acesso condicional no Active Directory Azure para a conectividade da VPN (pré-visualização) | Microsoft Docs"
description: 'Saiba como funciona o acesso condicional do Azure Active Directory para a conectividade da VPN. '
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: e9dadb3291ee760e7b05caedfa6b4128be77aa7d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>Azure acesso condicional de Active Directory para a conectividade da VPN (pré-visualização)

Com [acesso condicional do Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), pode ajustar a forma como os utilizadores autorizados possam acederem aos recursos. Com o acesso condicional do Azure AD para conectividade de rede privada virtual (VPN), pode ajudar a proteger as suas ligações de VPN.


Para configurar o acesso condicional para conectividade VPN, tem de concluir os seguintes passos: 

1.  Configure o seu servidor VPN.
2.  Configure o cliente VPN.
3.  Configure a política de acesso condicional.


## <a name="before-you-begin"></a>Antes de começar

Este tópico parte do princípio de que está familiarizado com os seguintes tópicos:

- [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN e de acesso condicional](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Para obter informações sobre como o Microsoft implementa esta funcionalidade, consulte [melhorando o acesso remoto no Windows 10 com um perfil VPN automático](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).   


## <a name="prerequisites"></a>Pré-requisitos

Para configurar o acesso condicional do Azure Active Directory para a conectividade da VPN, terá de ter um servidor VPN configurado. 



## <a name="step-1-configure-your-vpn-server"></a>Passo 1: Configurar o seu servidor VPN 

Este passo configura os certificados de raiz para a autenticação da VPN com o Azure AD. Para configurar o acesso condicional para conectividade VPN, tem de:

1. Crie um certificado VPN no portal do Azure.
2. Transfira o certificado VPN.
2. Implemente o certificado para o seu servidor VPN.

AD do Azure utiliza o certificado VPN para assinar os certificados emitidos para clientes Windows 10 na autenticação para o Azure AD para conectividade VPN. O token que os pedidos de cliente Windows 10 é um certificado que, em seguida, apresente a aplicação, que neste caso, é o servidor VPN.

![Transferir o certificado para o acesso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

No portal do Azure, pode criar dois certificados para gerir a transição, quando um certificado está prestes a expirar. Quando cria um certificado, pode escolher se é o certificado principal, o que é utilizado durante a autenticação para assinar o certificado para a ligação.

Para criar um certificado VPN:

1. Inicie sessão no seu [portal do Azure](https://portal.azure.com) como um administrador global.

2. No menu da esquerda, clique em **do Azure Active Directory**. 

    ![Selecione Azure Active Directory](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. No **do Azure Active Directory** na página de **gerir** secção, clique em **acesso condicional**.

    ![Selecione o acesso condicional](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. No **acesso condicional** na página de **gerir** secção, clique em **conectividade VPN (pré-visualização)**.

    ![Selecione a conectividade VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. No **conectividade VPN** página, clique em **novo certificado**.

    ![Selecione o novo certificado](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. No **novo** página, execute os seguintes passos:

    ![Selecione a duração e o site primário](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. Para **selecione duração**, selecione **1 ano**.

    b. Para **primário**, selecione **Sim**.

    c. Clique em **Criar**.

7. Na página de conetividade da VPN, clique em **Transferir certificado**.


Agora, está pronto para implementar o certificado recentemente criado ao seu servidor VPN. No seu servidor VPN, adicionar o certificado transferido como um *as AC de raiz fidedigna para autenticação VPN*.

Para implementações baseadas em Windows RRAS, no seu servidor NPS, adicione o certificado de raiz para o *Enterprise NTauth* armazenar executando os seguintes comandos:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>Passo 2: Configurar o cliente VPN 

Neste passo, poderá configurar o seu perfil de conectividade de cliente VPN conforme descrito na [VPN e acesso condicional](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3-configure-your-conditional-access-policy"></a>Passo 3: Configurar a política de acesso condicional

Esta secção fornece instruções para configurar a política de acesso condicional para conectividade VPN.


1. No **acesso condicional** página, na barra de ferramentas na parte superior, clique em **adicionar**.

    ![Selecione adiciona página de acesso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. No **novo** na página de **nome** caixa, escreva um nome para a sua política. Por exemplo, digite **política VPN**.

    ![Adicionar o nome de política na página de acesso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. No **atribuição** secção, clique em **utilizadores e grupos**.

    ![Selecionar utilizadores e grupos](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. No **utilizadores e grupos** página, execute os seguintes passos:

    ![Utilizador de teste selecione](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Clique em **selecionar utilizadores e grupos**.

    b. Clique em **Selecionar**.

    c. No **selecione** página, selecione o utilizador de teste e, em seguida, clique em **selecione**.

    d. No **utilizadores e grupos** página, clique em **feito**.

7. No **novo** página, execute os seguintes passos:

    ![Selecione as aplicações em nuvem](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. No **atribuições** secção, clique em **aplicações em nuvem**.

    b. No **aplicações em nuvem** página, clique em **selecionar aplicações**e, em seguida, clique em **selecione**.

    c. No **selecione** na página de **aplicações** caixa, escreva **vpn**.

    d. Selecione **servidor VPN**.

    e. Clique em **Selecionar**.


13. No **novo** página, para abrir o **conceder** na página de **controlos** secção, clique em **conceder**.

    ![Concessão Select](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. No **conceder** página, execute os seguintes passos:

    ![Selecione exigem a autenticação multifator](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Selecione **requer autenticação multifator**.

    b. Clique em **Selecionar**.

15. No **novo** página **ativar política de**, clique em **no**.

    ![Ativar a política](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. No **novo** página, clique em **criar**.



## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a forma como o Microsoft implementa esta funcionalidade, consulte [melhorando o acesso remoto no Windows 10 com um perfil VPN automático](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).    


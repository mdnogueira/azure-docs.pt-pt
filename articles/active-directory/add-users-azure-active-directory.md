---
title: Adicionar ou eliminar os utilizadores no Azure Active Directory | Microsoft Docs
description: Explica como adicionar novos utilizadores ou eliminar os utilizadores existentes no Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 0e46ff82c4177de6b33e5df8714318bff83fbb34
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Guia de introdução: Adicionar novos utilizadores ao Azure Active Directory
Este artigo explica como adicionar novos utilizadores na sua organização no Azure Active Directory (Azure AD) um cada vez no portal do Azure ou ao sincronizar os dados da conta de utilizador do Windows Server AD no local. 

## <a name="add-cloud-based-users"></a>Adicionar utilizadores baseados na nuvem
1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **do Azure Active Directory** e, em seguida, **utilizadores e grupos**.
3. No **utilizadores e grupos**, selecione **todos os utilizadores**e, em seguida, selecione **novo utilizador**.
   ![Selecionar o comando Adicionar](./media/add-users-azure-active-directory/add-user.png)
4. Introduza os detalhes para o utilizador, tais como **nome** e **nome de utilizador**. A parte do nome de domínio do nome de utilizador tem de estar ser a predefinição inicial domínio nome "[nome de domínio].onmicrosoft.com" ou um verificado não federada [nome de domínio personalizado](add-custom-domain.md) , tais como "contoso.com".
5. Copie ou caso contrário, tenha em atenção a palavra-passe do utilizador gerado, para que possa proporcioná-lo para o utilizador após a conclusão deste processo.
6. Opcionalmente, pode abrir e preencha as informações no **perfil**, **grupos**, ou **função de diretório** para o utilizador. Para obter mais informações sobre as funções de utilizador e administrador, consulte [Atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles-azure-portal.md).
7. No **utilizador**, selecione **criar**.
8. Distribua em segurança a palavra-passe gerada para o novo utilizador para que o utilizador pode iniciar sessão.

> [!TIP]
> Também pode sincronizar dados de conta de utilizador no local do Windows Server AD. As soluções de identidade da Microsoft span no local e baseado na nuvem capacidades, criar uma identidade de utilizador único para autenticação e autorização para todos os recursos, independentemente da localização. Chamamos esta identidade híbrida. [O Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) pode ser utilizado para integrar os diretórios no local com o Azure Active Directory para cenários de identidade híbrida. Isto permite-lhe fornecer uma identidade comum para o utilizadores das aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD. 

## <a name="delete-users-from-azure-ad"></a>Eliminar utilizadores do Azure AD
1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **utilizadores e grupos**.
3. No **utilizadores e grupos** painel, selecione o utilizador ao eliminar a partir da lista. 
4. No painel de para o utilizador selecionado, selecione **descrição geral**e, em seguida, na barra de comandos, selecione **eliminar**.
   ![Selecionar o comando Adicionar](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Saiba mais 
* [Adicionar utilizadores convidados a partir de outro diretório](active-directory-b2b-what-is-azure-ad-b2b.md) 

* [Atribuir um utilizador a uma função no seu Azure AD](active-directory-users-assign-role-azure-portal.md)

## <a name="next-steps"></a>Passos seguintes
Este guia de introdução, aprendeu como adicionar novos utilizadores ao Azure AD Premium. 

Pode utilizar a hiperligação seguinte para criar um novo utilizador no Azure AD a partir do portal do Azure.

> [!div class="nextstepaction"]
> [Adicionar utilizadores ao Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 
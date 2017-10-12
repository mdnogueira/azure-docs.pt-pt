---
title: "Adicionar utilizadores de outros diretórios ou empresas associadas no Azure Active Directory | Microsoft Docs"
description: "Explica como adicionar utilizadores ou alterar as informações de utilizador no Azure Active Directory, incluindo utilizadores externos e convidados."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 564a04ec-53c1-470b-9ab9-f3db57da0a89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 30dbd21c6d21aa55c2c7e84c315fba098674ddd8
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory"></a>Adicionar utilizadores de outros diretórios ou empresas associadas no Azure Active Directory

Este artigo explica como adicionar utilizadores de outros diretórios no Azure Active Directory ou adicionar utilizadores de empresas associadas. Para obter informações sobre a adição de novos utilizadores na organização e adicionar utilizadores com contas Microsoft, consulte [Adicionar novos utilizadores ao Azure Active Directory](active-directory-create-users.md). 

> [!IMPORTANT]
> A Microsoft recomenda que faça a gestão do Azure AD com o [centro de administração do Azure AD](https://aad.portal.azure.com) no portal do Azure em vez de utilizar o portal clássico do Azure referenciado neste artigo. Para saber como adicionar utilizadores convidados de colaboração B2B no centro de administração do Azure AD, veja [What is Azure AD B2B collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md) (O que é a colaboração B2B do Azure AD?).

Por predefinição, os utilizadores adicionados não têm permissões de administrador. No entanto, pode atribuir-lhes funções em qualquer altura.

## <a name="add-a-user"></a>Adicionar um utilizador
1. Iniciar sessão no [Portal Clássico do Azure](https://manage.windowsazure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **Active Directory** e abra o diretório.
3. Selecione o separador **Utilizadores** e selecione **Adicionar Utilizador** na barra de comandos.
4. Na página **Forneça mais informações sobre este utilizador**, em **Tipo de utilizador**, selecione:

   * **Utilizador noutro diretório do Azure AD** – adiciona uma conta de utilizador ao diretório com origem noutro diretório do Azure AD. Apenas pode selecionar um utilizador noutro diretório se também for membro desse diretório.
   * **Utilizadores de empresas associadas** – para convidar e autorizar utilizadores da empresa associada para o seu diretório (Consulte [Colaboração B2B do Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md)). Terá de [carregar um ficheiro CSV especificando endereços de e-mail](active-directory-b2b-references-csv-file-format.md).
5. Na página **Perfil** do utilizador, introduza o nome e o apelido, um nome amigável de utilizador e uma função de utilizador na lista **Funções**. Para obter mais informações sobre as funções de utilizador e administrador, consulte [Atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md). Especifique se pretende **Ativar o Multi-Factor Authentication** para o utilizador.
6. Na página **Obter palavra-passe temporária**, selecione **Criar**.

> [!IMPORTANT]
> Se a sua organização utilizar mais de um domínio, deverá ser informado dos seguintes problemas quando adicionar uma conta de utilizador:
>
> * PARA adicionar contas de utilizador com o mesmo nome de principal de utilizador (UPN) nos domínios, **primeiro** por adicione, por exemplo, geoffgrisso@contoso.onmicrosoft.com **seguido de**geoffgrisso@contoso.com.
> * **Não** adicione geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com.
>

Se alterar as informações de um utilizador cuja identidade está sincronizada com o serviço do Active Directory no local, não poderá alterar as informações do utilizador no Portal Clássico do Azure. Para alterar as informações do utilizador, utilize as ferramentas de gestão do Active Directory no local.

## <a name="add-external-users"></a>Adicionar utilizadores externos
Pode ainda adicionar utilizadores de outro diretório do Azure AD a que pertence ou de empresas associadas ao carregar um ficheiro CSV. Para adicionar um utilizador externo, em **Tipo de Utilizador**, especifique **Utilizador noutro diretório do Microsoft Azure AD** ou **Utilizadores em empresas associadas**.

Estes dois tipos de utilizadores têm origem noutro diretório e são adicionados como **utilizadores externos**. Os utilizadores externos podem colaborar com outros utilizadores num diretório sem qualquer requisito para adicionar novas contas e credenciais. Os utilizadores externos podem autenticar-se com o respetivo diretório raiz aquando do início de sessão, sendo que a autenticação funciona para quaisquer outros diretórios aos quais tiverem sido adicionados.

## <a name="external-user-management-and-limitations"></a>Gestão de utilizadores externos e limitações
Quando adiciona no seu diretório um utilizador de outro diretório, esse utilizador é um utilizador externo no seu diretório. O nome a apresentar e o nome de utilizador são copiados do diretório raiz deles e utilizados para o utilizador externo no seu diretório. De ora em diante, as propriedades da conta de utilizador externo são completamente independentes. Se forem realizadas alterações de propriedade ao utilizador no respetivo diretório raiz, essas alterações não serão propagadas à conta de utilizador externo no seu diretório.

A única ligação entre as duas contas é que o utilizador é sempre autenticado no respetivo diretório raiz ou com a respetiva conta Microsoft. Tal explica porque não é apresentada nenhuma opção para repor a palavra-passe ou ativar o Multi-Factor Authentication para um utilizador externo. Atualmente, a política de autenticação da conta Microsoft ou do diretório raiz é a única que é avaliada quando o utilizador inicia sessão.

> [!NOTE]
> Ainda pode desativar o utilizador externo no diretório, bloqueando assim o acesso ao diretório.
>
>

Se um utilizador for eliminado do respetivo diretório raiz ou se cancelar a respetiva conta Microsoft, o utilizador externo continuará a existir no seu diretório. Contudo, o utilizador no seu diretório não poderá aceder aos recursos, uma vez que não poderá autenticar-se com um diretório raiz ou conta Microsoft.

### <a name="services-that-currently-support-access-by-azure-ad-external-users"></a>Serviços que suportam atualmente o acesso pelos utilizadores externos do Azure AD
* **Portal Clássico do Azure**: permite a um utilizador externo que é administrador de vários diretórios gerir cada um desses diretórios.
* **SharePoint Online**: se a partilha externa estiver ativada, esta definição permite a um utilizador externo aceder aos recursos autorizados do SharePoint Online.
* **Dynamics CRM**: se o utilizador for licenciado através do PowerShell, esta definição permite a um utilizador externo aceder a recursos autorizados no Dynamics CRM.
* **Dynamics AX**: se o utilizador for licenciado através do PowerShell, esta definição permite a um utilizador externo aceder a recursos autorizados no Dynamics AX. As limitações dos [utilizadores externos do Azure AD](#known-limitations-of-azure-ad-external-users) também se aplicam aos utilizadores externos no Dynamics AX.

## <a name="next-steps"></a>Passos seguintes
* [Adicionar novos utilizadores ao Azure Active Directory](active-directory-create-users.md)
* [Administrar o Azure AD](active-directory-administer.md)
* [Gerir palavras-passe no Azure AD](active-directory-manage-passwords.md)
* [Gerir grupos no Azure AD](active-directory-manage-groups.md)

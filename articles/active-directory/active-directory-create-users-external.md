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
ms.date: 09/22/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 92b951ca273534a48648df5d8a7967d9b321c26f


---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory"></a>Adicionar utilizadores de outros diretórios ou empresas associadas no Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-users-create-external-azure-portal.md)
> * [Portal Clássico do Azure](active-directory-create-users-external.md)
> 
> 

Este artigo explica como adicionar utilizadores de outros diretórios no Azure Active Directory ou adicionar utilizadores de empresas associadas. Para obter informações sobre a adição de novos utilizadores na organização e adicionar utilizadores com contas Microsoft, consulte [Adicionar novos utilizadores ao Azure Active Directory](active-directory-create-users.md). Por predefinição, os utilizadores adicionados não têm permissões de administrador. No entanto, pode atribuir-lhes funções em qualquer altura.

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
> * Para adicionar contas de utilizador com o mesmo nome principal de utilizador (UPN) entre domínios, **comece** por adicionar, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **, seguido de ** geoffgrisso@contoso.com.
> * **Não** adicione geoffgrisso@contoso.com, antes de adicionar geoffgrisso@contoso.onmicrosoft.com. É importante seguir esta ordem uma vez que qualquer anulação poderá ser complicada.
> 
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

### <a name="known-limitations-of-azure-ad-external-users"></a>Limitações conhecidas dos utilizadores externos do Azure AD
* Os utilizadores externos que são administradores não podem adicionar utilizadores aos diretórios (colaboração B2B) a partir de empresas associadas, fora dos seus respetivos diretórios raiz
* Os utilizadores externos não podem autorizar aplicações multi-inquilino em diretórios, para além dos seus respetivos diretórios raiz
* Atualmente, o PowerBI não suporta o acesso por utilizadores externos
* O Portal do Office não suporta o licenciamento a utilizadores externos
* No que respeita ao Azure AD PowerShell, os utilizadores externos iniciam sessão no respetivo diretório raiz, sendo que não podem gerir diretórios nos quais forem utilizadores externos

## <a name="whats-next"></a>Passos seguintes
* [Adicionar novos utilizadores ao Azure Active Directory](active-directory-create-users.md)
* [Administrar o Azure AD](active-directory-administer.md)
* [Gerir palavras-passe no Azure AD](active-directory-manage-passwords.md)
* [Gerir grupos no Azure AD](active-directory-manage-groups.md)




<!--HONumber=Dec16_HO1-->



---
title: "Serviços de Domínio do Azure AD: Ativar a sincronização de palavras-passe | Microsoft Docs"
description: "Introdução aos Serviços de Domínio do Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: e4b1907b95576468654703c843a5f6e06846814b
ms.lasthandoff: 03/09/2017


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD
Nas tarefas anteriores, ativou os Serviços de Domínio do Azure AD Domain Services para o seu inquilino do Azure AD. A tarefa seguinte consiste em ativar hashes de credenciais necessários para a autenticação NTLM e Kerberos para sincronizar com os Serviços de Domínio do Azure AD. Quando a sincronização de credenciais estiver configurada, os utilizadores podem iniciar sessão no domínio gerido com as credenciais da empresa.

Os passos envolvidos diferem consoante a sua organização tenha um inquilino do Azure AD apenas na nuvem ou se estiver definido para sincronizar com o seu diretório no local com o Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Inquilino do Azure AD apenas na nuvem](active-directory-ds-getting-started-password-sync.md)
> * [Inquilino do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tarefa 5: Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um inquilino do Azure AD apenas na nuvem
Os Serviços de Domínio do Azure AD precisam de hashes de credenciais num formato adequado para autenticação NTLM e Kerberos, para autenticar os utilizadores no domínio gerido. A menos que ative os Serviços de Domínio do AAD Domain Services para o seu inquilino, o Azure AD não gera nem armazena hashes de credenciais no formato necessário para a autenticação NTLM ou Kerberos. Por motivos óbvios de segurança, o Azure AD também não armazena quaisquer credenciais no formato de texto não encriptado. Por isso, o Azure AD não tem uma forma de gerar estes hashes de credenciais NTLM ou Kerberos, com base nas credenciais existentes dos utilizadores.

> [!NOTE]
> Se a sua organização tiver um inquilino do Azure AD apenas na nuvem, os utilizadores que necessitam de utilizar os Serviços de Domínio do Azure AD têm de alterar as respetivas palavras-passe.
>
>

Este processo de alteração de palavras-passe faz com que os hashes de credencial necessários pelos Serviços de Domínio do Azure AD para a autenticação Kerberos e NTLM sejam gerados no Azure AD. Pode optar por expirar palavras-passe para todos os utilizadores no inquilino que necessitam de utilizar os Serviços de Domínio do Azure AD ou instruir esses utilizadores para alterarem as palavras-passe.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Ativar a geração de hashes de credenciais NTLM e Kerberos para um inquilino do Azure AD apenas na nuvem
Seguem-se as instruções que tem de fornecer aos utilizadores finais, para que possam alterar as respetivas palavras-passe:

1. Navegue até à página do Painel de Acesso do Azure AD para a sua organização, em [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Selecione o separador **perfil** nesta página.
3. Clique no mosaico **Alterar palavra-passe** nesta página.

    ![Crie uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Se não vir a opção **Alterar palavra-passe** na página do Painel de Acesso, certifique-se de que a sua organização configurou a [gestão de palavras-passe no Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. Na página **Alterar palavra-passe**, escreva a palavra-passe existente (antiga) e, em seguida, escreva uma palavra-passe nova e confirme-a. Clique em **Submeter**.

    ![Crie uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Depois de ter alterado a palavra-passe, a nova palavra-passe será utilizada nos Serviços de Domínio do Azure AD dentro de pouco tempo. Após alguns minutos (normalmente, cerca de 20 minutos), pode iniciar sessão em computadores associados ao domínio gerido com a palavra-passe recentemente alterada.

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Como atualizar a sua própria palavra-passe](../active-directory/active-directory-passwords-update-your-own-password.md#how-to-reset-your-password)
* [Introdução à Gestão de Palavras-passe no Azure AD](../active-directory/active-directory-passwords-getting-started.md).
* [Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um inquilino do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Associar uma máquina virtual do Windows a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Associar uma máquina virtual do Red Hat Enterprise Linux a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)


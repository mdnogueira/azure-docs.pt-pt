---
title: "Azure Active Directory Domain Services: Ativar a sincronização de palavras-passe | Microsoft Docs"
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
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6ed35ca1b83f5f7221824d99817800c1e42e68c1
ms.lasthandoff: 04/12/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Ativar a sincronização de palavras-passe com o Azure Active Directory Domain Services
Nas tarefas anteriores, ativou o Azure Active Directory Domain Services (AD DS) do seu inquilino do Azure Active Directory (Azure AD). A tarefa seguinte consiste em ativar hashes de credenciais, que são necessários para a autenticação LAN Manager do Windows NT (NTLM) e Kerberos para sincronizar com o Azure Active Directory Domain Services. Assim que a sincronização de credenciais estiver configurada, os utilizadores podem iniciar sessão no domínio gerido com as credenciais da empresa.

Os procedimentos diferem consoante a sua organização tenha um inquilino do Azure AD apenas na cloud ou se estiver definido para sincronizar com o seu diretório no local com o Azure AD Connect.

> [!div class="op_single_selector"]
> * [Inquilino do Azure AD apenas na nuvem](active-directory-ds-getting-started-password-sync.md)
> * [Inquilino do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tarefa 5: Ativar a sincronização de palavras-passe para o Azure Active Directory Domain Services para um inquilino do Azure AD apenas na cloud
Para autenticar os utilizadores no domínio gerido, o Azure Active Directory Domain Services precisa de hashes de credenciais num formato adequado para autenticação NTLM e Kerberos. A menos que ative o Azure Active Directory Domain Services para o seu inquilino, o Azure AD não gera nem armazena hashes de credenciais no formato necessário para a autenticação NTLM ou Kerberos. Por motivos óbvios de segurança, o Azure AD também não armazena quaisquer credenciais no formato de texto não encriptado. Por isso, o Azure AD não tem uma forma de gerar estes hashes de credenciais NTLM ou Kerberos, com base nas credenciais existentes dos utilizadores.

> [!NOTE]
> Se a sua organização tiver um inquilino do Azure AD apenas na cloud, os utilizadores que necessitam de utilizar o Azure Active Directory Domain Services tem de alterar as respetivas palavras-passe.
>
>

Este processo de alteração de palavras-passe faz com que os hashes de credencial necessários pelo Azure Active Directory Domain Services para a autenticação Kerberos e NTLM sejam gerados no Azure AD. Pode optar por expirar as palavras-passe para todos os utilizadores no inquilino que necessitam de utilizar o Azure Active Directory Domain Services ou instruí-los para alterarem as palavras-passe.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Ativar a geração de hashes de credenciais NTLM e Kerberos para um inquilino do Azure AD apenas na nuvem
Seguem-se as instruções que tem de fornecer aos utilizadores, para que possam alterar as respetivas palavras-passe:

1. Aceda à página do [Painel de Acesso do Azure AD](http://myapps.microsoft.com) para a sua organização.
2. Na janela do Painel de Acesso, selecione o separador **perfil**.
3. Clique no mosaico **Alterar palavra-passe**.

    ![O mosaico "Alterar palavra-passe" do Painel de Acesso do Azure AD](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Se a opção **Alterar palavra-passe** não estiver apresentada na página do Painel de Acesso, certifique-se de que a sua organização configurou a [gestão de palavras-passe no Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. Na página **Alterar palavra-passe**, escreva a palavra-passe existente (antiga), escreva uma palavra-passe nova e, em seguida, confirme-a. 

    ![Crie uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Clique em **Submeter**.

Uns minutos depois de ter alterado a palavra-passe, a nova palavra-passe pode ser utilizada no Azure Active Directory Domain Services. Após mais alguns minutos (normalmente, cerca de 20 minutos), pode iniciar sessão em computadores que estão associados ao domínio gerido com a palavra-passe recentemente alterada.

## <a name="next-steps"></a>Passos seguintes
* [Como atualizar a sua própria palavra-passe](../active-directory/active-directory-passwords-update-your-own-password.md#reset-your-password)
* [Introdução à Gestão de Palavras-passe no Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Ativar a sincronização de palavras-passe para o Azure Active Directory Domain Services para um inquilino do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrar um domínio gerido pelo Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Associar uma máquina virtual do Windows a um domínio gerido do Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Associar uma máquina virtual do Red Hat Enterprise Linux a um domínio gerido do Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)


<properties
    pageTitle="Serviços de Domínio do Azure AD: Ativar a sincronização de palavras-passe | Microsoft Azure"
    description="Introdução aos Serviços de Domínio do Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/06/2016"
    ms.author="maheshu"/>

# Serviços de Domínio do Azure AD *(Pré-visualização)* – Ativar a sincronização de palavras-passe para os Serviços de Domínio do Azure AD

## Tarefa 5: Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um diretório do Azure AD apenas na nuvem
Assim que tiver ativado os Serviços de Domínio do Azure AD para o inquilino do Azure AD, a tarefa seguinte consiste em ativar as credenciais para sincronizar com os Serviços de Domínio do Azure AD. Isto permite aos utilizadores iniciar sessão no domínio gerido utilizando as respetivas credenciais empresariais.

Os passos envolvidos são diferentes, com base se a sua organização tem um diretório do Azure AD apenas na nuvem ou se está definida para sincronizar com o seu diretório no local com o Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Diretório do Azure AD apenas na nuvem](active-directory-ds-getting-started-password-sync.md)
- [Diretório do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Ativar geração de hashes de credencial NTLM e de Kerberos para um diretório do Azure AD apenas na nuvem
Se a sua organização tiver um diretório do Azure AD apenas na nuvem, os utilizadores que necessitam de utilizar os Serviços de Domínio do Azure AD terão de alterar as palavras-passe. Este processo de alteração de palavras-passe faz com que os hashes de credencial necessários pelos Serviços de Domínio do Azure AD para a autenticação Kerberos e NTLM sejam gerados no Azure AD. Pode optar por expirar palavras-passe para todos os utilizadores no inquilino que necessitam de utilizar os Serviços de Domínio do Azure AD ou instruir esses utilizadores para alterarem as palavras-passe.

Seguem-se as instruções que tem de fornecer aos utilizadores finais, para alterarem as respetivas palavras-passe:

1. Navegue para a página do Painel de Acesso do Azure AD para a sua organização. Este está normalmente disponível em [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Selecione o separador **perfil** nesta página.

3. Clique no mosaico **Alterar palavra-passe** nesta página para iniciar uma alteração de palavra-passe.

    ![Crie uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Ao fazê-lo, é apresentada a página **alterar palavra-passe**. Os utilizadores podem introduzir a palavra-passe (antiga) existente e prosseguirem para alterar palavra-passe.

    ![Crie uma rede virtual para os Serviços de Domínio do Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Depois de os utilizadores terem alterado a palavra-passe, a nova palavra-passe pode ser utilizada nos Serviços de Domínio do Azure AD dentro de pouco tempo. Após alguns minutos, os utilizadores podem iniciar sessão em computadores associados ao domínio gerido utilizando a palavra-passe recentemente alterada.


<br>

## Conteúdo relacionado

- [Ativar a sincronização de palavras-passe para os Serviços de Domínio do AAD para um diretório do Azure AD sincronizado](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Administrar um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Associar uma máquina virtual do Windows a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Associar uma máquina virtual do Red Hat Enterprise Linux a um domínio gerido dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)



<!--HONumber=Aug16_HO1-->



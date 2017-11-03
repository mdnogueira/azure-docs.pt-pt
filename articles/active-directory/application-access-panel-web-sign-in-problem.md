---
title: "Problema de início de sessão para o site do painel de acesso | Microsoft Docs"
description: "Orientações para resolução de problemas que poderá encontrar ao tentar iniciar sessão e utilizar o painel de acesso"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviwer: japere
ms.openlocfilehash: 28d91237adf745e591b02322de7881c8122827ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problema de início de sessão para o site do painel de acesso

O painel de acesso é um portal baseado na web que permite que um utilizador que tenha uma conta escolar ou profissional no Azure Active Directory (Azure AD) para ver e iniciar baseado na nuvem as aplicações que o administrador do Azure AD concedeu-lhes acesso. Um utilizador que possua as edições do Azure AD também pode utilizar grupos self-service e capacidades de gestão de aplicações através do painel de acesso. O painel de acesso separado do portal do Azure e não exige que os utilizadores tenham uma subscrição do Azure.

Os utilizadores podem iniciar sessão painel de acesso se tiverem uma conta escolar ou profissional no Azure AD.

-   Os utilizadores podem ser autenticados pelo Azure AD diretamente.

-   Os utilizadores podem ser autenticados utilizando os serviços de Federação do Active Directory (AD FS).

-   Os utilizadores podem ser autenticados pelo Windows Server Active Directory.

Se um utilizador tem uma subscrição do Azure ou do Office 365 e foi utilizando o portal do Azure ou uma aplicação do Office 365, deverá conseguir utilizar o painel de acesso totalmente integrada sem ser necessário voltar a iniciar sessão. Os utilizadores que não são autenticados pedido que inicie sessão com o nome de utilizador e palavra-passe da sua conta no Azure AD. Se a organização tiver configurado a Federação, escrever o nome de utilizador é suficiente.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro 

-   Certifique-se o utilizador iniciar sessão para o **corrigir URL**: <https://myapps.microsoft.com>

-   Certifique-se de que o browser do utilizador foi adicionado o URL para o **fidedigna sites**

-   Certifique-se de que a conta de utilizador **ativada** para inícios de sessão.

-   Certifique-se de que a conta de utilizador **não bloqueadas.**

-   Certifique-se de que o utilizador **palavra-passe não está expirado ou esquecimento.**

-   Certifique-se **multi-factor Authentication** não está a bloquear o acesso do utilizador.

-   Certifique-se de um **política de acesso condicional** ou **Identity Protection** política não está a bloquear o acesso do utilizador.

-   Certifique-se de que um utilizador **informações de contacto de autenticação** é atualizado para permitir que as políticas de autenticação Multifator ou o acesso condicional a serem impostas.

-   Certifique-se para também tente limpar os cookies do browser e tentar iniciar sessão novamente.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Que cumprem os requisitos de browser para o painel de acesso

O painel de acesso necessita de um browser que suporte JavaScript e ativou CSS. Para utilizar com base em palavra-passe-início de sessão único (SSO) no painel de acesso, a extensão do painel de acesso tem de estar instalada no browser do utilizador. Esta extensão é transferida automaticamente quando um utilizador seleciona uma aplicação que está configurada para SSO baseada em palavra-passe.

Para SSO baseada em palavra-passe, os browsers do utilizador final podem ser:

-   Internet Explorer 8, 9, 10, 11 - no Windows 7 ou posterior

-   Limite de aniversário da edição do Windows 10 ou posterior 

-   Chrome – No Windows 7 ou posterior e no MacOS X ou posterior

-   Firefox 26.0 ou posterior – no Windows XP SP2 ou posterior e no Mac OS X 10.6 ou posterior


## <a name="problems-with-the-users-account"></a>Problemas com a conta de utilizador

O acesso ao painel de acesso pode ser bloqueado devido a um problema com a conta de utilizador. Seguem-se algumas formas pode resolver problemas e resolver problemas com utilizadores e as respetivas definições de conta:

-   [Verifique se existe uma conta de utilizador no Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Verifique o estado da conta do utilizador](#check-a-users-account-status)

-   [Repor palavra-passe de um utilizador](#reset-a-users-password)

-   [Ativar a reposição de palavras-passe self-service](#enable-self-service-password-reset)

-   [Verificar o estado de autenticação multifator do utilizador](#check-a-users-multi-factor-authentication-status)

-   [Verifique as informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

-   [Verifique as associações de grupo do utilizador](#check-a-users-group-memberships)

-   [Verifique as licenças atribuídas de um utilizador](#check-a-users-assigned-licenses)

-   [Atribuir uma licença de um utilizador](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Verifique se existe uma conta de utilizador no Azure Active Directory

Para verificar a conta de utilizador estiver presente, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Assinale as propriedades do objeto de utilizador para assegurar que procuram conforme esperado e não existem dados estão em falta.

### <a name="check-a-users-account-status"></a>Verifique o estado da conta do utilizador

Para verificar o estado da conta do utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **perfil**.

8.  Em **definições** Certifique-se de que **bloco sessão** está definido como **não**.

### <a name="reset-a-users-password"></a>Repor palavra-passe de um utilizador

Para repor a palavra-passe de um utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em de **Repor palavra-passe** botão na parte superior do painel de utilizador.

8.  Clique em de **Repor palavra-passe** botão no **Repor palavra-passe** painel que aparece.

9.  Copiar o **palavra-passe temporária** ou **introduza uma palavra-passe nova** para o utilizador.

10. Comunicar esta nova palavra-passe ao utilizador, necessário para alterar esta palavra-passe durante o próximo início de sessão no Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Ativar a reposição de palavra-passe self-service

Para ativar a reposição de palavra-passe self-service, siga os passos de implementação abaixo:

-   [Permitir que os utilizadores reponham as respetivas palavras-passe do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Permitir que os utilizadores reponham ou alterem as palavras-passe do Active Directory no local](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Verificar o estado de autenticação multifator do utilizador

Para verificar o estado de autenticação multifator de um utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  Clique em de **multi-factor Authentication** botão na parte superior do painel.

7.  Uma vez a **Portal de administração do multi-factor Authentication** cargas, certifique-se no **utilizadores** separador.

8.  Localize o utilizador na lista de utilizadores por pesquisar, filtrar ou ordenar.

9.  Selecione o utilizador na lista de utilizadores e **ativar**, **desativar**, ou **impor** autenticação multifator conforme pretendido.

   >[!NOTE]
   >Se um utilizador estiver num **imposto** Estado, pode defini-los como **desativado** temporariamente a informá-los novamente para a respetiva conta. Depois de estarem no, em seguida, pode alterar o respetivo estado para **ativado** novamente para exigi-las para voltar a registar as suas informações de contactos durante o início de sessão seguinte. Em alternativa, pode seguir os passos a [verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir estes dados para os mesmos.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Verifique as informações de contacto de autenticação de um utilizador

Para verificar a informação de contacto de autenticação de um utilizador utilizada para autenticação multifator, acesso condicional, proteção de identidade e de reposição de palavra-passe, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **perfil**.

8.  Desloque para baixo até **informações de contacto de autenticação**.

9.  **Reveja** os dados registados para o utilizador e a atualização, conforme necessário.

### <a name="check-a-users-group-memberships"></a>Verifique as associações de grupo do utilizador

Para verificar as associações de grupo do utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **grupos** ver grupos que o utilizador é membro.

### <a name="check-a-users-assigned-licenses"></a>Verifique as licenças atribuídas de um utilizador

Para verificar as licenças atribuídas de um utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o utilizador tem atribuída.

### <a name="assign-a-user-a-license"></a>Atribuir uma licença de um utilizador 

Para atribuir uma licença a um utilizador, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os utilizadores**.

6.  **Pesquisa** para o utilizador que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o utilizador tem atribuída.

8.  Clique em de **atribuir** botão.

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique o **opções de atribuição** item granularly atribuir produtos. Clique em **Ok** quando este está concluída.

11. Clique em de **atribuir** botão para atribuir estes licenças para este utilizador.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem o problema

Abra um pedido de suporte com as seguintes informações se disponíveis:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   ID do inquilino

-   Tipo de browser

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios de fiddler

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)

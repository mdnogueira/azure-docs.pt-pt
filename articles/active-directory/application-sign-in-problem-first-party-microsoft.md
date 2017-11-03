---
title: "Problemas em iniciar sessão para uma aplicação da Microsoft | Microsoft Docs"
description: "Resolver problemas comuns de deparam quando iniciar sessão no originais Applications Microsoft utilizar o Azure AD (como o Office 365)"
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
ms.openlocfilehash: 5638434270ee82d2b9737ea8eed8b5a8c62f7121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>Problemas em iniciar sessão para uma aplicação da Microsoft

Applications da Microsoft (como o Office 365 Exchange, SharePoint, Yammer, etc.) são atribuídos e geridos um pouco diferente 3rd aplicações de SaaS de terceiros ou outras aplicações que integrar com o Azure AD para início de sessão único em.

Existem três formas principais que um utilizador pode aceder a uma aplicação publicada de Microsoft.

-   Para aplicações do Office 365 ou outros conjuntos de pagos, é concedido acesso através de utilizadores **atribuição de licenças** diretamente à conta de utilizador ou através de um grupo com o nosso capacidade de atribuição com base no grupo de licenças.

-   Para aplicações da Microsoft ou de terceiros um terceiro publica livremente para qualquer pessoa a utilizar, podem ser concedido acesso aos utilizadores através de **consentimento do utilizador**. This0 significa que iniciar sessão para a aplicação com a conta do Azure AD ou escola e permitir que tem acesso a algumas conjunto limitado de dados na respetiva conta.

-   Para aplicações que a Microsoft ou uma 3rd terceiros publica livremente para qualquer pessoa a utilizar, podem também ser concedido acesso aos utilizadores através de **consentimento administrador**. Isto significa que um administrador determinou que a aplicação pode ser utilizada por todas as pessoas na organização, para que inicie sessão na aplicação com uma conta de Administrador Global e concedem acesso a todas as pessoas na organização.

Para resolver o problema, começar a utilizar o [geral problema áreas com acesso à aplicação a ter em consideração](#general-problem-areas-with-application-access-to-consider) e, em seguida, leia o [explicação passo a passo: passos para resolver problemas de acesso do Microsoft Application](#walkthrough-steps-to-troubleshoot-microsoft-application-access) para obter os detalhes.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Áreas de problema geral com acesso à aplicação a considerar

Segue-se uma lista de áreas de problema geral que pode explorar se tiver uma ideia dos onde começar, mas recomendamos que leia as instruções para começar rapidamente: [explicação passo a passo: passos para resolver problemas de acesso do Microsoft Application](#walkthrough-steps-to-troubleshoot-microsoft-application-access).

-   [Problemas com a conta de utilizador](#problems-with-the-users-account)

-   [Problemas com grupos](#problems-with-groups)

-   [Problemas com as políticas de acesso condicional](#problems-with-conditional-access-policies)

-   [Problemas de consentimento de aplicação](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Passos para resolver problemas de acesso do Microsoft Application

Abaixo são alguns utilizadores problemas comuns depare quando os utilizadores não podem iniciar sessão para uma aplicação da Microsoft.

-   Problemas gerais para verificar primeiro

  * Certifique-se o utilizador iniciar sessão para o **corrigir URL** e não é um URL de aplicação local.

  * Certifique-se de que a conta de utilizador **não bloqueadas.**

  * Certifique-se de que o **conta de utilizador existe** no Azure Active Directory. [Verifique se existe uma conta de utilizador no Azure Active Directory](#problems-with-the-users-account)

  * Certifique-se de que a conta de utilizador **ativada** para inícios de sessão. [Verifique o estado da conta do utilizador](#problems-with-the-users-account)

  * Certifique-se de que o utilizador **palavra-passe não está expirado ou esquecimento.** [Repor palavra-passe de um utilizador](#reset-a-users-password) ou [ativar a reposição de palavra-passe self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * Certifique-se **multi-factor Authentication** não está a bloquear o acesso do utilizador. [Verifique o estado de autenticação multifator de um utilizador](#check-a-users-multi-factor-authentication-status) ou [verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

   * Certifique-se de um **política de acesso condicional** ou **Identity Protection** política não está a bloquear o acesso do utilizador. [Verificar uma política de acesso condicional específico](#problems-with-conditional-access-policies) ou [Verifique a política de acesso condicional de uma aplicação específica](#check-a-specific-applications-conditional-access-policy) ou [desativar uma política de específica de acesso condicional](#disable-a-specific-conditional-access-policy)

   * Certifique-se de que um utilizador **informações de contacto de autenticação** é atualizado para permitir que as políticas de autenticação Multifator ou o acesso condicional a serem impostas. [Verifique o estado de autenticação multifator de um utilizador](#check-a-users-multi-factor-authentication-status) ou [verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info)

-   Para **Microsoft** **aplicações que requerem uma licença** (como o Office 365), aqui estão alguns problemas específicos para verificar depois de ter ruled de problemas gerais acima:

   * Certifique-se o utilizador ou tem um **licença atribuída.** [Verifique as licenças atribuídas de um utilizador](#check-a-users-assigned-licenses) ou [verifique licenças atribuídas de um grupo](#check-a-groups-assigned-licenses)

   * Se a licença for **atribuídos a um** **grupo estático**, certifique-se de que o **utilizador é membro** desse grupo. [Verifique as associações de grupo do utilizador](#check-a-users-group-memberships)

   * Se a licença for **atribuídos a um** **grupo dinâmico**, certifique-se de que o **regra de filiação dinâmica está definida corretamente**. [Verificar os critérios de associação de um grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

   * Se a licença for **atribuídos a um** **grupo dinâmico**, certifique-se de que o grupo dinâmico tem **terminou o processamento** e que a respetiva associação a **utilizador é membro** (esta operação pode demorar algum tempo). [Verifique as associações de grupo do utilizador](#check-a-users-group-memberships)

   *  Uma vez, certifique-se de que a licença é atribuída, certifique-se a licença **não expirado**.

   *  Certifique-se a licença **para a aplicação** estão a aceder.

-   Para **Microsoft** **aplicações que não requerem uma licença**, seguem-se alguns outros aspetos a verificar:

   * Se a aplicação está a solicitar **permissões ao nível do utilizador** (por exemplo "aceder à caixa de correio do utilizador"), certifique-se de que o utilizador tem sessão iniciada para a aplicação e efetuou uma **operação de nível de utilizador consentimento** para permitir que a aplicação de aceder aos seus dados.

   * Se a aplicação está a solicitar **permissões ao nível do administrador** (por exemplo "acesso caixas de correio de todos os utilizadores"), certifique-se de que um Administrador Global efetuou uma **operação de consentimento de nível de administrador em nome de todos os utilizadores** na organização.

## <a name="problems-with-the-users-account"></a>Problemas com a conta de utilizador

Acesso de aplicação pode ser bloqueado devido a um problema com um utilizador que está atribuído à aplicação. Seguem-se algumas formas pode resolver problemas e resolver problemas com utilizadores e as respetivas definições de conta:

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

  * **Tenha em atenção**: se um utilizador estiver num **imposto** Estado, pode defini-los como **desativado** temporariamente a informá-los novamente para a respetiva conta. Depois de estarem no, em seguida, pode alterar o respetivo estado para **ativado** novamente para exigi-las para voltar a registar as suas informações de contactos durante o início de sessão seguinte. Em alternativa, pode seguir os passos a [verificar informações de contacto de autenticação de um utilizador](#check-a-users-authentication-contact-info) para verificar ou definir estes dados para os mesmos.

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

## <a name="problems-with-groups"></a>Problemas com grupos

Acesso de aplicação pode ser bloqueado devido a um problema com um grupo que está atribuído à aplicação. Seguem-se algumas formas pode resolver problemas e resolver problemas com grupos e associações a grupos:

-   [Verifique a associação a um grupo](#check-a-groups-membership)

-   [Verificar os critérios de associação de um grupo dinâmico](#check-a-dynamic-groups-membership-criteria)

-   [Verifique as licenças atribuídas de um grupo](#check-a-groups-assigned-licenses)

-   [Reprocessar licenças de um grupo](#reprocess-a-groups-licenses)

-   [Atribuir uma licença de um grupo](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Verifique a associação a um grupo

Para verificar a associação a um grupo, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo que está interessado e **clique na linha** para selecionar.

7.  Clique em **membros** para rever a lista de utilizadores atribuídos a este grupo.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Verificar os critérios de associação de um grupo dinâmico 

Para verificar os critérios de associação de um grupo dinâmico, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo que está interessado e **clique na linha** para selecionar.

7.  Clique em **regras de associação dinâmica.**

8.  Reveja o **simples** ou **avançadas** definida para este grupo de regras e certifique-se de que o utilizador pretende que seja um membro deste grupo cumpre estes critérios.

### <a name="check-a-groups-assigned-licenses"></a>Verifique as licenças atribuídas de um grupo

Para verificar as licenças atribuídas de um grupo, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o grupo foi atribuído.

### <a name="reprocess-a-groups-licenses"></a>Reprocessar licenças de um grupo

Para reprocessar licenças atribuídas de um grupo, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o grupo foi atribuído.

8.  Clique em de **Reprocessar** botão para se certificar de que as licenças atribuídas aos membros deste grupo estão atualizadas. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

   >[!NOTE]
   >Para fazê-lo mais rapidamente, considere temporariamente diretamente, atribuir uma licença ao utilizador. [Atribuir uma licença de um utilizador](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Atribuir uma licença de um grupo

Para atribuir uma licença a um grupo, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **utilizadores e grupos** no menu de navegação.

5.  Clique em **todos os grupos**.

6.  **Pesquisa** para o grupo que está interessado e **clique na linha** para selecionar.

7.  Clique em **licenças** ver qual licenças atualmente o grupo foi atribuído.

8.  Clique em de **atribuir** botão.

9.  Selecione **um ou mais produtos** da lista de produtos disponíveis.

10. **Opcional** clique o **opções de atribuição** item granularly atribuir produtos. Clique em **Ok** quando este está concluída.

11. Clique em de **atribuir** botão para atribuir estes licenças para este grupo. Isto pode demorar muito tempo, dependendo do tamanho e complexidade do grupo.

   >[!NOTE]
   >Para fazê-lo mais rapidamente, considere temporariamente diretamente, atribuir uma licença ao utilizador. [Atribuir uma licença de um utilizador](#problems-with-application-consent).
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>Problemas com as políticas de acesso condicional

### <a name="check-a-specific-conditional-access-policy"></a>Verificar uma política de específica de acesso condicional

Para verificar ou validar uma política de acesso condicional único:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação.

5.  Clique em de **acesso condicional** item de navegação.

6.  Clique na política que está interessado inspecionar.

7.  Revisão que existem não existem condições específicas, atribuições ou outras definições que possam estar a bloquear o acesso do utilizador.

   >[!NOTE]
   >Pode pretender desativar temporariamente esta política para se certificar de que não está a afetar inícios de sessão. Para tal, defina o **ativar política de** alternar para **não** e clique em de **guardar** botão.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Verifique a política de acesso condicional de uma aplicação específica

Para verificar ou validar uma única aplicação atualmente configuradas na política de acesso condicional:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação.

5.  Clique em **todas as aplicações**.

6.  Procure a aplicação que está interessado ou o utilizador está a tentar iniciar sessão por nome de apresentação da aplicação ou o id da aplicação.

     >[!NOTE]
     >Se a aplicação que procura não for apresentada, clique em de **filtro** botão e expande o âmbito da lista para **todas as aplicações**. Se pretender ver mais colunas, clique em de **colunas** botão Adicionar detalhes adicionais para as suas aplicações.
     >
     >

7.  Clique em de **acesso condicional** item de navegação.

8.  Clique na política que está interessado inspecionar.

9.  Revisão que existem não existem condições específicas, atribuições ou outras definições que possam estar a bloquear o acesso do utilizador.

     >[!NOTE]
     >Pode pretender desativar temporariamente esta política para se certificar de que não está a afetar inícios de sessão. Para tal, defina o **ativar política de** alternar para **não** e clique em de **guardar** botão.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Desativar uma política de específica de acesso condicional

Para verificar ou validar uma política de acesso condicional único:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação.

5.  Clique em de **acesso condicional** item de navegação.

6.  Clique na política que está interessado inspecionar.

7.  Desativar a política ao definir o **ativar política de** alternar para **não** e clique em de **guardar** botão.

## <a name="problems-with-application-consent"></a>Problemas de consentimento de aplicação

Acesso de aplicação pode ser bloqueado porque não ocorreu a operação de consentimento de permissões adequadas. Seguem-se algumas formas pode resolver problemas e resolver problemas de consentimento de aplicação:

-   [Efetuar uma operação de consentimento de nível de utilizador](#perform-a-user-level-consent-operation)

-   [Efetuar a operação de consentimento de nível de administrador para qualquer aplicação](#perform-administrator-level-consent-operation-for-any-application)

-   [Executar ao nível do administrador consentimento para uma aplicação de inquilino único](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Executar ao nível do administrador consentimento para uma aplicação multi-inquilino](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Efetuar uma operação de consentimento de nível de utilizador

-   Para qualquer aplicação com capacidade para abrir ID Connect que os pedidos de permissões, ao navegar para de início de sessão a aplicação ecrã executa um consentimento ao nível do utilizador para a aplicação para o utilizador com sessão iniciada.

-   Se pretender fazê-lo através de programação, consulte [pedir consentimento do utilizador individual](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Efetuar a operação de consentimento de nível de administrador para qualquer aplicação

-   Para **apenas as aplicações desenvolvidas com o modelo de aplicação V1**, pode forçar este consentimento ao nível do administrador para ocorrer adicionando "**? perguntar = admin\_consentimento**" até ao fim da sessão de uma aplicação no URL.

-   Para **qualquer aplicação desenvolvida utilizando o modelo de aplicação V2**, pode impor esta consentimento ao nível do administrador para ocorrer ao seguir as instruções no **pedir as permissões de um administrador da directory** secção [utilizando o ponto final de consentimento de admin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Executar ao nível do administrador consentimento para uma aplicação de inquilino único

-   Para **único inquilino aplicações** que pedir permissões (como as que estiver a desenvolver ou proprietário na sua organização), pode efetuar um **consentimento de nível administrativo** operação em nome de todos os utilizadores, iniciar sessão como um Administrador Global e clicando no **conceder permissões** na parte superior da parte a **registo da aplicação -&gt; todas as aplicações -&gt; seleciona uma aplicação -&gt; permissões obrigatórias** painel.

-   Para **qualquer aplicação desenvolvida utilizando o modelo de aplicação V1 ou V2**, pode impor esta consentimento ao nível do administrador para ocorrer ao seguir as instruções no **pedir as permissões de um administrador da directory** secção [utilizando o ponto final de consentimento de admin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Executar ao nível do administrador consentimento para uma aplicação multi-inquilino

-   Para **aplicações multi-inquilino** que permissões de pedido (como uma aplicação de terceiros ou desenvolvidas pela organização do Microsoft,), pode realizar uma **consentimento de nível administrativo** operação. Inicie sessão como um Administrador Global e clicar no **conceder permissões** botão sob o **aplicações da empresa -&gt; todas as aplicações -&gt; seleciona uma aplicação -&gt; permissões** painel (disponível em breve).

-   Também pode impor esta consentimento ao nível do administrador para ocorrer ao seguir as instruções no **pedir as permissões de um administrador da directory** secção [utilizando o ponto final de consentimento de admin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Passos seguintes
[Utilizar o ponto final de consentimento de administração](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)


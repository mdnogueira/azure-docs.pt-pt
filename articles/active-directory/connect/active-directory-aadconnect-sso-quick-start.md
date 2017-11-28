---
title: "O Azure AD Connect: Totalmente integrada Single Sign-On - introdução | Microsoft Docs"
description: "Este artigo descreve como começar com Azure Active Directory totalmente integrada Single Sign-On"
services: active-directory
keywords: "o que é o Azure AD Connect, a instalação do Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: b85afe54832319fae2ea3a2501ec268bc63fc7c1
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory totalmente integrada Single Sign-On: início rápido

## <a name="deploy-seamless-single-sign-on"></a>Implementar totalmente integrada Single Sign-On

Azure Active Directory (Azure AD) totalmente integrada Single Sign-On (SSO totalmente integrado) automaticamente inicia sessão dos utilizadores quando estão nos respetivos ambientes de trabalho empresariais que estejam ligados à rede da sua empresa. SSO totalmente integrado fornece aos utilizadores com o facilitar o acesso às suas aplicações baseado na nuvem sem necessidade de quaisquer componentes adicionais no local.

Para implementar o SSO totalmente integrada, siga estes passos.

## <a name="step-1-check-the-prerequisites"></a>Passo 1: Verificar os pré-requisitos

Certifique-se de que os seguintes pré-requisitos estão assegurados:

* **Configurar o servidor do Azure AD Connect**: Se utilizar [autenticação pass-through](active-directory-aadconnect-pass-through-authentication.md) como método de início de sessão, não é necessária nenhuma verificação de pré-requisitos adicional. Se utilizar [sincronização de hash de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) como método de início de sessão e se existir uma firewall entre o Azure AD Connect e o Azure AD, certifique-se de que:
   - Utilizar a versão 1.1.644.0 ou posterior do Azure AD Connect. 
   - Se a sua firewall ou proxy permite a listas brancas DNS, lista branca as ligações para o  **\*. msappproxy.net** URLs através da porta 443. Se não, permitir o acesso à [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizada semanalmente. Este pré-requisito é aplicável apenas quando ativar a funcionalidade. Não é necessária para inícios de sessão de utilizador reais.

    >[!NOTE]
    >Azure AD Connect as versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização de hash de palavra-passe. Se lhe _não_ tencionar utilizar a sincronização de hash de palavra-passe em conjunto com a autenticação pass-through, leia o [notas de versão do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) para obter mais informações.

* **Configurar as credenciais de administrador de domínio**: tem de ter credenciais de administrador de domínio para cada do Active Directory de floresta que:
    * Sincronizar com o Azure AD através do Azure AD Connect.
    * Contém utilizadores que pretende ativar para SSO totalmente integrada.

## <a name="step-2-enable-the-feature"></a>Passo 2: Ativar a funcionalidade

Ativar a SSO totalmente integrada através de [do Azure AD Connect](active-directory-aadconnect.md).

Se estiver a efetuar uma instalação de raiz do Azure AD Connect, escolha o [caminho de instalação personalizada](active-directory-aadconnect-get-started-custom.md). Na **sessão do utilizador** página, selecione o **ativar o início de sessão único em** opção.

![O Azure AD Connect: Utilizador iniciar sessão](./media/active-directory-aadconnect-sso/sso8.png)

Se já tiver uma instalação do Azure AD Connect, selecione o **alterar utilizador início de sessão** página no Azure AD Connect e, em seguida, selecione **seguinte**.

![O Azure AD Connect: Alterar o utilizador inicie sessão](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Continuar com o assistente até chegar à **ativar o início de sessão único em** página. Forneça as credenciais de administrador de domínio para cada do Active Directory de floresta que:
    * Sincronizar com o Azure AD através do Azure AD Connect.
    * Contém utilizadores que pretende ativar para SSO totalmente integrada.

Após a conclusão do assistente, SSO totalmente integrada está ativada no seu inquilino.

>[!NOTE]
> As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. São utilizados apenas para ativar a funcionalidade.

Siga estas instruções para verificar que ativou SSO totalmente integrado corretamente:

1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global para o seu inquilino.
2. Selecione **do Azure Active Directory** no painel esquerdo.
3. Selecione **do Azure AD Connect**.
4. Certifique-se de que o **totalmente integrada-início de sessão único** funcionalidade aparece como **ativado**.

![Portal do Azure: painel do Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Passo 3: Implementar a funcionalidade

Para implementar a funcionalidade aos seus utilizadores, tem de adicionar os seguintes URLs de AD do Azure para as definições de zona de Intranet dos utilizadores através da política de grupo no Active Directory:

- https://AutoLogon.microsoftazuread-SSO.com
- https://aadg.Windows.NET.nsatc.NET

Além disso, tem de ativar uma política de zona de Intranet definição chamado **permita que as atualizações na barra de estado através do script** através da política de grupo. 

>[!NOTE]
> As seguintes instruções funcionam apenas para o Internet Explorer e Google Chrome no Windows (se a partilha um conjunto de URLs de sites fidedignos com o Internet Explorer). Leia a secção seguinte para obter instruções sobre como configurar o Mozilla Firefox e Google Chrome em Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Por que motivo é necessário modificar as definições de zona de Intranet dos utilizadores?

Por predefinição, o browser calcula automaticamente a zona correta, Internet ou da Intranet, a partir de um URL específico. Por exemplo, "http://contoso/" mapeia para a zona da Intranet, enquanto "http://intranet.contoso.com/" mapeia para a zona de Internet (porque o URL contém um período). Browsers não enviam permissões de Kerberos para um ponto final da nuvem, como os dois URLs do Azure AD, a menos que tem de adicionar explicitamente o URL da zona de Intranet do browser.

### <a name="detailed-steps"></a>Passos detalhados

1. Abra a ferramenta de Editor de gestão de políticas de grupo.
2. Editar a política de grupo é aplicada a alguns ou todos os seus utilizadores. Este exemplo utiliza **política de domínio predefinida**.
3. Navegue até à **configuração do utilizador** > **modelos administrativos** > **componentes do Windows**  >   **Internet Explorer** > **painel de controlo de Internet** > **página segurança**. Em seguida, selecione **Site à zona atribuição lista**.
    ![Início de sessão único](./media/active-directory-aadconnect-sso/sso6.png)
4. Ativar a política e, em seguida, introduza os seguintes valores na caixa de diálogo:
   - **Nome do valor**: O Azure AD URLs onde são reencaminhados de permissões de Kerberos.
   - **Valor** (dados): **1** indica a zona de Intranet.

   O resultado tem o seguinte aspeto:

    Valor: https://autologon.microsoftazuread-sso.com
  
    Dados: 1
        
   Valor: https://aadg.windows.net.nsatc.net

    Dados: 1

   >[!NOTE]
   > Se pretender não permitir alguns utilizadores de utilizar o SSO totalmente integrada (por exemplo, se estes utilizadores iniciam sessão em quiosques partilhados), defina os valores anteriores para **4**. Esta ação adiciona os URLs do AD do Azure para a zona restrita e falha SSO totalmente integrado sempre.
   >

5. Selecione **OK**e, em seguida, selecione **OK** novamente.

    ![Início de sessão único](./media/active-directory-aadconnect-sso/sso7.png)

6. Navegue até à **configuração do utilizador** > **modelos administrativos** > **componentes do Windows**  >   **Internet Explorer** > **painel de controlo de Internet** > **página segurança** > **zona de Intranet**. Em seguida, selecione **permita que as atualizações na barra de estado através do script**.

    ![Início de sessão único](./media/active-directory-aadconnect-sso/sso11.png)

7. Ativar a definição de política e, em seguida, selecione **OK**.

    ![Início de sessão único](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Considerações de browser

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox automaticamente não utiliza a autenticação Kerberos. Cada utilizador tem de adicionar manualmente os URLs do AD do Azure para as respetivas definições Firefox utilizando os seguintes passos:
1. Execute o Firefox e introduza `about:config` na barra de endereço. Dispensa quaisquer notificações que vê.
2. Procure o **network.negotiate-auth.trusted-uris** preferência. Essa preferência apresenta uma lista de Web sites fidedignos do Firefox para a autenticação Kerberos.
3. Clique com botão direito e selecione **modificar**.
4. Introduza https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net no campo.
5. Selecione **OK** e, em seguida, volte a abrir o browser.

#### <a name="safari-on-mac-os"></a>Safari no Mac OS

Certifique-se de que o computador que executa o Mac OS está associado ao Azure AD. Para obter instruções sobre a associação do Azure AD, consulte [melhores práticas para integrar o OS X com o Active Directory](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-on-mac-os"></a>Google Chrome no Mac OS

Google Chrome em outras plataformas de não-Windows e Mac OS, consulte [a lista de políticas de projeto Chromium](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obter informações sobre como URLs da lista branca o Azure AD para autenticação integrada.

A utilização de extensões de política de grupo do Active Directory de terceiros para implementar os URLs do Azure AD para o Firefox e Google Chrome nos utilizadores de Mac está fora do âmbito deste artigo.

#### <a name="known-browser-limitations"></a>Limitações conhecidas do browser

SSO totalmente integrada não funciona no modo de navegação privado em browsers Firefox e limite. Também não funciona com o Internet Explorer se o browser está em execução no modo protegido avançado.

>[!IMPORTANT]
>Iremos recentemente revertida suporte para o limite investigar problemas reportados por clientes.

## <a name="step-4-test-the-feature"></a>Passo 4: Testar a funcionalidade

Para testar a funcionalidade para um utilizador específico, certifique-se de que todas as condições seguintes são cumpridos:
  - O utilizador inicia sessão num dispositivo empresarial.
  - O dispositivo é associado ao domínio do Active Directory.
  - O dispositivo tem uma ligação direta para o seu controlador de domínio (DC) na rede da empresa com ou sem fios ou através de uma ligação de acesso remoto, tais como uma ligação VPN.
  - Tiver [implementado a funcionalidade](##step-3-roll-out-the-feature) a este utilizador através da política de grupo.

Para testar o cenário em que o utilizador introduz apenas o nome de utilizador, mas não a palavra-passe:
   - Inicie sessão no https://myapps.microsoft.com/ numa nova sessão do browser privada.

Para testar o cenário em que o utilizador não tem de introduzir o nome de utilizador ou a palavra-passe, utilize um destes passos: 
   - Inicie sessão no https://myapps.microsoft.com/contoso.onmicrosoft.com numa nova sessão do browser privada. Substitua *contoso* com o nome do seu inquilino.
   - Inicie sessão no https://myapps.microsoft.com/contoso.com numa nova sessão do browser privada. Substitua *contoso.com* com um domínio verificado (não um domínio Federado) no seu inquilino.

## <a name="step-5-roll-over-keys"></a>Passo 5: Rollover de chaves

No passo 2, o Azure AD Connect cria contas de computador (que representa o Azure AD) em todas as florestas do Active Directory em que tiver ativado o SSO totalmente integrada. Para obter mais informações, consulte [do Azure Active Directory totalmente integrada Single Sign-On: Descrição profunda técnica](active-directory-aadconnect-sso-how-it-works.md). Para segurança melhorada, recomendamos que periodicamente implementar através de Kerberos as chaves de desencriptação destas contas de computador. Para obter instruções sobre como implementar através de chaves, consulte [do Azure Active Directory totalmente integrada Single Sign-On: Perguntas mais frequentes](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Não precisa de efetuar este passo _imediatamente_ depois de ter ativado a funcionalidade. Rollover as chaves de desencriptação de Kerberos, pelo menos, uma vez a cada 30 dias.

## <a name="next-steps"></a>Passos seguintes

- [Descrição detalhada da Technical](active-directory-aadconnect-sso-how-it-works.md): compreender como funciona a funcionalidade totalmente integrado Single Sign-On.
- [Perguntas mais frequentes](active-directory-aadconnect-sso-faq.md): obter respostas a perguntas mais frequentes sobre totalmente integrada Single Sign-On.
- [Resolver problemas](active-directory-aadconnect-troubleshoot-sso.md): Saiba como resolver problemas comuns com a funcionalidade totalmente integrado Single Sign-On.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para novos pedidos de funcionalidade de ficheiros.

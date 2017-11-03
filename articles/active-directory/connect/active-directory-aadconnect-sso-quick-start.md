---
title: "Azure AD Connect: Totalmente integrada Single Sign-On - introdução | Microsoft Docs"
description: "Este artigo descreve como começar com Azure Active Directory totalmente integrada Single Sign-On."
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
ms.openlocfilehash: 8975a82c5573cc0c284e1fc76cd0ef2c19fbbd72
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory totalmente integrada Single Sign-On: início rápido

## <a name="how-to-deploy-seamless-sso"></a>Como implementar o SSO totalmente integrada

Azure Active Directory totalmente integrada Single Sign-On (Azure AD totalmente integrada SSO) inicia automaticamente os utilizadores quando estão nos respetivos ambientes de trabalho empresariais ligados à rede da sua empresa. Fornece aos utilizadores acesso fácil às suas aplicações baseado na nuvem sem necessidade de quaisquer componentes adicionais no local.

Para implementar o SSO totalmente integrada, tem de seguir estes passos:

## <a name="step-1-check-prerequisites"></a>Passo 1: Os pré-requisitos de verificação

Certifique-se de que os seguintes pré-requisitos estão assegurados:

1. Configurar o servidor do Azure AD Connect: Se utilizar [autenticação pass-through](active-directory-aadconnect-pass-through-authentication.md) como método de início de sessão, não é necessária nenhuma verificação de pré-requisitos adicional. Se utilizar [sincronização de Hash de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) como método de início de sessão e se existir uma firewall entre o Azure AD Connect e o Azure AD, certifique-se de que:
- Estiver a utilizar versões 1.1.644.0 ou posterior do Azure AD Connect. 
- Se a sua firewall ou proxy permite listas brancas DNS, as ligações da lista branca ao  **\*. msappproxy.net** URLs através da porta 443. Se não, permitir o acesso ao [intervalos de IP de DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizada semanalmente. Este pré-requisito é aplicável apenas ao ativar a funcionalidade, não para inícios de sessão de utilizador reais.

    >[!NOTE]
    >Azure AD Connect as versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização de Hash de palavra-passe. Se lhe _não_ tencionar utilizar a sincronização de Hash de palavra-passe em conjunto com a autenticação pass-through, leia o [notas de versão do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) para obter mais informações.

2. Precisa de credenciais de administrador de domínio para cada floresta do AD que sincroniza com o Azure AD (utilizando o Azure AD Connect) e para cujos utilizadores que pretende ativar a SSO totalmente integrada.

## <a name="step-2-enable-the-feature"></a>Passo 2: Ativar a funcionalidade

SSO totalmente integrada pode ser ativada utilizando [do Azure AD Connect](active-directory-aadconnect.md).

Se estão a fazer uma instalação de raiz do Azure AD Connect, escolha o [caminho de instalação personalizada](active-directory-aadconnect-get-started-custom.md). Na página "Utilizador início de sessão", selecione a opção "Ativar o início de sessão único em".

![O Azure AD Connect - sessão do utilizador](./media/active-directory-aadconnect-sso/sso8.png)

Se já tiver uma instalação do Azure AD Connect, escolha "Alterar utilizador-página sessão" no Azure AD Connect e clique em "Seguinte". Em seguida, selecione a opção "Ativar o início de sessão único em".

![O Azure AD Connect - alteração utilizador iniciar sessão](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Continue o assistente até chegar a página "Ativar o início de sessão único em". Forneça credenciais de administrador de domínio para cada floresta do AD que sincroniza com o Azure AD (utilizando o Azure AD Connect) e para cujos utilizadores que pretende ativar a SSO totalmente integrada. 

Após a conclusão do assistente, SSO totalmente integrada está ativada no seu inquilino.

>[!NOTE]
> As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD, mas só são utilizadas para ativar a funcionalidade.

Siga estas instruções para verificar que ativou SSO totalmente integrado corretamente:

1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de Administrador Global para o seu inquilino.
2. Selecione **do Azure Active Directory** no painel de navegação esquerdo.
3. Selecione **do Azure AD Connect**.
4. Certifique-se de que o **totalmente integrada Single Sign-On** funcionalidade mostra como **ativado**.

![Portal do Azure - painel do Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Passo 3: Implementar a funcionalidade

Para implementar a funcionalidade aos seus utilizadores, tem de adicionar os seguintes URLs do Azure AD para definições de zona da Intranet dos utilizadores através da política de grupo no Active Directory:

- https://AutoLogon.microsoftazuread-SSO.com
- https://aadg.Windows.NET.nsatc.NET

Além disso, terá de ativar uma definição de política do zona da Intranet (utilizando a política de grupo) chamada "Permitir que as atualizações à barra de estado através do script".

>[!NOTE]
> As seguintes instruções só funcionam para o Internet Explorer e Google Chrome no Windows (se o conjunto de URLs de sites fidedignos partilha com o Internet Explorer). Leia a secção seguinte para obter instruções para configurar o Mozilla Firefox e Google Chrome em Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Por que motivo é necessário modificar as definições de zona de Intranet dos utilizadores?

Por predefinição, o browser calcula automaticamente a zona correta (Internet ou da Intranet) a partir de um URL. Por exemplo, http://contoso/ está mapeado para a zona da Intranet, enquanto que http://intranet.contoso.com/ está mapeado para a zona de Internet (porque o URL contém um período). Os browsers não enviam permissões Kerberos um ponto final da nuvem, como os dois URLs de AD do Azure - a menos que o respetivo URL explicitamente é adicionado à zona de Intranet do browser.

### <a name="detailed-steps"></a>Passos detalhados

1. Abra a ferramenta de gestão de políticas de grupo.
2. Editar a política de grupo é aplicada a alguns ou todos os seus utilizadores. Neste exemplo, utilizamos o **política de domínio predefinida**.
3. Navegue para **utilizador computador\Modelos administrativos\Componentes Components\Internet Explorer\Internet controlo Panel\Security página** e selecione **Site à zona atribuição lista**.
![Início de sessão único](./media/active-directory-aadconnect-sso/sso6.png)
4. Ativar a política e introduza a seguinte valores (em que as permissões Kerberos são reencaminhados de URLs do Azure AD) e os dados (*1* indica a zona da Intranet) na caixa de diálogo.

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> Se pretender não permitir alguns utilizadores de utilizar o SSO totalmente integrado - por exemplo, se esses utilizadores são iniciar sessão em quiosques partilhados - definir os valores anteriores *4*. Esta ação adiciona os URLs do AD do Azure para a zona restrito e falha SSO totalmente integrado sempre.

5. Clique em **OK** e **OK** novamente.
![Início de sessão único](./media/active-directory-aadconnect-sso/sso7.png)
6. Navegue para **computador\Modelos administrativos\Componentes Components\Internet Explorer\Internet controlo Panel\Security Page\Intranet horário utilizador** e selecione **permita que as atualizações na barra de estado através do script**.
![Início de sessão único](./media/active-directory-aadconnect-sso/sso11.png)
7. Ativar a definição de política e clique em **OK**.
![Início de sessão único](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Considerações de browser

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox não automaticamente a autenticação Kerberos. Cada utilizador tem de adicionar manualmente os URLs do AD do Azure para as respetivas definições de Firefox utilizando os seguintes passos:
1. Execute o Firefox e introduza `about:config` na barra de endereço. Dispensa quaisquer notificações que vê.
2. Procure o **network.negotiate-auth.trusted-uris** preferência. Essa preferência apresenta uma lista de Web sites fidedignos do Firefox para a autenticação Kerberos.
3. Clique com botão direito e selecione "Modificar".
4. Introduza "https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net" no campo.
5. Clique em "OK" e reabra o browser.

#### <a name="safari-on-mac-os"></a>Safari no Mac OS

Certifique-se de que a máquina com o Mac OS está associada ao AD. Consulte as instruções sobre como fazê-lo [aqui](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-on-mac-os"></a>Google Chrome no Mac OS

Google Chrome em outras plataformas de não-Windows e Mac OS, consulte [neste artigo](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obter informações sobre como URLs da lista branca o Azure AD para autenticação integrada.

Utilizar extensões de política de grupo do Active Directory de terceiros para implementar os URLs do Azure AD para o Firefox e Google Chrome nos utilizadores de Mac está fora do âmbito deste artigo.

#### <a name="known-browser-limitations"></a>Limitações conhecidas do browser

SSO totalmente integrada não funciona no modo de navegação privado em browsers Firefox e limite. Também não funciona com o Internet Explorer se o browser está em execução no modo de proteção avançada.

>[!IMPORTANT]
>Iremos recentemente revertida suporte para o limite investigar problemas reportados por clientes.

## <a name="step-4-test-the-feature"></a>Passo 4: Testar a funcionalidade

Para testar a funcionalidade para um utilizador específico, certifique-se de que _todos os_ as condições seguintes são cumpridos:
  - O utilizador iniciar sessão num dispositivo empresarial.
  - O dispositivo foi anteriormente aderiu ao domínio do Active Directory (AD).
  - O dispositivo tem uma ligação direta para o domínio controlador (DC), na rede da empresa com ou sem fios ou através de uma ligação de acesso remoto, tais como uma ligação VPN.
  - Tiver [implementado a funcionalidade](##step-3-roll-out-the-feature) a este utilizador através da política de grupo.

Para testar o cenário em que o utilizador introduz apenas o nome de utilizador, mas não a palavra-passe:
- Inicie sessão no *https://myapps.microsoft.com/* numa nova sessão do browser privada.

Para testar o cenário em que o utilizador não tem de introduzir o nome de utilizador ou a palavra-passe: 
- Inicie sessão no *https://myapps.microsoft.com/contoso.onmicrosoft.com* numa nova sessão do browser privada. Substituir "*contoso*" com o nome do seu inquilino.
- Ou inicie sessão no *https://myapps.microsoft.com/contoso.com* numa nova sessão do browser privada. Substituir "*contoso.com*" com um domínio verificado (não um domínio Federado) no seu inquilino.

## <a name="step-5-roll-over-keys"></a>Passo 5: Rollover de chaves

No passo 2, o Azure AD Connect cria contas de computador (que representa o Azure AD) em todas as florestas do AD em que tiver ativado o SSO totalmente integrada. Saiba mais em detalhe [aqui](active-directory-aadconnect-sso-how-it-works.md). Para segurança melhorada, recomenda-se que periodicamente implementar através de Kerberos as chaves de desencriptação destas contas de computador. As instruções sobre como rollover são [aqui](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Não precisa de efetuar este passo _imediatamente_ depois de ter ativado a funcionalidade. Rollover as chaves de desencriptação de Kerberos, pelo menos, a cada 30 dias.

## <a name="next-steps"></a>Passos seguintes

- [Descrição detalhada da Technical](active-directory-aadconnect-sso-how-it-works.md) -compreender como funciona esta funcionalidade.
- [Perguntas mais frequentes](active-directory-aadconnect-sso-faq.md) -respostas para as perguntas mais frequentes.
- [Resolver problemas](active-directory-aadconnect-troubleshoot-sso.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.

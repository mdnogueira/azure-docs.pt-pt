---
title: "Do Azure AD Connect: A autenticação pass-through - perguntas mais frequentes | Microsoft Docs"
description: "Respostas a perguntas mais frequentes sobre a autenticação pass-through do Active Directory do Azure."
services: active-directory
keywords: "Authentication do Azure AD Connect pass-through, a instalação do Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
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
ms.openlocfilehash: e1bd58797124210f7c31e90fb20d728289a04ba2
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Authentication do Azure Active Directory pass-through: Perguntas mais frequentes

Neste artigo, vamos abordar perguntas mais frequentes sobre a autenticação pass-through do Azure Active Directory (Azure AD). Manter a verificação para o novo conteúdo.

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>Qual os Azure AD início de sessão métodos - autenticação pass-through, sincronização de Hash de palavra-passe e serviços de Federação do Active Directory (AD FS) - devo escolher?

Depende do seu ambiente no local e os requisitos da organização. Rever este artigo para um [comparação dos vários do Azure AD início de sessão métodos](active-directory-aadconnect-user-signin.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>É uma funcionalidade livre de autenticação pass-through?

A autenticação pass-through é uma funcionalidade livre e não precisa de quaisquer edições pagas do Azure AD para utilizá-lo.

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Está disponível na autenticação pass-through [Datacenters da Microsoft Cloud](http://www.microsoft.de/cloud-deutschland) e [na nuvem do Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

A autenticação pass-through não, apenas está disponível na instância em todo o mundo do Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-accessmd-work-with-pass-through-authentication"></a>Does [acesso condicional](../active-directory-conditional-access.md) funcionam com a autenticação pass-through?

Sim, todas as capacidades de acesso condicional, incluindo o multi-factor Authentication do Azure, trabalham com a autenticação pass-through.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>A autenticação pass-through suporta "ID alternativo" como o nome de utilizador, em vez de "userPrincipalName"?

Sim. Suporta a autenticação pass-through `Alternate ID` como o nome de utilizador quando configurados no Azure AD Connect, conforme mostrado [aqui](active-directory-aadconnect-get-started-custom.md). Nem todas as aplicações do Office 365 suportam `Alternate ID`. Consulte a documentação específica da aplicação para a declaração de suporte.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Sincronização de Hash de palavra-passe atuar como contingência para autenticação pass-through?

Não, autenticação pass-through _não_ automaticamente a ativação pós-falha para a sincronização de Hash de palavra-passe. Funciona apenas como contingência para [cenários que a autenticação pass-through não suporta atualmente](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Para evitar falhas de início de sessão de utilizador, deve configurar a autenticação pass-through para [elevada disponibilidade](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Pode instalar um [Proxy de aplicações do Azure AD](../active-directory-application-proxy-get-started.md) conector no mesmo servidor como um agente de autenticação pass-through?

Sim, esta configuração é suportada com as versões do agente de autenticação pass-through rebranded (versões 1.5.193.0 ou posterior).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Quais as versões do Azure AD Connect e o agente de autenticação pass-through precisa?

Terá de versão 1.1.486.0 ou posterior para o Azure AD Connect e 1.5.58.0 ou posterior para o agente de autenticação pass-through para esta funcionalidade funcione. Todos os softwares devem ser instalados em servidores com o Windows Server 2012 R2 ou posterior.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>O que acontece se a palavra-passe do meu utilizador expirou e tentar iniciar sessão utilizando a autenticação pass-through?

Se tiver configurado [repetição de escrita de palavras-passe](../active-directory-passwords-update-your-own-password.md) para um utilizador específico, e se o utilizador inicia sessão utilizando a autenticação pass-through, pode alterar ou repor as palavras-passe. As palavras-passe são gravadas no local do Active Directory conforme esperado.

No entanto, se a repetição de escrita de palavras-passe não está configurada para um utilizador específico ou se o utilizador não tem um válido do Azure AD licença atribuída, o utilizador não é possível atualizar a palavra-passe na nuvem. Estes não é possível atualizar a palavra-passe, mesmo se a palavra-passe expirou. O utilizador em vez disso, vê esta mensagem: "organização não lhe permitem atualizar a palavra-passe neste site. Atualize-lo, de acordo com o método recomendado pela sua organização, ou peça ao administrador se precisar de ajuda." O utilizador ou o administrador tem de repor a palavra-passe no Active Directory no local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Como autenticação pass-through a proteger contra ataques de palavra-passe de força bruta?

Leitura [neste artigo](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) para obter mais informações.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>O que a agentes de autenticação pass-through a comunicar através das portas 80 e 443?

- Os agentes de autenticação efetuam pedidos HTTPS através da porta 443 para todas as operações de funcionalidade.
- Os agentes de autenticação efetuam pedidos HTTP através da porta 80 para transferir as listas de revogação de certificados SSL.

     >[!NOTE]
     >Nas atualizações recentes é reduzida, o número de portas, necessário para a funcionalidade. Se tiver de versões anteriores do Azure AD Connect ou o agente de autenticação, mantém estas portas abertas também: 5671, 8080, 9090, 9091, 9350, 9352 e 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Os agentes de autenticação pass-through comunicar através de um servidor de proxy web de saída?

Sim. Se WPAD (Web Proxy a deteção automática) estiver ativado no seu ambiente no local, os agentes de autenticação automaticamente tentar localizar e utilizar um servidor web proxy na rede.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Pode instalar dois ou mais agentes de autenticação pass-through no mesmo servidor?

Não, apenas pode instalar um agente de autenticação pass-through num único servidor. Se pretender configurar a autenticação pass-through para elevada disponibilidade, siga as instruções deste [artigo](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) em vez disso.

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>Posso já utilizar serviços de Federação do Active Directory (AD FS) para o início de sessão do Azure AD. Como posso alterná-lo para a autenticação pass-through?

Se tiver configurado os AD FS como o método de início de sessão utilizando o Assistente do Azure AD Connect, altere o método de início de sessão do utilizador para autenticação pass-through. Esta alteração permite a autenticação pass-through no inquilino e converte _todos os_ os domínios de Federated em domínios geridos. Todos os início de sessão dos pedidos subsequentes no seu inquilino são processados pela autenticação pass-through. Atualmente, não é possível suportado dentro do Azure AD Connect para utilizar uma combinação de AD FS e a autenticação pass-through em domínios diferentes.

Se o AD FS foi configurado como método de início de sessão _fora_ do Assistente do Azure AD Connect, alterar o método de início de sessão do utilizador para autenticação pass-through (a partir da opção "Não configurar"). Esta alteração permite a autenticação pass-through no inquilino. No entanto, todos os seus domínios de Federated continuam a utilizar o AD FS para início de sessão. Utilize o PowerShell para converter manualmente alguns ou todos os estes domínios Federated domínios geridos. Depois de que todos os início de sessão pedidos nos domínios geridos (_apenas_) são processados pela autenticação pass-through.

>[!IMPORTANT]
>A autenticação pass-through não processa início de sessão para o Azure apenas na nuvem os utilizadores do AD.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>Pode utilizar autenticação pass-through num ambiente multifloresta AD?

Sim. Ambientes de várias florestas são suportadas se existirem fidedignidades de floresta entre as florestas do AD e se o encaminhamento de sufixo de nome se encontra corretamente configurado.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quantos agentes de autenticação pass-through é necessário instalar?

Instalar agentes de autenticação pass-through vários garante [elevada disponibilidade](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). No entanto não fornece determinista balanceamento da carga entre os agentes de autenticação.

Considere o horário de pico e carga média de pedidos de início de sessão que pretende ver no seu inquilino. Como um benchmark, um único agente de autenticação pode processar autenticações 300,000 para 400,000 por segundo um padrão 4 núcleos de CPU, servidor com 16 GB de RAM. Para a maioria dos clientes, os agentes de autenticação de dois ou três no total são suficientes para elevada disponibilidade e a capacidade.

Recomendamos que instale agentes de autenticação próximo os controladores de domínio para melhorar a latência de início de sessão.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Pode instalar o agente de autenticação pass-through primeiro num servidor diferente daquela que executa o Azure AD Connect?

Não, este cenário é _não_ suportado.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Quantos agentes de autenticação pass-through que deverá a instalar

Recomendamos que:

- Instalar agentes de autenticação de dois ou três no total. Esta configuração é suficiente para a maioria dos clientes.
- Instalar agentes de autenticação nos controladores de domínio (ou como próximo-los como possíveis) para melhorar a latência de início de sessão.
- Certifique-se de que os servidores (em que a autenticação se encontram instalados agentes) são adicionadas à mesma floresta do AD como os utilizadores cujas palavras-passe tem de ser validada.

>[!NOTE]
>Não há um limite de sistema de 12 de autenticação de agentes por inquilino.

## <a name="how-can-i-disable-pass-through-authentication"></a>Como desativar a autenticação pass-through?

Executar novamente o Assistente do Azure AD Connect e alterar o método de início de sessão do utilizador da autenticação pass-through para outro método. Esta alteração desativa a autenticação pass-through no inquilino e desinstala o agente de autenticação do servidor. Tem de desinstalar manualmente os agentes de autenticação de outros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>O que acontece quando posso desinstalar um agente de autenticação pass-through?

Desinstalar um agente de autenticação pass-through de um servidor faz com que a sua paragem aceitar pedidos de início de sessão. Certifique-se de que tem outro agente de autenticação em execução antes de efetuar esta operação, para evitar interrompendo a sessão do utilizador no seu inquilino.

## <a name="next-steps"></a>Passos seguintes
- [**Limitações atuais** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) -saber quais os cenários que são suportados e aqueles que não são.
- [**Início Rápido** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) - obter cópias de segurança e executar a autenticação pass-through do Azure AD.
- [**Bloqueio do smart** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -capacidade de configurar o bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [**Descrição detalhada da Technical** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -compreender como funciona esta funcionalidade.
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**Descrição detalhada da segurança** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -particularmente técnicas obter informações adicionais sobre a funcionalidade.
- [**Azure SSO totalmente integrada de AD** ](active-directory-aadconnect-sso.md) -Saiba mais sobre esta funcionalidade complementares.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.

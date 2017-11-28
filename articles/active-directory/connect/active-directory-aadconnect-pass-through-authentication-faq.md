---
title: "Do Azure AD Connect: A autenticação pass-through - perguntas mais frequentes | Microsoft Docs"
description: "Respostas a perguntas mais frequentes sobre a autenticação pass-through do Active Directory do Azure"
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
ms.openlocfilehash: d6a405f7245bf1b9635872efd0e29f8361d6a2f6
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Authentication do Azure Active Directory pass-through: Perguntas mais frequentes

Este artigo aborda perguntas mais frequentes sobre a autenticação pass-through do Azure Active Directory (Azure AD). Manter a verificação para o conteúdo atualizado.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Quais os métodos para iniciar sessão Azure AD, a autenticação pass-through, palavra-passe hash sincronização e serviços de Federação do Active Directory (AD FS), deve escolher?

Depende do seu ambiente no local e os requisitos da organização. Reveja o [do Azure AD Connect início de sessão opções do utilizador](active-directory-aadconnect-user-signin.md) artigo para ver uma comparação dos vários do Azure AD início de sessão métodos.

## <a name="is-pass-through-authentication-a-free-feature"></a>É uma funcionalidade livre de autenticação pass-through?

A autenticação pass-through é uma funcionalidade livre. Não precisa de quaisquer edições pagas do Azure AD para utilizá-lo.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Está disponível na autenticação pass-through a [cloud do Microsoft Azure Datacenters](http://www.microsoft.de/cloud-deutschland) e o [cloud do Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Não. A autenticação pass-through só está disponível a instância mundial do Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Does [acesso condicional](../active-directory-conditional-access-azure-portal.md) funcionam com a autenticação pass-through?

Sim. Todas as capacidades de acesso condicional, incluindo o multi-factor Authentication do Azure, trabalham com a autenticação pass-through.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>A autenticação pass-through suporta "ID alternativo" como o nome de utilizador, em vez de "userPrincipalName"?

Sim. Suporta a autenticação pass-through `Alternate ID` como o nome de utilizador quando configurado no Azure AD Connect. Para obter mais informações, consulte [instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Nem todas as aplicações do Office 365 suportam `Alternate ID`. Consulte a declaração de suporte de documentação específica da aplicação.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Sincronização de hash de palavra-passe atua como contingência para autenticação pass-through?

Não. A autenticação pass-through _não_ automaticamente a ativação pós-falha para a sincronização de hash de palavra-passe. Funciona apenas como contingência para [cenários que a autenticação pass-through não suporta atualmente](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Para evitar falhas de início de sessão de utilizador, deve configurar a autenticação pass-through para [elevada disponibilidade](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Pode instalar um [Proxy de aplicações do Azure AD](../active-directory-application-proxy-get-started.md) conector no mesmo servidor como um agente de autenticação pass-through?

Sim. As versões do agente de autenticação pass-through, versão 1.5.193.0 rebranded ou posterior, suporta esta configuração.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Quais as versões do Azure AD Connect e o agente de autenticação pass-through precisa?

Para esta funcionalidade funcione, tem de versão 1.1.486.0 ou posterior para o Azure AD Connect e 1.5.58.0 ou posterior para o agente de autenticação pass-through. Instale todos os softwares em servidores com o Windows Server 2012 R2 ou posterior.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>O que acontece se a palavra-passe do meu utilizador expirou e tentar iniciar sessão utilizando a autenticação pass-through?

Se tiver configurado [repetição de escrita de palavras-passe](../active-directory-passwords-update-your-own-password.md) para um utilizador específico, e se o utilizador inicia sessão utilizando a autenticação pass-through, pode alterar ou repor as palavras-passe. As palavras-passe são gravadas no local do Active Directory conforme esperado.

Se não tiver configurado a repetição de escrita de palavras-passe para um utilizador específico, ou se o utilizador não tem um válido do Azure AD licença atribuída, o utilizador não é possível atualizar a palavra-passe na nuvem. Estes não é possível atualizar a palavra-passe, mesmo que a palavra-passe expirou. O utilizador em vez disso, vê esta mensagem: "organização não lhe permitem atualizar a palavra-passe neste site. A atualização, de acordo com o método recomendado pela sua organização, ou peça ao administrador se precisar de ajuda." O utilizador ou o administrador tem de repor a palavra-passe no Active Directory no local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Como é que a autenticação pass-through, proteger contra ataques de palavra-passe de força bruta?

Leitura [autenticação pass-through do Active Directory do Azure: bloqueio inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) para obter mais informações.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>O que a agentes de autenticação pass-through a comunicar através das portas 80 e 443?

- Os agentes de autenticação efetuam pedidos HTTPS através da porta 443 para todas as operações de funcionalidade.
- Os agentes de autenticação efetuam pedidos HTTP através da porta 80 para transferir as listas de revogação de certificado SSL (CRLs).

     >[!NOTE]
     >Atualizações recentes reduzida, o número de portas que requer que a funcionalidade. Se tiver de versões anteriores do Azure AD Connect ou o agente de autenticação, mantém estas portas abertas bem: 5671, 8080, 9090, 9091, 9350, 9352 e 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Os agentes de autenticação pass-through comunicar através de um servidor de proxy web de saída?

Sim. Se a deteção automática de Proxy Web (WPAD) estiver ativado no seu ambiente no local, os agentes de autenticação automaticamente tentar localizar e utilizar um servidor web proxy na rede.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Pode instalar dois ou mais agentes de autenticação pass-through no mesmo servidor?

Não, apenas pode instalar um agente de autenticação pass-through num único servidor. Se pretender configurar a autenticação pass-through para elevada disponibilidade, siga as instruções em [autenticação pass-through do Active Directory do Azure: início rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Já posso utilizar o AD FS para iniciar sessão Azure AD. Como posso alterná-lo para a autenticação pass-through?

Se tiver configurado o AD FS como método de iniciar sessão através do Assistente do Azure AD Connect, altere o método que o utilizador utiliza para iniciar sessão para a autenticação pass-through. Esta alteração permite a autenticação pass-through no inquilino e converte _todos os_ os domínios em domínios geridos federados. A autenticação pass-through processa todos os pedidos subsequentes para iniciar sessão no seu inquilino. Atualmente, não é possível suportado dentro do Azure AD Connect para utilizar uma combinação de AD FS e a autenticação pass-through em domínios diferentes.

Se o AD FS foi configurado como método da sessão _fora_ Assistente do Azure AD Connect, alterar o utilizador inicie sessão método para a autenticação pass-through. Pode efetuar esta alteração do **não configurar** opção. Esta alteração permite que a autenticação pass-through no inquilino, mas todos os seus domínios federados irão continuar a utilizar o AD FS para início de sessão. Utilize o PowerShell para converter manualmente alguns ou todos estes domínios federados domínios geridos. Depois de efetuar essa alteração *apenas* autenticação pass-through processa todos os pedidos para iniciar sessão para os domínios geridos.

>[!IMPORTANT]
>A autenticação pass-through não processa o início de sessão para o Azure apenas na nuvem os utilizadores do AD.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Pode utilizar autenticação pass-through no ambiente de várias florestas do Active Directory?

Sim. Ambientes de várias florestas são suportadas se existirem fidedignidades de floresta entre as florestas do Active Directory e se o encaminhamento de sufixo de nome se encontra corretamente configurado.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quantos agentes de autenticação pass-through é necessário instalar?

Instalar agentes de autenticação pass-through vários garante [elevada disponibilidade](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). No entanto, não fornece determinista balanceamento da carga entre os agentes de autenticação.

Considere o horário de pico e carga média de pedidos de início de sessão que pretende ver no seu inquilino. Como um benchmark, um único agente de autenticação pode processar autenticações 300,000 para 400,000 por segundo um padrão 4 núcleos de CPU, servidor de RAM de 16 GB. Para a maioria dos clientes, os agentes de autenticação de dois ou três no total são suficientes para elevada disponibilidade e a capacidade.

Deve instalar agentes de autenticação próximo os controladores de domínio para melhorar a latência de início de sessão.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Pode instalar o agente de autenticação pass-through primeiro num servidor diferente daquela que executa o Azure AD Connect?

Não, este cenário é _não_ suportado.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Quantos agentes de autenticação pass-through que deverá a instalar

Recomendamos que:

- Instalar agentes de autenticação de dois ou três no total. Esta configuração é suficiente para a maioria dos clientes.
- Instalar agentes de autenticação nos controladores de domínio (ou como próximo-los como possíveis) para melhorar a latência de início de sessão.
- Certifique-se de que adiciona servidores onde instalou os agentes de autenticação para a mesma floresta do Active Directory que os utilizadores cujas palavras-passe tem de validar.

>[!NOTE]
>Não há um limite de sistema de 12 de autenticação de agentes por inquilino.

## <a name="how-can-i-disable-pass-through-authentication"></a>Como desativar a autenticação pass-through?

Executar novamente o Assistente do Azure AD Connect e alterar o método de início de sessão do utilizador da autenticação pass-through para outro método. Esta alteração desativa a autenticação pass-through no inquilino e desinstala o agente de autenticação do servidor. Tem de desinstalar manualmente os agentes de autenticação de outros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>O que acontece quando posso desinstalar um agente de autenticação pass-through?

Se desinstalar um agente de autenticação pass-through a partir de um servidor, faz com que o servidor parar a aceitar pedidos de início de sessão. Para evitar interrompendo a capacidade de utilizador de início de sessão no seu inquilino, certifique-se de que tem outro agente de autenticação em execução antes de desinstalar um agente de autenticação pass-through.

## <a name="next-steps"></a>Passos seguintes
- [Limitações atuais](active-directory-aadconnect-pass-through-authentication-current-limitations.md): saber que cenários são suportados e aqueles que não são.
- [Início Rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): começar a trabalhar na autenticação pass-through do Azure AD.
- [Bloqueio do smart](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Saiba como configurar a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Descrição detalhada da Technical](active-directory-aadconnect-pass-through-authentication-how-it-works.md): compreender como funciona a funcionalidade de autenticação pass-through.
- [Resolver problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [Descrição detalhada da segurança](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): obter particularmente técnicas informações sobre a funcionalidade de autenticação pass-through.
- [Azure SSO totalmente integrada de AD](active-directory-aadconnect-sso.md): saber mais sobre esta funcionalidade complementares.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para novos pedidos de funcionalidade de ficheiros.


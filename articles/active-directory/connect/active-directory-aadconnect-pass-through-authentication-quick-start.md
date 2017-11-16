---
title: "Autenticação do Azure AD pass-through – introdução | Microsoft Docs"
description: "Este artigo descreve como começar com a autenticação pass-through do Azure Active Directory (Azure AD)."
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
ms.openlocfilehash: e0b58142a2ed17d2cd4749b33e9e80ff1a01662a
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Do Azure autenticação do Active Directory pass-through: Início rápido

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Como implementar a autenticação pass-through do Azure AD

Autenticação de pass-through do Azure Active Directory (Azure AD) permite aos utilizadores iniciar sessão no local e de aplicações baseado na nuvem utilizando as mesmas palavras-passe. Os utilizadores, inicia sessão ao validar as palavras-passe diretamente no seu Active Directory no local.

>[!IMPORTANT]
>Se tiver sido a utilizar esta funcionalidade através da pré-visualização, deve garantir que atualizar versões de pré-visualização dos agentes de autenticação utilizando as instruções fornecidas [aqui](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Tem de seguir estas instruções para implementar a autenticação pass-through:

## <a name="step-1-check-prerequisites"></a>Passo 1: Os pré-requisitos de verificação

Certifique-se de que os seguintes pré-requisitos estão assegurados:

### <a name="on-the-azure-active-directory-admin-center"></a>No Centro de administração do Azure Active Directory

1. Crie uma conta de Administrador Global apenas na nuvem no seu inquilino do Azure AD. Desta forma, pode gerir a configuração do seu inquilino devem os serviços no local falhar ou ficar indisponíveis. Saiba mais sobre [adicionar uma conta de Administrador Global apenas na nuvem](../active-directory-users-create-azure-portal.md). Ao executar este passo é fundamental para se certificar de que não fica bloqueado fora do seu inquilino.
2. Adicionar um ou mais [nomes de domínio personalizado](../active-directory-domains-add-azure-portal.md) no seu inquilino do Azure AD. Os utilizadores a iniciar sessão utilizando um destes nomes de domínio.

### <a name="in-your-on-premises-environment"></a>No seu ambiente no local

1. Identifique um servidor com o Windows Server 2012 R2 ou posterior para executar o Azure AD Connect. Adicione o servidor à mesma floresta do AD como os utilizadores cujas palavras-passe tem de ser validada.
2. Instalar o [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado no passo anterior. Se já tiver executado o Azure AD Connect, certifique-se de que a versão está 1.1.644.0 ou posterior.

    >[!NOTE]
    >Azure AD Connect as versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a **sincronização de Hash de palavra-passe**. Se lhe _não_ tencionar utilizar a sincronização de Hash de palavra-passe em conjunto com a autenticação pass-through, leia o [notas de versão do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) para obter mais informações.

3. Identificar um servidor adicional com o Windows Server 2012 R2 ou posterior para executar um agente de autenticação de autónomo. A versão do agente de autenticação tem de ser 1.5.193.0 ou posterior. Este servidor é necessário para garantir uma disponibilidade elevada de pedidos de início de sessão. Adicione o servidor à mesma floresta do AD como os utilizadores cujas palavras-passe tem de ser validada.
4. Se existir uma firewall entre os servidores e do Azure AD, terá de configurar os seguintes itens:
   - Certifique-se de que pode efetuar a autenticação agentes **saída** pedidos para o Azure AD através das seguintes portas:
   
   | Número de porta | Como é utilizado |
   | --- | --- |
   | **80** | Transferência de revogação de certificados as CRLs () ao validar o certificado SSL |
   | **443** | Todas as comunicações de saída com o nosso serviço |
   
   Se a firewall impuser regras de acordo com os utilizadores de origem, abra estas portas para o tráfego dos serviços do Windows que executam o serviço de rede.
   - Se a sua firewall ou proxy permite listas brancas DNS, as ligações da lista branca ao  **\*. msappproxy.net** e  **\*. servicebus.windows.net**. Se não, permitir o acesso ao [intervalos de IP de DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizada semanalmente.
   - Os agentes de autenticação necessitam de aceder a **login.windows.net** e **login.microsoftonline.com** para o registo inicial, por isso, abra a firewall para os URLs bem.
   - Para a validação de certificado, desbloquear os seguintes URLs: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** e  **www.microsoft.com:80**. Estes URLs são utilizados para a validação de certificados com outros produtos Microsoft, pelo que pode já ter estes URLs desbloqueados.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Passo 2: Ativar o suporte do Exchange ActiveSync (opcional)

Siga estas instruções para ativar o suporte do Exchange ActiveSync:

1. Utilize [do Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) para executar o seguinte comando:
```
Get-OrganizationConfig | fl per*
```

2. Verifique o valor da `PerTenantSwitchToESTSEnabled` definição. Se o valor for **verdadeiro**, o seu inquilino está corretamente configurado - isto é, geralmente, as maiúsculas e minúsculas para a maioria dos clientes. Se o valor for **falso**, execute o seguinte comando:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Certifique-se de que o valor da `PerTenantSwitchToESTSEnabled` definição está agora definida como **verdadeiro**. Aguarde uma hora antes de mover para o passo seguinte.

Se enfrentam os problemas durante este passo, consulte a nossa [guia de resolução de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues) para obter mais informações.

## <a name="step-3-enable-the-feature"></a>Passo 3: Ativar a funcionalidade

A autenticação pass-through pode ser ativada utilizando [do Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>A autenticação pass-through pode ser ativada no servidor primário ou de teste do Azure AD Connect. É altamente recomendado ativá-la a partir do servidor principal.

Se estiver a instalar o Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizada](active-directory-aadconnect-get-started-custom.md). Na **sessão do utilizador** página, escolha **autenticação pass-through** como o método de início de sessão. Após a conclusão com êxito, é instalado um agente de autenticação pass-through no mesmo servidor que o Azure AD Connect. Além disso, a funcionalidade de autenticação pass-through está ativada no seu inquilino.

![O Azure AD Connect - sessão do utilizador](./media/active-directory-aadconnect-sso/sso3.png)

Se já tiver instalado o Azure AD Connect (utilizando o [instalação rápida](active-directory-aadconnect-get-started-express.md) ou [instalação personalizada](active-directory-aadconnect-get-started-custom.md) caminho), selecione **alterar utilizador início de sessão** tarefa no Azure AD Ligar e clique em **seguinte**. Em seguida, selecione **autenticação pass-through** como o método de início de sessão. Após a conclusão com êxito, é instalado um agente de autenticação pass-through no mesmo servidor que o Azure AD Connect e a funcionalidade está ativada no seu inquilino.

![O Azure AD Connect - alteração utilizador iniciar sessão](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>A autenticação pass-through é uma funcionalidade de nível de inquilino. Ativá-la tem impacto no início de sessão para os utilizadores em _todos os_ os domínios geridos no seu inquilino. Se estão a mudar de AD FS para autenticação pass-through, recomendamos que aguarde pelo menos de 12 horas antes de encerrar a infraestrutura do AD FS - este tempo de espera é certificar-se de que os utilizadores podem manter iniciar sessão no Exchange ActiveSync durante a transição.

## <a name="step-4-test-the-feature"></a>Passo 4: Testar a funcionalidade

Siga estas instruções para verificar que ativou a autenticação pass-through corretamente:

1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de Administrador Global para o seu inquilino.
2. Selecione **do Azure Active Directory** no painel de navegação esquerdo.
3. Selecione **do Azure AD Connect**.
4. Certifique-se de que o **autenticação pass-through** funcionalidade mostra como **ativado**.
5. Selecione **autenticação pass-through**. Este painel apresenta uma lista de servidores onde estão instalados os agentes de autenticação.

![Centro de administração do Active Directory do Azure - painel do Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centro de administração do Active Directory do Azure – painel de autenticação pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Nesta fase, os utilizadores de todos os domínios geridos no seu inquilino podem iniciar sessão com a autenticação pass-through. No entanto, os utilizadores de domínios federados continuam a iniciar sessão com os serviços de Federação do Active Directory (AD FS) ou de outro fornecedor de federação que configurou anteriormente. Se converter um domínio do federado gerido, todos os utilizadores a partir desse domínio iniciam automaticamente o início de sessão utilizando a autenticação pass-through. Os utilizadores apenas na nuvem não são afetados pela funcionalidade de autenticação pass-through.

## <a name="step-5-ensure-high-availability"></a>Passo 5: Certifique-se de elevada disponibilidade

Se planeia implementar autenticação pass-through num ambiente de produção, deve instalar um agente de autenticação de autónomo. Instalar este agente de autenticação de segundo num servidor _outros_ que a uma execução o Azure AD Connect e o agente de autenticação primeiro. Esta configuração fornece a elevada disponibilidade dos pedidos de início de sessão. Siga estas instruções para implementar um agente de autenticação de autónomo:

1. **Transferir a versão mais recente do agente de autenticação (versões 1.5.193.0 ou posterior)**: a iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com credenciais de Administrador Global do inquilino.
2. Selecione **do Azure Active Directory** no painel de navegação esquerdo.
3. Selecione **do Azure AD Connect** e, em seguida, **autenticação pass-through**. E selecione **Transferir agente**.
4. Clique em de **aceitar os termos & Transferir** botão.
5. **Instale a versão mais recente do agente de autenticação**: executar o executável que transferiu no passo anterior. Forneça as credenciais de Administrador Global do seu inquilino quando lhe for pedido.

![Centro de administração do Active Directory do Azure - botão Transferir o agente de autenticação](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centro de administração do Active Directory do Azure - painel Transferir agente](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Também pode transferir o agente de autenticação do [aqui](https://aka.ms/getauthagent). Certifique-se que reveja e aceite o agente de autenticação [termos de serviço](https://aka.ms/authagenteula) _antes_ instale-o.

## <a name="next-steps"></a>Passos seguintes
- [**Bloqueio do smart** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -capacidade de configurar o bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [**Limitações atuais** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) -saber quais os cenários que são suportados e aqueles que não são.
- [**Descrição detalhada da Technical** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -compreender como funciona esta funcionalidade.
- [**Perguntas mais frequentes** ](active-directory-aadconnect-pass-through-authentication-faq.md) -respostas para as perguntas mais frequentes.
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**Descrição detalhada da segurança** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -particularmente técnicas obter informações adicionais sobre a funcionalidade.
- [**Azure SSO totalmente integrada de AD** ](active-directory-aadconnect-sso.md) -Saiba mais sobre esta funcionalidade complementares.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.

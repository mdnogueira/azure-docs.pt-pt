---
title: "Autenticação de pass-through do Azure AD – introdução | Microsoft Docs"
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
ms.openlocfilehash: 8adca38cc5a783abfe29725dbb0a201de4183dad
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Do Azure autenticação do Active Directory pass-through: Início rápido

## <a name="deploy-azure-ad-pass-through-authentication"></a>Implementar a autenticação do Azure AD pass-through

Autenticação de pass-through do Azure Active Directory (Azure AD) permite aos utilizadores iniciar sessão para aplicações baseadas na nuvem e no local utilizando as mesmas palavras-passe. A autenticação pass-through inicia utilizadores sessão ao validar as palavras-passe diretamente contra do Active Directory no local.

>[!IMPORTANT]
>Se utilizar esta funcionalidade através de uma versão de pré-visualização, certifique-se de que atualiza as versões de pré-visualização dos agentes de autenticação utilizando as instruções fornecidas [autenticação pass-through do Active Directory do Azure: atualização pré-visualização Autenticação agentes](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Siga estas instruções para implementar a autenticação pass-through:

## <a name="step-1-check-the-prerequisites"></a>Passo 1: Verificar os pré-requisitos

Certifique-se de que os seguintes pré-requisitos estão assegurados.

### <a name="in-the-azure-active-directory-admin-center"></a>No Centro de administração do Azure Active Directory

1. Crie uma conta de administrador global apenas na nuvem no seu inquilino do Azure AD. Desta forma, pode gerir a configuração do seu inquilino devem os serviços no local falhar ou ficar indisponíveis. Saiba mais sobre [adicionar uma conta de administrador global apenas na nuvem](../active-directory-users-create-azure-portal.md). A concluir este passo é fundamental para se certificar de que não fica bloqueado fora do seu inquilino.
2. Adicionar um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) no seu inquilino do Azure AD. Os utilizadores podem iniciar sessão com um destes nomes de domínio.

### <a name="in-your-on-premises-environment"></a>No seu ambiente no local

1. Identifique um servidor que executa o Windows Server 2012 R2 ou posterior para executar o Azure AD Connect. Adicione o servidor à mesma floresta do Active Directory como os utilizadores cujas palavras-passe tem de validar.
2. Instalar o [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado no passo anterior. Se já tiver executado o Azure AD Connect, certifique-se de que a versão está 1.1.644.0 ou posterior.

    >[!NOTE]
    >Azure AD Connect as versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização de hash de palavra-passe. Se lhe _não_ tencionar utilizar a sincronização de hash de palavra-passe em conjunto com a autenticação pass-through, leia o [notas de versão do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Identificar um servidor adicional (com o Windows Server 2012 R2 ou posterior) onde pode executar um agente de autenticação de autónomo. A versão do agente de autenticação tem de ser 1.5.193.0 ou posterior. Este servidor adicional é necessário para garantir a disponibilidade elevada de pedidos para iniciar sessão. Adicione o servidor à mesma floresta do Active Directory como os utilizadores cujas palavras-passe tem de validar.
4. Se existir uma firewall entre os servidores e do Azure AD, configure os seguintes itens:
   - Certifique-se de que pode efetuar a autenticação agentes *saída* pedidos para o Azure AD através das seguintes portas:
   
    | Número de porta | Como é utilizado |
    | --- | --- |
    | **80** | Transfere as listas de revogação de certificados (CRL) ao validar o certificado SSL |
    | **443** | Processa todas as comunicações de saída com o serviço |
   
    Se a firewall impuser regras de acordo com os utilizadores de origem, abra estas portas para o tráfego dos serviços do Windows que são executados como um serviço de rede.
   - Se a sua firewall ou proxy permite listas brancas DNS, as ligações da lista branca ao  **\*. msappproxy.net** e  **\*. servicebus.windows.net**. Se não, permitir o acesso à [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizada semanalmente.
   - Os agentes de autenticação necessitam de aceder a **login.windows.net** e **login.microsoftonline.com** para o registo inicial. Abra a firewall para os URLs bem.
   - Para a validação de certificado, desbloquear os seguintes URLs: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, e  **www.microsoft.com:80**. Estes URLs são utilizados para validação de certificados com outros produtos Microsoft. Já poderá ter estes URLs desbloqueados.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Passo 2: Ativar o suporte do Exchange ActiveSync (opcional)

Siga estas instruções para ativar o suporte do Exchange ActiveSync:

1. Utilize [do Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) para executar o seguinte comando:
```
Get-OrganizationConfig | fl per*
```

2. Verifique o valor da `PerTenantSwitchToESTSEnabled` definição. Se o valor for **verdadeiro**, seu inquilino está corretamente configurado. Isto é, geralmente, as maiúsculas e minúsculas para a maioria dos clientes. Se o valor for **falso**, execute o seguinte comando:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Certifique-se de que o valor da `PerTenantSwitchToESTSEnabled` definição está agora definida como **verdadeiro**. Aguarde uma hora antes de passar para o passo seguinte.

Se enfrentam quaisquer problemas durante este passo, verifique o [guia de resolução de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>Passo 3: Ativar a funcionalidade

Ativar a autenticação pass-through através de [do Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Pode ativar a autenticação pass-through no servidor primário ou de teste do Azure AD Connect. Deve ativá-la do servidor primário.

Se estiver a instalar o Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizada](active-directory-aadconnect-get-started-custom.md). Na **sessão do utilizador** página, escolha **autenticação pass-through** como o **método de início de sessão**. Após a conclusão com êxito, um agente de autenticação pass-through está instalado no mesmo servidor que o Azure AD Connect. Além disso, a funcionalidade de autenticação pass-through está ativada no seu inquilino.

![O Azure AD Connect: Utilizador iniciar sessão](./media/active-directory-aadconnect-sso/sso3.png)

Se já tiver instalado o Azure AD Connect utilizando o [instalação rápida](active-directory-aadconnect-get-started-express.md) ou o [instalação personalizada](active-directory-aadconnect-get-started-custom.md) caminho, selecione o **alterar utilizador início de sessão** tarefa no Azure AD Ligar e, em seguida, selecione **seguinte**. Em seguida, selecione **autenticação pass-through** como método de início de sessão. Após a conclusão com êxito, um agente de autenticação pass-through está instalado no mesmo servidor que o Azure AD Connect e a funcionalidade está ativada no seu inquilino.

![O Azure AD Connect: Alterar a sessão do utilizador](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>A autenticação pass-through é uma funcionalidade de nível de inquilino. Ativá-la afeta o início de sessão para os utilizadores em _todos os_ os domínios geridos no seu inquilino. Se está a mudar de serviços de Federação do Active Directory (AD FS) para autenticação pass-through, deve aguardar pelo menos de 12 horas antes de encerrar a infraestrutura do AD FS. Este tempo de espera é garantir que os utilizadores podem manter iniciar sessão no Exchange ActiveSync durante a transição.

## <a name="step-4-test-the-feature"></a>Passo 4: Testar a funcionalidade

Siga estas instruções para verificar que ativou a autenticação pass-through corretamente:

1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global para o seu inquilino.
2. Selecione **do Azure Active Directory** no painel esquerdo.
3. Selecione **do Azure AD Connect**.
4. Certifique-se de que o **autenticação pass-through** funcionalidade aparece como **ativado**.
5. Selecione **autenticação pass-through**. O **autenticação pass-through** painel apresenta uma lista de servidores onde estão instalados os agentes de autenticação.

![Centro de administração do Active Directory do Azure: painel do Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centro de administração do Active Directory do Azure: painel autenticação pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Nesta fase, os utilizadores de todos os domínios geridos no seu inquilino podem iniciar sessão utilizando a autenticação pass-through. No entanto, os utilizadores de domínios federados continuam a sessão utilizando o AD FS ou outro fornecedor de federação que configurou anteriormente. Se converter um domínio do federado gerido, todos os utilizadores a partir desse domínio iniciam automaticamente o início de sessão utilizando a autenticação pass-through. A funcionalidade de autenticação pass-through não afeta os utilizadores apenas na nuvem.

## <a name="step-5-ensure-high-availability"></a>Passo 5: Certifique-se de elevada disponibilidade

Se planeia implementar autenticação pass-through num ambiente de produção, deve instalar um agente de autenticação de autónomo. Instalar este agente de autenticação de segundo num servidor _outros_ que a uma execução o Azure AD Connect e o agente de autenticação primeiro. Esta configuração fornece elevada disponibilidade para pedidos iniciar sessão. Siga estas instruções para implementar um agente de autenticação de autónomo:

1. Transferir a versão mais recente do agente de autenticação (versão 1.5.193.0 ou posterior). Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com credenciais de administrador global do inquilino.
2. Selecione **do Azure Active Directory** no painel esquerdo.
3. Selecione **do Azure AD Connect**, selecione **autenticação pass-through**e, em seguida, selecione **Transferir agente**.
4. Selecione o **aceitar os termos & Transferir** botão.
5. Instale a versão mais recente do agente de autenticação ao executar o executável que foi transferido no passo anterior. Forneça as credenciais de administrador global do seu inquilino quando lhe for pedido.

![Centro de administração do Active Directory do Azure: botão Transferir o agente de autenticação](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centro de administração do Active Directory do Azure: painel Transferir agente](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Também pode transferir o [agente de autenticação do Azure Active Directory](https://aka.ms/getauthagent). Certifique-se que reveja e aceite o agente de autenticação [termos de serviço](https://aka.ms/authagenteula) _antes_ instale-o.

## <a name="next-steps"></a>Passos seguintes
- [Bloqueio do smart](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Saiba como configurar a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Limitações atuais](active-directory-aadconnect-pass-through-authentication-current-limitations.md): saber que cenários são suportados com a autenticação pass-through e aqueles que não são.
- [Descrição detalhada da Technical](active-directory-aadconnect-pass-through-authentication-how-it-works.md): compreender como funciona a funcionalidade de autenticação pass-through.
- [Perguntas mais frequentes](active-directory-aadconnect-pass-through-authentication-faq.md): encontre respostas a perguntas mais frequentes.
- [Resolver problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [Descrição detalhada da segurança](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): Obtenha informações técnicas sobre a funcionalidade de autenticação pass-through.
- [Azure SSO totalmente integrada de AD](active-directory-aadconnect-sso.md): saber mais sobre esta funcionalidade complementares.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para novos pedidos de funcionalidade de ficheiros.


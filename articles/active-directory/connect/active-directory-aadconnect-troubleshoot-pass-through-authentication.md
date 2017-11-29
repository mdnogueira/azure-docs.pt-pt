---
title: "Azure AD Connect: Resolver problemas de autenticação pass-through | Microsoft Docs"
description: "Este artigo descreve como resolver problemas de autenticação de pass-through do Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Resolver problemas de autenticação de pass-through ligar do Azure AD, instalar o Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: 730dfcb8925a1a7edf8796ddaf86d2038fd9df44
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Resolver problemas de autenticação de pass-through do Azure Active Directory

Este artigo ajuda-o a encontrar informações sobre problemas comuns de autenticação pass-through do Azure AD de resolução de problemas.

>[!IMPORTANT]
>Se estão a enfrentar problemas início de sessão do utilizador com a autenticação pass-through, não desative a funcionalidade ou desinstalar agentes de autenticação pass-through sem ter uma conta de Administrador Global apenas na nuvem para contingência. Saiba mais sobre [adicionar uma conta de Administrador Global apenas na nuvem](../active-directory-users-create-azure-portal.md). Efetuar este passo é crucial e assegura que não fica bloqueado fora do seu inquilino.

## <a name="general-issues"></a>Problemas gerais

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Verifique o estado da funcionalidade e os agentes de autenticação

Certifique-se de que a funcionalidade de autenticação pass-through ainda é **ativado** no seu inquilino e o estado dos agentes de autenticação mostra **Active Directory**e não **inativo**. Pode verificar o estado, acedendo ao **do Azure AD Connect** painel o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Active Directory do Azure - painel do Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centro de administração do Active Directory do Azure – painel de autenticação pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Mensagens de início de sessão destinada ao utilizador-erro

Se o utilizador não é possível iniciar sessão em utilizar a autenticação pass-through, poderá ver um dos seguintes erros de destinada ao utilizador o ecrã de início de sessão do Azure AD: 

|Erro|Descrição|Resolução
| --- | --- | ---
|AADSTS80001|Não é possível ligar ao Active Directory|Certifique-se de que os servidores de agente são membros da mesma floresta do AD que os utilizadores cujas palavras-passe tem de ser validada e são capazes de estabelecer ligação ao Active Directory.  
|AADSTS8002|Limite de tempo excedido ao ligar ao Active Directory|Certifique-se de garantir que o Active Directory está disponível e está a responder aos pedidos dos agentes.
|AADSTS80004|O nome de utilizador foi transmitido para o agente não era válido|Certifique-se de que o utilizador está a tentar iniciar sessão com o nome de utilizador adequada.
|AADSTS80005|Validação encontrou WebException imprevisível|Um erro transitório. Repita o pedido. Se continuar a falhar, contacte o suporte da Microsoft.
|AADSTS80007|Ocorreu um erro ao comunicar com o Active Directory|Verifique os registos de agente para obter mais informações e certifique-se de que do Active Directory está a funcionar conforme esperado.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Motivos da falha de início de sessão no Centro de administração do Azure Active Directory (necessita de licença Premium)

Se o seu inquilino tem uma licença do Azure AD Premium associada à mesma, pode também ver o [relatório de atividade de início de sessão](../active-directory-reporting-activity-sign-ins.md) no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Active Directory do Azure - relatório de inícios de sessão](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

Navegue para **do Azure Active Directory** -> **inícios de sessão** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) e clique em atividade de início de sessão de um utilizador específico. Procure o **código de erro de início de sessão em** campo. Mapear o valor desse campo um motivo da falha e a resolução utilizando a seguinte tabela:

|Código de erro de início de sessão|Motivo da falha de início de sessão|Resolução
| --- | --- | ---
| 50144 | A palavra-passe do Active Directory do utilizador expirou. | Repor palavra-passe o utilizador no Active Directory no local.
| 80001 | Não existe nenhum Agente de Autenticação disponível. | Instale e registe um agente de autenticação.
| 80002 | O pedido de validação da palavra-passe do Agente de Autenticação atingiu o tempo limite. | Verifique se o Active Directory está acessível a partir do agente de autenticação.
| 80003 | O Agente de Autenticação recebeu uma resposta inválida. | Se o problema consistentemente reproduzível entre vários utilizadores, verifique a configuração do Active Directory.
| 80004 | Foi utilizado um Nome Principal de Utilizador (UPN) no pedido de início de sessão. | Pedir ao utilizador para iniciar sessão com o nome de utilizador correto.
| 80005 | Agente de Autenticação: ocorreu um erro. | Erro transitório. Tente novamente mais tarde.
| 80007 | O Agente de Autenticação não se consegue ligar ao Active Directory. | Verifique se o Active Directory está acessível a partir do agente de autenticação.
| 80010 | O Agente de Autenticação não conseguiu desencriptar a palavra-passe. | Se o problema consistentemente reproduzível, instalar e registar um novo agente de autenticação. E a desinstalação dos atual. 
| 80011 | O Agente de Autenticação não conseguiu obter a chave de desencriptação. | Se o problema consistentemente reproduzível, instalar e registar um novo agente de autenticação. E a desinstalação dos atual.

## <a name="authentication-agent-installation-issues"></a>Problemas de instalação do agente de autenticação

### <a name="an-unexpected-error-occurred"></a>Ocorreu um erro inesperado

[Recolher registos de agente](#collecting-pass-through-authentication-agent-logs) do servidor e contacte Support da Microsoft com o seu problema.

## <a name="authentication-agent-registration-issues"></a>Problemas de registo do agente de autenticação

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registo do agente de autenticação falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual tenha sido instalado o agente de autenticação pode comunicar com o nosso serviço URLs e portas listadas [aqui](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Falha do registo do agente de autenticação devido a erros de autorização de conta ou token

Certifique-se de que utiliza uma conta de Administrador Global apenas na nuvem para a instalação do agente de autenticação autónomo ou do Azure AD Connect e operações de registo. Há um problema conhecido com contas de Administrador Global ativar a MFA Desative a MFA temporariamente (apenas para concluir as operações) como uma solução.

### <a name="an-unexpected-error-occurred"></a>Ocorreu um erro inesperado

[Recolher registos de agente](#collecting-pass-through-authentication-agent-logs) do servidor e contacte Support da Microsoft com o seu problema.

## <a name="authentication-agent-uninstallation-issues"></a>Problemas de desinstalação do agente de autenticação

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Mensagem de aviso quando a desinstalação do Azure AD Connect

Se tiver a autenticação pass-through activada no seu inquilino e tentar desinstalar o Azure AD Connect, mostra-lhe a seguinte mensagem de aviso: "Os utilizadores não será capazes de início de sessão para o Azure AD, salvo se tiver outros agentes de autenticação pass-through instalados noutros servidores."

Certifique-se de que a configuração [elevada disponível](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) antes de desinstalar o Azure AD Connect, para evitar danificando a sessão do utilizador.

## <a name="issues-with-enabling-the-feature"></a>Problemas com a ativação da funcionalidade

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Ativar a funcionalidade falhou porque não existiam sem agentes de autenticação disponíveis

Tem de ter, pelo menos, um agente de autenticação de Active Directory para ativar a autenticação pass-through no seu inquilino. Pode instalar um agente de autenticação por instalar o Azure AD Connect ou autónomo agente de autenticação.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Ativar a funcionalidade falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual está instalado o Azure AD Connect pode comunicar com o nosso serviço URLs e portas listadas [aqui](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Ativar a funcionalidade falhou devido a erros de autorização de conta ou token

Certifique-se de que utilize uma conta de Administrador Global apenas na nuvem quando ativar a funcionalidade. Há um problema conhecido com a autenticação multifator (MFA)-ativado contas de Administrador Global Desative a MFA temporariamente (apenas para concluir a operação) como uma solução.

## <a name="exchange-activesync-configuration-issues"></a>Problemas de configuração do Exchange ActiveSync

Estes são os problemas comuns quando configurar o suporte do Exchange ActiveSync para a autenticação pass-through.

### <a name="exchange-powershell-issue"></a>Problema do PowerShell do Exchange

Se vir a "**não é possível encontrar um parâmetro que corresponda ao nome do parâmetro 'PerTenantSwitchToESTSEnabled'\.**" Ocorreu um erro ao executar o `Set-OrganizationConfig` do Exchange PowerShell comando, contacte Support da Microsoft.

### <a name="exchange-activesync-not-working"></a>Exchange ActiveSync não está a funcionar

A configuração demora algum tempo a serem aplicadas - o período de tempo depende do seu ambiente. Se a situação persistir durante muito tempo, contacte Support da Microsoft.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Recolher registos de agente de autenticação pass-through

Dependendo do tipo de problema que pode ter, tem de ter um aspeto em locais diferentes para os registos do agente de autenticação pass-through.

### <a name="azure-ad-connect-logs"></a>Os registos do Azure AD Connect

Para erros relacionados com a instalação, consulte os registos do Azure AD Connect em **%ProgramData%\AADConnect\trace-\*. log**.

### <a name="authentication-agent-event-logs"></a>Registos de eventos do agente de autenticação

Erros relacionados com o agente de autenticação, abra a aplicação do Visualizador de eventos no servidor e verifique em **aplicações e serviço Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Para a análise de detalhado, ative o registo da "Sessão". Não execute o agente de autenticação com este registo ativado durante as operações normais; Utilize apenas para a resolução de problemas. Os conteúdos dos registos apenas são visíveis após o registo está desativado novamente.

### <a name="detailed-trace-logs"></a>Registos de rastreio de detalhado

Para resolver falhas de início de sessão de utilizador, procure os registos de rastreio no **%ProgramData%\Microsoft\Azure AD Connect Agent\Trace de autenticação\\**. Estes registos incluem motivos por que motivo um utilizador específico início de sessão falhou ao utilizar a funcionalidade de autenticação pass-through. Estes erros também estão mapeados para os motivos da falha de início de sessão mostrados na precedente [tabela](#sign-in-failure-reasons-on-the-Azure-portal). Segue-se uma entrada de registo de exemplo:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Pode obter descritivos detalhes do erro ('1328' no exemplo anterior), ao abrir a linha de comandos e executar o seguinte comando (Nota: substitua '1328' com o número de erro real que ver nos seus registos):

`Net helpmsg 1328`

![Autenticação pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Registos de controlador de domínio

Se um registo de auditoria estiver ativado, pode encontrar informações adicionais nos registos de segurança dos seus controladores de domínio. Uma forma simples de consultar os pedidos de início de sessão enviados por agentes de autenticação pass-through é o seguinte:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

### <a name="performance-monitor-counters"></a>Contadores de Monitor de desempenho

Outra forma para monitorizar os agentes de autenticação é para controlar os contadores de Monitor de desempenho específicos em cada servidor onde está instalado o agente de autenticação. Utilize os seguintes contadores Global (**autenticações # PTA**, **#PTA falha autenticações** e **autenticações efetuadas com êxito de #PTA**) e os contadores de erro (**Erros de autenticação de # PTA**):

![Contadores de Monitor de desempenho de autenticação pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>A autenticação pass-through fornece elevada disponibilidade utilizando múltiplos de autenticação de agentes e _não_ balanceamento de carga. Consoante a configuração, _não_ todos os agentes de autenticação receber aproximadamente _igual_ número de pedidos. É possível que um agente de autenticação específicas não recebe nenhum tráfego em todos os.

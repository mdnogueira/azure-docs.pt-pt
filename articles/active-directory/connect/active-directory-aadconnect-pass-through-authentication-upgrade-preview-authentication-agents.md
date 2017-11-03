---
title: "Do Azure AD Connect: A autenticação pass-through - agentes de autenticação de atualização pré-visualização | Microsoft Docs"
description: "Este artigo descreve como atualizar a configuração de autenticação de pass-through do Azure Active Directory (Azure AD)."
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
ms.date: 08/04/2017
ms.author: billmath
ms.openlocfilehash: 0a7293f2b3a366b25e780ee75601dfbb2b35ddaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Do Azure autenticação do Active Directory pass-through: Os agentes de autenticação de atualização pré-visualização

## <a name="overview"></a>Descrição geral

Este artigo destina-se a clientes que utilizam autenticação pass-through do Azure AD através da pré-visualização. Foi recentemente atualizado (e rebranded) o software do agente de autenticação. Terá de _manualmente_ pré-visualização atualizar agentes de autenticação instalados em servidores no local. Esta atualização manual é apenas uma única ação. Todas as atualizações futuras para agentes de autenticação são automáticas. As razões para atualizar são os seguintes:

- As versões de pré-visualização de agentes de autenticação não receberão qualquer adicional de segurança ou as correções de erros.
-   As versões de pré-visualização de agentes de autenticação não podem ser instaladas nos servidores adicionais, de elevada disponibilidade.

## <a name="check-versions-of-your-authentication-agents"></a>Verifique as versões dos seus agentes de autenticação

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Passo 1: Verificar onde estão instalados os agentes de autenticação

Siga estes passos para verificar a instalar os agentes de autenticação:

1. Iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de Administrador Global para o seu inquilino.
2. Selecione **do Azure Active Directory** no painel de navegação esquerdo.
3. Selecione **do Azure AD Connect**. 
4. Selecione **autenticação pass-through**. Este painel apresenta uma lista de servidores onde estão instalados os agentes de autenticação.

![Centro de administração do Active Directory do Azure – painel de autenticação pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Passo 2: Verificar as versões dos seus agentes de autenticação

Para verificar as versões dos seus agentes de autenticação, em cada servidor identificado no passo anterior, siga estas instruções:

1. Aceda a **painel de controlo -> programas -> programas e funcionalidades** no servidor no local.
2. Se existir uma entrada para "**agente do Microsoft Azure AD Connect autenticação**", não precisa de efetuar qualquer ação neste servidor.
3. Se existir uma entrada para "**conector do Proxy de aplicações do Microsoft Azure AD**", versões 1.5.132.0 ou anterior, terá de atualizar manualmente neste servidor.

![Versão de pré-visualização do agente de autenticação](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Procedimentos recomendados para antes de iniciar a atualização

Antes de atualizar, certifique-se de que tem os seguintes itens no local:

1. **Criar conta de Administrador Global apenas na nuvem**: não atualizar sem ter uma conta de Administrador Global apenas na nuvem para utilizar em emergência situações em que os agentes de autenticação pass-through não estiverem a funcionar corretamente. Saiba mais sobre [adicionar uma conta de Administrador Global apenas na nuvem](../active-directory-users-create-azure-portal.md). Efetuar este passo é crucial e assegura que não fica bloqueado fora do seu inquilino.
2.  **Certifique-se de elevada disponibilidade**: Se não foi concluído anteriormente, instalar uma segunda autónoma agente de autenticação para fornecer elevada disponibilidade para pedidos de início de sessão, a utilizar estas [instruções](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Atualizar o agente de autenticação no seu servidor do Azure AD Connect

Tem de atualizar o Azure AD Connect antes de atualizar o agente de autenticação no mesmo servidor. Siga estes passos no servidor primário e servidores do Azure AD Connect de teste:

1. **Atualizar o Azure AD Connect**: Siga esta [artigo](./active-directory-aadconnect-upgrade-previous-version.md) e atualize para a versão mais recente do Azure AD Connect.
2. **Desinstale a versão de pré-visualização do agente de autenticação**: transferir [este script do PowerShell](https://aka.ms/rmpreviewagent) e executá-lo como um administrador no servidor.
3. **Transferir a versão mais recente do agente de autenticação (versões 1.5.193.0 ou posterior)**: a iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com credenciais de Administrador Global do inquilino. Selecione **do Azure Active Directory -> do Azure AD Connect -> autenticação pass-through -> agente de transferência**. Aceitar o [termos de serviço](https://aka.ms/authagenteula) e transferir a versão mais recente do agente de autenticação. Também pode transferir o agente de autenticação do [aqui](https://aka.ms/getauthagent).
4. **Instale a versão mais recente do agente de autenticação**: executar o executável que transferiu no passo 3. Forneça as credenciais de Administrador Global do seu inquilino quando lhe for pedido.
5. **Certifique-se de que a versão mais recente foi instalada**: conforme mostrado anteriormente, aceda a **painel de controlo -> programas -> programas e funcionalidades** e verifique se existe uma entrada para "**agente do Microsoft Azure AD Connect autenticação**".

>[!NOTE]
>Se verifique o painel de autenticação pass-through no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) depois de concluir precedente passos, irá ver duas entradas de agente de autenticação por servidor - uma entrada que mostra o agente de autenticação como **Active Directory** e os outros como **inativo**. Este é _esperado_. O **inativo** entrada é automaticamente removida após alguns dias.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Atualizar o agente de autenticação noutros servidores

Siga estes passos para atualizar agentes de autenticação noutros servidores (onde o Azure AD Connect não está instalado):

1. **Desinstale a versão de pré-visualização do agente de autenticação**: transferir [este script do PowerShell](https://aka.ms/rmpreviewagent) e executá-lo como um administrador no servidor.
2. **Transferir a versão mais recente do agente de autenticação (versões 1.5.193.0 ou posterior)**: a iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com credenciais de Administrador Global do inquilino. Selecione **do Azure Active Directory -> do Azure AD Connect -> autenticação pass-through -> agente de transferência**. Aceitar os termos de serviço e transferir a versão mais recente.
3. **Instale a versão mais recente do agente de autenticação**: executar o executável que transferiu no passo 2. Forneça as credenciais de Administrador Global do seu inquilino quando lhe for pedido.
4. **Certifique-se de que a versão mais recente foi instalada**: conforme mostrado anteriormente, aceda a **painel de controlo -> programas -> programas e funcionalidades** e verifique se existe uma entrada chamada **agente do Microsoft Azure AD Connect autenticação**.

>[!NOTE]
>Se verifique o painel de autenticação pass-through no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) depois de concluir precedente passos, irá ver duas entradas de agente de autenticação por servidor - uma entrada que mostra o agente de autenticação como **Active Directory** e os outros como **inativo**. Este é _esperado_. O **inativo** entrada é automaticamente removida após alguns dias.

## <a name="next-steps"></a>Passos seguintes
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) -Saiba como resolver problemas comuns com a funcionalidade.

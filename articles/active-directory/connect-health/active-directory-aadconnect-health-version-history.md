---
title: "Histórico das Versões do Azure AD Connect Health"
description: "Este documento descreve as versões para o Azure AD Connect Health e que foi incluído nessas versões."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6c990a184d44771c78330f54f518bb4c35a36a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Histórico do Lançamento de Versões
A equipa do Azure Active Directory atualiza regularmente o Azure AD Connect Health com novas funcionalidades e funções. Este artigo apresenta as versões e funcionalidades que tenham sido publicadas.

## <a name="october-2016"></a>Outubro de 2016
**Atualização do agente:**

* O agente do Azure AD Connect Health para AD FS \(versão 2.6.408.0\)
  1. Melhoramentos na deteção de endereços IP do cliente em pedidos de autenticação
  2. Correções de erros relacionados com alertas
* Agente do Azure AD Connect Health para AD DS (versão 2.6.408.0)
  1. Correções de erros relacionados com alertas.
* Agente do Azure AD Connect Health para sincronização (versão 2.6.353.0) lançada com o Azure AD Connect versão 1.1.281.0
  1. Fornecer os dados necessários para os relatórios de erros de sincronização
  2. Correções de erros relacionados com alertas

**Novas funcionalidades de pré-visualização:**

* Relatórios de erros de sincronização do Azure AD Connect

**Novas funcionalidades:**

* Azure AD Connect Health para AD FS - campo do endereço IP está disponível no relatório sobre os principais 50 utilizadores com o nome de utilizador/palavra-passe incorreta.

## <a name="july-2016"></a>Julho de 2016
**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para AD DS](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>Janeiro de 2016
**Atualização do agente:**

* Agente do Azure AD Connect Health para AD FS (versão 2.6.91.1512)

**Novas funcionalidades:**

* [Ferramenta de teste de conectividade para o Azure AD Connect agentes de estado de funcionamento](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembro de 2015
**Novas funcionalidades:**

* Suporte para [controlo de acesso baseado em funções](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md).

**Fixos problemas:**

* Correções de erros para erros encontrados durante registos do agente.

## <a name="september-2015"></a>Setembro de 2015
**Novas funcionalidades:**

* Errado relatório de palavra-passe do nome de utilizador para o AD FS
* Suporte para configurar o Proxy de HTTP não autenticado
* Suporte para configurar o agente no Server core
* Melhoramentos para alertas para o AD FS
* Melhoramentos no agente do Azure AD Connect Health para AD FS para dados e conectividade carregar.

**Fixos problemas:**

* Correções de erros nas informações sobre a utilização de tipos de erro do AD FS.

## <a name="june-2015"></a>Junho de 2015
**Versão inicial do Azure AD Connect Health para AD FS e Proxy do AD FS.**

**Novas funcionalidades:**

* Alertas de monitorização servidores do AD FS e Proxy do AD FS com notificações por e-mail.
* Acesso fácil a topologia do AD FS e padrões de contadores de desempenho do AD FS.
* Tendência de pedidos de token com êxito nos servidores do AD FS, agrupados por aplicações, métodos de autenticação, etc de localização de rede do pedido.
* Tendências de pedido falhado nos servidores do AD FS, agrupados por aplicações, etc. tipos de erro.
* Implementação do agente mais simples utilizar credenciais de Administrador Global do AD do Azure.  

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [monitorizar os serviços de infraestrutura e a sincronização de identidade de no local na nuvem](active-directory-aadconnect-health.md).


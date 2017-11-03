---
title: "Atualizar para o Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Escolha que solução de proxy é melhor se estiver a atualizar a partir do Microsoft Forefront ou Unified Gateway de acesso."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 6c9f70493155de6989b26fd4e8bcf1dff01c835c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="compare-remote-access-solutions"></a>Comparar soluções de acesso remoto

Proxy de aplicações do Active Directory do Azure é uma das duas soluções de acesso remoto que a Microsoft oferece. O outro é o Proxy de aplicações Web, a versão no local. Estas duas soluções de substituem produtos anteriores que Microsoft oferecido: Microsoft Forefront Threat Management Gateway (TMG) e o Gateway de acesso unificado (UAG). Utilize este artigo para compreender a forma como estas quatro soluções comparam entre si. Os de que ainda a utilizar as soluções TMG ou UAG preteridas, utilize este artigo para o ajudar a planear a migração para um Proxy de aplicações. 


## <a name="feature-comparison"></a>Comparação de funcionalidades

Utilize esta tabela para compreender como comparam Threat Management Gateway (TMG), o Gateway de acesso unificado (UAG), o Proxy de aplicações Web (WAP) e o Proxy de aplicações do Azure AD (AP) entre si.

| Funcionalidade | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticação de certificados | Sim | Sim | - | - |
| Publicar seletivamente as aplicações de browser | Sim | Sim | Sim | Sim |
| Pré-autenticação e único início de sessão | Sim | Sim | Sim | Sim | 
| Camada 2/3 firewall | Sim | Sim | - | - |
| Reencaminhar capacidades de proxy | Sim | - | - | - |
| Capacidades VPN | Sim | Sim | - | - |
| Suporte de protocolos avançado | - | Sim | Sim, se executar através de HTTP | Sim, se executar através de HTTP ou através do Gateway de ambiente de trabalho remoto |
| Funciona como servidor de proxy do AD FS | - | Sim | Sim | - |
| Um portal de acesso à aplicação | - | Sim | - | Sim |
| Tradução de ligação de corpo de resposta | Sim | Sim | - | Sim | 
| Autenticação com cabeçalhos | - | Sim | - | Sim, com PingAccess | 
| Segurança de escala da nuvem | - | - | - | Sim | 
| Acesso condicional | - | Sim | - | Sim |
| Não existem componentes na zona desmilitarizada (DMZ) | - | - | - | Sim |
| Não existem ligações de entrada | - | - | - | Sim |

Para a maioria dos cenários, recomendamos que aplicação Azure AD como a solução moderna. Proxy de aplicações Web é apenas preferenciais em cenários que necessitam de um servidor proxy para o AD FS e não é possível utilizar domínios personalizados no Azure Active Directory. 

Proxy de aplicações do Azure AD oferece vantagens únicas quando comparado com produtos semelhantes, incluindo:

- Expandir o Azure AD para recursos no local
   - Proteção e segurança de escala da nuvem
   - As funcionalidades, como o acesso condicional e multi-factor Authentication são fáceis de ativar
- Não existem componenet na zona desmilitarizada
- Não existem ligações de entrada necessárias
- Painel de um acesso que os utilizadores podem aceder a para todas as respetivas aplicações, incluindo o Office 365, Azure AD integradas aplicações SaaS, e no local aplicações web. 


## <a name="next-steps"></a>Passos seguintes

- [Utilize a aplicação Azure AD para fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md)
- [Transição do Forefront TMG e UAG para o Proxy de aplicações](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

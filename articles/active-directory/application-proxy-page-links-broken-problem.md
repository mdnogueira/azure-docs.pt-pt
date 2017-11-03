---
title: "A página não funciona para uma aplicação de Proxy de aplicações | Microsoft Docs"
description: "Como resolver problemas com ligações quebrados em aplicações de Proxy de aplicações que tem de ser integrado com o Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 83c4261fab0498541591c01f9bb692b396c7b751
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>A página não funciona para uma aplicação de Proxy de aplicações

Este artigo ajudá-lo a resolver problemas com a razão pela qual as ligações na sua aplicação de Proxy de aplicações do Azure Active Directory não funcionam corretamente.

## <a name="overview"></a>Descrição geral 
Depois de publicar uma aplicação de Proxy de aplicações, as ligações só funcionam por predefinição na aplicação estão ligações para destinos contidos o URL de raiz publicada. As ligações a aplicações não estão a funcionar, o URL interno para a aplicação, provavelmente, não inclui todos os destinos de hiperligações na aplicação.

**Por que motivo isto acontecer?** Quando clica numa ligação numa aplicação, o Proxy de aplicações tenta resolver o URL como um URL interno dentro da mesma aplicação, ou como um URL disponível externamente. Se a ligação a apontar para um URL interno que não se encontra dentro da mesma aplicação, não pertence a qualquer um destes registos e resultar num erro de não foi encontrado.

## <a name="ways-you-can-resolve-broken-links"></a>Formas que pode resolver quebrada ligações

Existem três formas para resolver este problema. As opções abaixo no constam aumentar complexidade.

1.  Certifique-se o URL interno uma raiz que contém todas as ligações para a aplicação. Isto permite que todas as ligações que seja resolvido como conteúdo publicado na mesma aplicação.

    Se alterar o URL interno, mas não pretenda alterar a página de destino para os utilizadores, altere o URL de página inicial para o URL interno publicado anteriormente. Isto pode ser feito por que "Do Azure Active Directory" -&gt; registos de aplicações -&gt; selecione a aplicação -&gt; propriedades. Neste separador de propriedades, consulte o campo "Home Page do URL", pode ajustar a ser a página de destino pretendida.

2.  Se as suas aplicações utilizam nomes de domínio completamente qualificado (FQDN), utilize [domínios personalizados](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) para publicar as suas aplicações. Esta funcionalidade permite que o mesmo URL a utilizar o internamente e externamente.

    Esta opção assegura que as ligações na sua aplicação são externamente acessíveis através do Proxy de aplicação, uma vez que as ligações na aplicação para os URLs internos também são reconhecidas externamente. Tenha em atenção que todas as ligações ainda tem de pertencer a uma aplicação publicada. No entanto, com esta opção as ligações não têm de pertencer à mesma aplicação e podem pertencer a várias aplicações.

3.  Se nenhuma destas opções exequível, associe a pré-visualização para uma nova funcionalidade que efetuar a conversão/tradução de URL. Com esta opção, interno URLs ou ligações que existem no corpo HTML das suas aplicações ser traduzidas, ou "mapeadas", para os URLs de Proxy da aplicação externo publicada. Isto funciona apenas para ligações no HTML ou CSS e esta ajuda se a ligação é gerada através de JS. 

Como resultado, recomendamos vivamente a utilizar o [domínios personalizados](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) solução se possível. Se pretende associar a pré-visualização, e-mail < aadapfeedback@microsoft.com > com o applicationId(s).

## <a name="next-steps"></a>Passos seguintes
[Trabalhar com servidores de proxy no local existentes](application-proxy-working-with-proxy-servers.md)


---
title: "Localizar aplicações da nuvem não geridos com o Cloud App Discovery no Azure Active Directory | Microsoft Docs"
description: "Fornece informações sobre como localizar e gerir aplicações com o Cloud App Discovery, quais são as vantagens e como funciona."
services: active-directory
keywords: "o cloud app discovery, gestão de aplicações"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 35b898aa3c03aeef914a7df574ac65a22a6c7bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Localizar aplicações da nuvem não geridos com o Cloud App Discovery
## <a name="summary"></a>Resumo

O cloud App Discovery é uma funcionalidade do Azure Active Directory Premium que permite-lhe detetar aplicações na nuvem não geridos que está a ser utilizadas por pessoas na sua organização. Empresas moderna, departamentos de TI, normalmente, não têm conhecimento de todas as aplicações em nuvem que utilizam os membros da sua organização para trabalhar. É fácil ver a razão pela qual os administradores teria preocupações acesso não autorizado a dados empresariais, fugas de dados e outros riscos de segurança. Esta falta de deteção pode efetuar a criação de um plano para lidar com estes riscos de segurança aparentemente, uma tarefa intimidante.

> [!TIP] 
> Veja os melhoramentos ao Cloud App Discovery no Azure Active Directory (Azure AD), que são melhorados por [integração com o Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

**Com o Cloud App Discovery, pode:**

* Localize as aplicações em nuvem que está a ser utilizadas e medida que a utilização de pelo número de utilizadores, volume de tráfego ou número de pedidos da web para a aplicação.
* Identifique os utilizadores que estão a utilizar uma aplicação.
* Exporte dados para análise offline.
* Coloque estas aplicações sob controlo de TI e ativar o início de sessão para gestão de utilizadores.

## <a name="how-it-works"></a>Como funciona
1. Se encontram instalados agentes de utilização de aplicações nos computadores do utilizador.
2. As informações de utilização de aplicação capturadas pelos agentes são enviadas através de um canal seguro e encriptado para o serviço de deteção de aplicações em nuvem.
3. O serviço de Cloud App Discovery avalia os dados e gera relatórios.

![Diagrama do cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>Passos seguintes
* [Considerações de privacidade e segurança do cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Definições de registo do cloud App Discovery para servidores Proxy com portas personalizadas](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Changelog de agente do cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)


---
title: "Conector personalizado FAQ – Azure Logic Apps | Microsoft Docs"
description: "FAQ sobre requisitos, acionadores, e assim sucessivamente sobre a criação de conectores personalizados"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>FAQ: Os conectores de personalizado

## <a name="requirements"></a>Requisitos

**P:** posso criar um conector sem REST APIs? </br>
**R:** não, para criar um conector, tem de suportar estável as APIs REST de HTTP para o seu serviço. 

**P:** que ferramentas se pode utilizar para criar um conector? </br>
**R:** Azure tem capacidades e serviços que pode utilizar para qualquer serviço a expor como uma API, tais como o serviço de aplicações do Azure para alojar, gestão de API e muito mais.

**P:** são suportados os tipos de autenticação? </br>
**R:** pode utilizar estes padrões de autenticação suportadas:

* [OAuth 2.0](https://oauth.net/2/), incluindo [do Azure Active Directory](https://azure.microsoft.com/develop/identity/) ou serviços específicos, como o Dropbox, GitHub e SalesForce
* Genérico OAuth 2.0
* [Autenticação básica](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [Chave de API](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>Acionadores

**P:** posso criar acionadores sem webhooks? </br>
**R:** não, conectores personalizados para o Azure Logic Apps e acionadores de apenas webhook com base no suporte de Microsoft Flow. Se pretender pedir outros padrões de implementação, contacte [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com) com mais detalhes sobre a API.

## <a name="certification"></a>Certificação

**Q**: não sou um parceiro da Microsoft ou independentes de Software (ISV). Pode ainda criar conectores? </br>
**A**: Sim, pode registar estes conectores para utilização interna da sua organização, mas se pretender certificar e um conector de versão publicamente, ou tem possuem o serviço subjacente ou direitos explícitos presentes ao utilizar a API.

## <a name="other"></a>Outros

**P:** My APIs utilize um anfitrião dinâmico. Como posso implementá-la com OpenAPI? </br>
**R:** conectores personalizado não suportam o dinâmicos anfitriões. Em vez disso, utilize um anfitrião estático para desenvolvimento e testes. Se pretender certificar o conector, peça ao seu contacto da Microsoft sobre a implementação dinâmica.

**P:** suportam Postman coleção V2? </br>
**R:** não, apenas o Postman coleção V1 é atualmente suportado.

**P:** suportam OpenAPI 3.0? </br>
**R:** não, apenas OpenAPI 2.0 é suportada atualmente.
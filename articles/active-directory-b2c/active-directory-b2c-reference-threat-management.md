---
title: "O Azure Active Directory B2C: Ameaça gestão | Microsoft Docs"
description: "Saiba mais sobre as técnicas de deteção e a respetiva mitigação de ataques denial of service e ataques de palavra-passe no Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
ms.openlocfilehash: 9472cb01eb713e297053727b1a314293574bb657
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-threat-management"></a>Do Azure Active Directory B2C: Gestão de ameaça

Gestão de ameaça inclui o planeamento para proteção contra ataques contra o seu sistema e redes. Ataques denial-of-service podem tornar os recursos disponíveis para utilizadores-alvo. Ataques de palavra-passe levar ao acesso não autorizado aos recursos. O Azure Active Directory B2C (Azure AD B2C) com funcionalidades incorporadas que podem ajudar a proteger os seus dados em relação a estas ameaças de várias formas.

## <a name="denial-of-service-attacks"></a>Ataques denial-of-service

Azure AD B2C utiliza a deteção e a respetiva mitigação técnicas, como cookies SIN e limites de taxa e ligação para proteger recursos subjacentes contra ataques denial-of-service.

## <a name="password-attacks"></a>Ataques de palavra-passe

O Azure AD B2C também tem técnicas de mitigação de ataques de palavra-passe. Inclui mitigação de ataques de palavra-passe de dicionário e ataques de palavra-passe de força bruta. Palavras-passe que são definidas pelos utilizadores têm de ser razoavelmente complexas. Ao utilizar vários sinais, o Azure AD B2C analisa a integridade dos pedidos. O Azure AD B2C foi concebido para diferenciar inteligentemente utilizadores-alvo de hackers e botnets. O Azure AD B2C fornece uma estratégia sofisticada para contas de bloqueio com base nas palavras-passe introduzidas na probabilidade de um ataque.

Para obter mais informações, visite o [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/threatmanagement).

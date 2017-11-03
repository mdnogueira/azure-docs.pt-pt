---
title: "Azure Active Directory manual de comunicação social PoC Ingredients | Microsoft Docs"
description: "Explorar e implementar rapidamente a cenários de identidade e gestão de acesso"
services: active-directory
keywords: do Azure Active Directory, manual, uma prova de conceito, PoC
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 7d43be3cbfd63b6bc5f06426e9810a37bbf3d071
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Azure Active Directory prova de conceito manual de comunicação social Ingredients 

## <a name="theme"></a>Tema
O Azure AD fornece soluções de identidade e acesso através de várias áreas da empresa. Iremos classificar os cenários nas seguintes áreas: 

* [Muitas aplicações, uma identidade](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [Aumentar a segurança](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [Escala com Self-Service](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

Definir um tema ao moldura o ajuda de PoC a focar-se os esforços que tenha consequências importantes com objetivos de negócio, que frequentemente são os acionadores de interesse numa prova de conceito em primeiro lugar. 

## <a name="environment"></a>Ambiente

É importante determinar os detalhes do ambiente onde irá fornecer a PoC. Idealmente, pode tirar partido de-lo Depois de concluída a PoC. O ambiente de destino é fundamental e deve encontrar o equilíbrio certo entre tornando-a como real quanto possível e a sobrecarga de restrições ou considerações adicionais. Os ambientes típicos para provas de conceito são:
* **Produção:** os cenários, irá ser implementados no seu ambiente em direto e já implementado serviços Cloud da Microsoft (produção AD, Office 365, solução SSO/inquilino do Azure AD). 
* **Teste de aceitação de utilizadores (UAT) / ambiente de desenvolvimento:** tiver infraestrutura de teste (paralela AD e potencialmente do Azure AD solução inquilino/SSO) com dados de teste que se assemelha a produção. Normalmente, o ambiente de teste é partilhado entre vários projetos na empresa.

A maioria dos cenários neste guia são cumulativas natureza. Como resultado, estes podem ser implementados no inquilino produção sem afetar os utilizadores fora da PoC. Ao longo deste documento, iremos irá ser prós que cenários teriam efeito em todo o inquilino. Nesses casos, poderá considerar um ambiente de não produção. 


## <a name="target-users"></a>Segmente utilizadores

É importante determinar o conjunto de destino de utilizadores que irá exercer os cenários, especialmente quando o ambiente de teste ou de produção. As categorias de utilizadores de destino para a PoC são:
* **Os utilizadores piloto:** utilizadores Real no ambiente que irá utilizar a solução com a conta que utilizam para as respetivas funções de tarefas do dia a dia
* **Utilizadores de teste:** contas criadas no ambiente de teste 

A maioria dos cenários neste guia podem ser executados pelos utilizadores piloto. Ao longo deste documento, iremos irá ser prós considerações de utilizador de destino se for necessário.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
---
title: "Azure Active Directory híbrida identidade considerações de design - determinar os requisitos de autenticação multifator"
description: "Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas, que escolha ao autenticar o utilizador e antes de permitir o acesso à aplicação. Depois destas condições são cumpridas, o utilizador é autenticado e permissão de acesso à aplicação."
documentationcenter: 
services: active-directory
author: femila
manager: billmath
editor: 
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 5b3a8ce6e4203dfb3700f324e32687dd910118af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de autenticação multifator para a sua solução de identidade híbrida
Neste mundo de mobilidade, com utilizadores que acedem aos dados e aplicações na nuvem e de qualquer dispositivo, proteger esta informação tornou essencial da.  Todos os dias há um novo título sobre uma violação de segurança.  Contudo, não há nenhuma garantia de tais danos, o multi-factor authentication, fornece uma camada adicional de segurança para ajudar a evitar estas violações.
Comece por ao avaliar os requisitos de organizações para autenticação multifator. Ou seja, o que é a organização tentar proteger.  Esta avaliação é importante definir os requisitos técnicos para configurar e ativar os utilizadores de organizações para autenticação multifator.

> [!NOTE]
> Se não estiver familiarizado com a MFA e o que faz, recomenda-se vivamente que leia o artigo [que é o Azure multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md) antes de continuar a ler nesta secção.
> 
> 

Certifique-se responder às seguintes:

* É a sua empresa tentar proteger aplicações da Microsoft? 
* Como estas aplicações são publicadas?
* A sua empresa fornecer acesso remoto para permitir que os funcionários acedam a aplicações no local?

Se Sim, que tipo de acesso remoto? Também terá de avaliar onde estarão localizados os utilizadores que estão a aceder a estas aplicações. Esta avaliação é outro passo importante para definir a estratégia de autenticação multifator adequada. Certifique-se responder às seguintes questões:

* Onde os utilizadores que vão ser localizado?
* Podem podem estar localizados em qualquer local?
* A sua empresa pretende estabelecer as restrições de acordo com a localização do utilizador?

Assim que compreender estes requisitos, é importante avaliar também os requisitos do utilizador para autenticação multifator. Esta avaliação é importante porque irá definir os requisitos para disponibilizando a autenticação multifator. Certifique-se responder às seguintes questões:

* Os utilizadores estão familiarizados com a autenticação multifator?
* Algumas utilizações será necessárias para fornecer autenticação adicional?  
  * Se Sim, o tempo, quando estiver a partir de redes externas ou ao aceder aos aplicações específicas ou sob outras condições?
* Os utilizadores necessitarão formação sobre como configurar e implementar a autenticação multifator?
* Quais são os cenários de chaves da sua empresa pretende ativar a multi-factor authentication para os seus utilizadores?

Depois de as respostas a perguntas anterior, será capaz de compreender se existem já implementado a autenticação multifator no local. Esta avaliação é importante definir os requisitos técnicos para configurar e ativar os utilizadores de organizações para autenticação multifator. Certifique-se responder às seguintes questões:

* A sua empresa precisa de proteger contas privilegiadas com a MFA?
* A sua empresa precisa de ativar a MFA para determinados aplicação por motivos de conformidade?
* A sua empresa precisa de ativar a MFA para todos os utilizadores elegíveis destas aplicações ou apenas os administradores?
* Tem de tem MFA sempre ativada ou apenas quando os utilizadores tem sessão iniciados fora da rede empresarial?

## <a name="next-steps"></a>Passos seguintes
[Definir uma estratégia de adoção de identidade híbrida](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Consultar também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)


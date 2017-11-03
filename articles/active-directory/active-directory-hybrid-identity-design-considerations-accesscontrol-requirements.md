---
title: "Azure Active Directory híbrida identidade considerações de design - determinar os requisitos de controlo de acesso | Microsoft Docs"
description: "Abrange os pillars de identidade e identificar os requisitos de acesso para recursos para os utilizadores num ambiente híbrido."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6404940da460461632616fe49f055d50c2a7aba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de controlo de acesso para a sua solução de identidade híbrida
Quando uma organização está a conceber a solução de identidade híbrida também podem utilizar esta oportunidade para rever os requisitos de acesso para os recursos que estiver a planear para tornar disponível para os utilizadores. O acesso a dados cruzada todos os quatro pillars da identidade, que são:

* Administração
* Autenticação
* Autorização
* Auditoria

As secções que se segue irão cobrir autenticação e autorização em obter mais detalhes, administração e auditoria fazem parte do ciclo de vida de identidade híbrida. Leitura [determinar as tarefas de gestão de identidade híbrida](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) para obter mais informações sobre estas capacidades.

> [!NOTE]
> Leitura [a quatro Pillars de identidade - Identity Management de antiguidade de TI híbrido](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) para obter mais informações sobre cada uma dessas pillars.
> 
> 

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Existem diferentes cenários de autenticação e autorização, estes cenários terá requisitos específicos que tem de ser cumpridos pela solução de identidade híbrida que a empresa irá adotar. Cenários que envolvem comunicações do negócio para empresas (B2B) podem adicionar um desafio adicional para administradores de TI, uma vez que têm de se certificar de que o método de autenticação e autorização utilizado pela organização pode comunicar com os respetivos parceiros de negócios. Durante o processo de conceção de requisitos de autenticação e autorização, certifique-se de que as perguntas seguintes são respondidas:

* Será a sua organização autenticar e autorizar o apenas os utilizadores localizados no seu sistema de gestão de identidade?
  * Existem quaisquer planos para cenários B2B?
  * Se Sim, já sabe quais protocolos (SAML, OAuth, Kerberos, os Tokens ou certificados) serão utilizados para ligar a ambas as empresas?
* É a solução de identidade híbrida que adotar suporte vai esses protocolos?

Outro ponto importante a ter em consideração é onde estarão localizado o repositório de autenticação que será utilizado pelos utilizadores e os parceiros e o modelo administrativo para ser utilizado. Considere as seguintes opções de dois núcleos:

* Centralizado: neste modelo de credenciais do utilizador, políticas e administração podem ser centralizado no local ou na nuvem.
* Híbrida: neste modelo de credenciais do utilizador, políticas e administração serão centralizado no local e um replicados na nuvem.

O modelo irá adotar a sua organização variam de acordo com os seus requisitos empresariais, pode pretender responda às questões seguintes para identificar onde irão residir o sistema de gestão de identidade e o modo administrativo a utilizar:

* A sua organização atualmente tem uma gestão de identidades no local?
  * Se Sim, planear para mantê-los?
  * Existem quaisquer requisitos regulamento ou de compatibilidade que sua organização tem de seguir que determinam em que o sistema de gestão de identidade deve residir?
* A sua organização utiliza o início de sessão único para aplicações localizados no local ou na nuvem?
  * Se Sim, a adoção de um modelo de identidade híbrida afeta a este processo?

## <a name="access-control"></a>Controlo de Acesso
Apesar de autenticação e autorização são elementos principais para ativar o acesso a dados empresariais através de validação do utilizador, também é importante controlar o nível de acesso que estes utilizadores serão e o nível de administradores do acesso será têm sobre os recursos que o se estiver a gerir. A solução de identidade híbrida tem de ser capaz de fornecer granular acesso a recursos, a delegação e controlo de acesso de base de função. Certifique-se de que a questão seguinte são respondidas sobre o controlo de acesso:

* A sua empresa tem mais do que um utilizador com privilégios elevados para gerir o seu sistema de identidade?
  * Se Sim, a cada utilizador tem o mesmo nível de acesso?
* A empresa teria de delegar o acesso aos utilizadores para gerir recursos específicos?
  * Se Sim, frequência Isto acontece?
* A empresa teria de integram capacidades de controlo de acesso entre no local e nuvem recursos?
* A empresa teria de limitar o acesso a recursos de acordo com algumas condições?
* A empresa teria qualquer aplicação que necessita de acesso de controlo personalizado a alguns recursos?
  * Se Sim, em que essas aplicações estão (no local ou na nuvem)?
  * Se Sim, onde são aqueles destino recursos localizados (no local ou na nuvem)?

> [!NOTE]
> Certifique-se de que toma nota de cada resposta e que compreende os fundamentos. [Definir a estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e as vantagens/desvantagens de cada opção.  As respostas a estas questões irá selecionar que opções melhor se adapta às suas necessidades empresariais.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Veja Também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)


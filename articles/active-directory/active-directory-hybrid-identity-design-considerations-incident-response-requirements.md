---
title: "Azure Active Directory híbrida identidade considerações de design - determinar os requisitos de incidente rResponse | Microsoft Docs"
description: "Determinar as capacidades de monitorização e relatórios para a solução de identidade híbrida que podem ser aproveitadas pelas administrador de TI para efetuar ações para identificar e mitigar uma potenciais ameaças"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 536071ec61d093af243bfd42faa6bb404172fb8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de resposta a incidentes para a sua solução de identidade híbrida
Organizações de médias ou grandes provavelmente terá um [resposta a incidentes segurança](https://technet.microsoft.com/library/cc700825.aspx) no local para ajudar a IT executar ações em conformidade para o nível de incidente. O sistema de gestão de identidade é um componente importante no processo de resposta a incidentes, porque pode ser utilizado para o ajudar a identificar quem efetuou uma ação específica contra o destino. A solução de identidade híbrida tem de ser capaz de fornecer capacidades de monitorização e relatórios que podem ser aproveitadas pelas administrador de TI para efetuar ações para identificar e mitigar uma potencial ameaça. Um plano de resposta a incidentes típico terá as fases seguintes como parte do plano:

1. Avaliação inicial.
2. Comunicação de incidente.
3. Controlo de danos e redução do risco.
4. Identificação dos quais foi comprometida e a gravidade.
5. Preservação de prova.
6. Notificação de entidades confiadoras adequadas.
7. Recuperação de sistema.
8. Documentação.
9. Avaliação de danos e o custo.
10. Revisão de processo e o plano.

Durante a identificação das qual it foi comprometida e a fase de gravidade, será necessário identificar os sistemas que tenham sido comprometidos, ficheiros que tenham sido acedidos e determinam a sensibilidade desses ficheiros. O sistema de identidade híbrida deve ser capaz de satisfazer estes requisitos para o ajudar a identificar o utilizador que efetuou essas alterações. 

## <a name="monitoring-and-reporting"></a>Monitorização e relatórios
Muitas vezes o sistema de identidade também pode ajudar na fase de avaliação inicial, principalmente se o sistema criou na auditoria e capacidades de relatórios. Durante a avaliação inicial, administrador de TI devem ser capaz de identificar uma atividade suspeita ou o sistema deve ser capaz de Acionador automaticamente com base numa tarefa de pré-configurada. Muitas atividades pode indicar um ataque possíveis, no entanto, noutros casos, um sistema incorretamente configurado poderá dar origem a um número de falsos positivos num sistema de deteção de intrusões. 

O sistema de gestão de identidade deve ajudar os administradores de TI para identificar e comunicar essas atividades suspeitas. Normalmente, estes requisitos técnicos podem ser cumpridos através da monitorização de todos os sistemas e ter uma capacidade de relatórios que pode realçar potenciais ameaças. Utilize as perguntas abaixo para ajudar a sua solução de identidade híbrida ao colocar em requisitos de resposta a incidentes de consideração de design:

* Da sua empresa tem uma resposta de incidente de segurança no local?
  * Se Sim, é o sistema de gestão de identidade atual utilizado como parte do processo?
* A sua empresa precisa de identificar tentativas de início de sessão suspeitas dos utilizadores entre vários dispositivos?
* A sua empresa precisa de detetar potenciais comprometido credenciais do utilizador?
* A sua empresa precisa de auditoria de acesso e a ação do utilizador?
* A sua empresa precisa de saber quando um utilizador repor a palavra-passe?

## <a name="policy-enforcement"></a>Imposição de política
Durante o controlo de danos e a fase de redução do risco, é importante reduzir rapidamente os efeitos reais e potenciais de um ataque. Neste momento, essa ação que irá demorar pode tornar a diferença entre uma secundária e um principal. A resposta exata dependerá da natureza do ataque que enfrentam e a sua organização. Se a avaliação inicial concluir que uma conta foi comprometida, terá de impor a política para bloquear esta conta. É apenas um exemplo onde irá ser aproveitado o sistema de gestão de identidade. Utilize as perguntas abaixo para ajudar a conceber a sua solução de identidade híbrida ao tendo em consideração, como as políticas poderá ser forçadas para reagir a um incidente em curso:

* A sua empresa tem políticas no local para impedir que os utilizadores de acesso de rede se necessário?
  * Se Sim, é a atual solução integrada com o sistema de gestão de identidade híbrida que adotar vai?
* A sua empresa precisa de impor o acesso condicional para utilizadores que estão em quarentena? 

> [!NOTE]
> Certifique-se de que toma nota de cada resposta e que compreende os fundamentos. [Definir a estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e as vantagens/desvantagens de cada opção.  Ao responder a estas questões, vai selecionar que opções melhor se adapta às sua empresa precisa.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Definir a estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Veja Também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)


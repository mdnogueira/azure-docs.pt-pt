---
title: "Identidade de híbrida do Azure Active Directory considerações de design - Determine a tarefas de gestão de identidade híbrida | Microsoft Docs"
description: "Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas, que escolha ao autenticar o utilizador e antes de permitir o acesso à aplicação. Depois destas condições são cumpridas, o utilizador é autenticado e permissão de acesso à aplicação."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 921c8391fc18eca35d10c3ade158a98ae88df397
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Planear o ciclo de vida da identidade híbrida
Identidade é uma das fundações da sua estratégia de acesso de mobilidade e a aplicação da empresa. Se está a iniciar sessão seu dispositivo móvel ou aplicação SaaS, a sua identidade é a chave para obter acesso ao tudo. Ao nível mais elevado, uma solução de gestão de identidades abrange unificá e a sincronização entre os repositórios de identidade que inclui a automatização e centralizar o processo de aprovisionamento de recursos. A solução de identidade deve ser uma identidade centralizada em nuvem e no local e também utilizar alguma forma de Federação de identidade para manter a autenticação centralizada em segurança partilhar e colaborar com utilizadores externos e as empresas. Recursos no intervalo de sistemas operativos e aplicações para pessoas ou afiliado, uma organização. Estrutura organizacional pode ser alterada para acomodar as políticas de aprovisionamento e os procedimentos.

Também é importante que tenha uma solução de identidade adaptada para capacitar os seus utilizadores, fornecendo-las com experiências self-service para mantê-las produtiva. A sua solução de identidade é mais robusta se permite início de sessão único para os utilizadores em todos os recursos que precisam de aceder aos administradores em todos os níveis podem utilizar procedimentos normalizados para a gestão de credenciais de utilizador. Alguns níveis de administração podem ser reduzidos ou eliminado o aperto, consoante o volume de solução de gestão de aprovisionamento. Além disso, pode em segurança distribuir capacidades de administração, manualmente ou automaticamente, entre várias organizações. Por exemplo, um administrador de domínio pode servir apenas as pessoas e os recursos nesse domínio. Este utilizador pode efetuar tarefas administrativas e de aprovisionamento, mas não está autorizado a efetuar tarefas de configuração, tais como criar fluxos de trabalho.

## <a name="determine-hybrid-identity-management-tasks"></a>Determinar as tarefas de gestão de identidade híbrida
A distribuição tarefas administrativas na sua organização melhora a eficácia de administração e a precisão e melhora o balanceamento da carga de trabalho de uma organização. Seguem-se a pivots que definem um sistema de gestão de identidade robusto.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)

Para definir as tarefas de gestão de identidade híbrida, tem de compreender alguns características essenciais da organização que irá ser adoção de identidade híbrida. É importante compreender os repositórios atuais a ser utilizados para origens de identidade. Por saber desses elementos de core, terá dos requisitos dos e com base no que precisa de fazer perguntas mais granulares que serão levar para uma melhor decisão de conceção para a sua solução de identidade.  

Ao definir estes requisitos, certifique-se de que, pelo menos, as perguntas seguintes são respondidas

* Opções de aprovisionamento: 
  
  * A solução de identidade híbrida suporta uma gestão de acesso da conta robusta e aprovisionamento do sistema?
  * Como são os utilizadores, grupos e as palavras-passe acedendo a ser geridos?
  * Está a responder a gestão de ciclo de vida de identidade? 
    * Quanto suspensão de conta de atualizações de palavra-passe necessário?
* Gestão de licenças: 
  
  * Funciona a gestão de licenças de identificadores de solução de identidade de híbrida?
    * Se Sim, que capacidades estão disponíveis?
* A solução de lidar com a gestão de licenças com base no grupo? 
  
      - Se Sim, é possível atribuir um grupo de segurança para o mesmo? 
       - Se Sim, o diretório em nuvem atribuirá automaticamente licenças para todos os membros do grupo? 
        - O que acontece se um utilizador é, subsequentemente, adicionado ou removido do grupo, serão uma licença automaticamente atribuída ou removida conforme adequada? 
* Integração com outros fornecedores de identidade de terceiros:
* Esta solução híbrida pode ser integrada com fornecedores de identidade de terceiros para implementar o início de sessão único?
* É possível uniformizar a todos os fornecedores de identidade diferente para um sistema de identidade coesa?
* Se Sim, como e que são eles e que capacidades estão disponíveis?

## <a name="synchronization-management"></a>Gestão de sincronização
Um dos objetivos de um Gestor de identidade, para poder colocar todos os fornecedores de identidade e mantê-las sincronizado. Manter os dados sincronizados com base num fornecedor de identidade principal autoritativo. Num cenário de identidade híbrida, com um modelo de gestão sincronizados, gerir todas as identidades de utilizador e dispositivo num servidor no local e sincronizar as contas e, opcionalmente, palavras-passe para a nuvem. O utilizador introduz a mesma palavra-passe no local como ele ou ela não na nuvem e no início de sessão, a palavra-passe é verificada pela solução de identidade. Este modelo utiliza uma ferramenta de sincronização de diretórios.

![](./media/hybrid-id-design-considerations/Directory_synchronization.png)A estrutura de adequado a sincronização da sua solução de identidade híbrida Certifique-se de que as perguntas seguintes são respondidas: • quais são as soluções de sincronização disponíveis para a solução de identidade híbrida?
• Quais são o início de sessão único capacidades disponíveis?
• Quais são as opções para a Federação de identidade entre B2B e B2C?

## <a name="next-steps"></a>Passos seguintes
[Determinar a estratégia de adoção de gestão de identidade híbrida](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Veja Também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)


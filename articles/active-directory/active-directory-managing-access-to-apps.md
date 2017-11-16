---
title: "Gerir o acesso a aplicações ao utilizar o Azure AD | Microsoft Docs"
description: "Descreve como o Azure Active Directory permite às organizações especificar as aplicações a que cada utilizador tem acesso."
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: b0829f18-9e57-4107-925d-5f0457d81671
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 6170b78f888679ab358198d940c82a00fa5e43a1
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="managing-access-to-apps"></a>Gerir o acesso a aplicações
Gestão de acesso em curso, avaliação de utilização e relatórios continuam a ser um desafio depois de uma aplicação está integrada no sistema de identidade da sua organização. Em muitos casos, os administradores de TI ou suporte técnico tem de efetuar uma função ativa em curso gerir o acesso às suas aplicações. Por vezes, a atribuição é efetuada por uma equipa de TI por divisões ou geral. Muitas vezes, a decisão de atribuição se destina a ser delegados para o negócio decisor, os respetivos aprovação antes de IT torna a atribuição.  Outras organizações investem na integração com um existente automatizada identidades e acessos sistema de gestão, como o controlo de acesso baseado em funções (RBAC) ou o controlo de acesso baseado em atributos (ABAC). A integração e o desenvolvimento de regra tendem a ser especializados e dispendioso. Monitorização ou relatórios sobre a abordagem de gestão são o seu próprio investimento separado, dispendioso e complexo.

## <a name="how-does-azure-active-directory-help"></a>Como ajuda do Azure Active Directory?
 Do Azure AD suporta a gestão de acesso de um vasto conjunto para aplicações configuradas, permitindo que as organizações alcançar facilmente as políticas de acesso corretas vão de atribuição automática, baseadas em atributos (cenários de ABAC ou RBAC) através da delegação e incluindo gestão de administradores. Com o Azure AD pode facilmente alcançar políticas complexas, combinar vários modelos de gestão para uma única aplicação e pode ainda utilizar novamente as regras de gestão em todas as aplicações com o mesmo público.

* [Adicionar novos ou existentes de aplicações](active-directory-enterprise-apps-manage-sso.md)

 Atribuição de aplicações do Azure AD centra-se em dois modos de atribuição primário:

* **Atribuição de individuais** administrador de TI um com permissões de Administrador Global do diretório pode selecionar as contas de utilizador individuais e conceder acesso à aplicação.
* **Atribuição baseada em grupo (paga do Azure AD apenas)** administrador de TI um com permissões de Administrador Global do diretório pode atribuir um grupo para a aplicação. Acesso de utilizadores específicos é determinado pelo se forem membros do grupo no momento tentam aceder à aplicação. Por outras palavras, um administrador eficazmente pode criar uma regra de atribuição a indicar "qualquer membro do grupo atribuído atual tem acesso à aplicação". Utilizar esta opção de atribuição, os administradores podem beneficiar de qualquer uma das opções de gestão de grupo do Azure AD, incluindo [baseadas em atributos de grupos dinâmicos](active-directory-groups-create-azure-portal.md), grupos de sistema externo (por exemplo, no local do Active Directory ou Workday), ou grupos gerida pelo administrador ou self-service-geridos. Um único grupo pode ser facilmente atribuído a várias aplicações, garantindo que as aplicações com a afinidade de atribuição possam partilhar as regras de atribuição, reduzir a complexidade da gestão geral. Tenha em atenção que o grupo aninhado associações não são suportadas para a atribuição de grupo para as aplicações neste momento.

Utilizar estes modos de atribuição de dois, os administradores podem alcançar qualquer abordagem de gestão de atribuição desejados.

Com o Azure AD, utilização e relatórios de atribuição é totalmente integrada, permitindo que os administradores facilmente relatórios sobre o estado de atribuição, erros de atribuição e a utilização do mesmo.

## <a name="complex-application-assignment-with-azure-ad"></a>Atribuição de aplicações complexas com o Azure AD
Considere uma aplicação, como o Salesforce. Em muitas organizações, Salesforce é principalmente utilizado por organizações de vendas e marketing. Muitas vezes, os membros da equipa de marketing altamente com acesso privilegiado Salesforce, enquanto os membros da equipa de vendas tem acesso limitado. Em muitos casos, uma população abrangente dos técnicos de informação ter restringido a acesso à aplicação. Exceções para estas regras dificultar é importante. É frequentemente prerogative das equipas de liderança marketing ou vendas para conceder um acesso de utilizadores ou alterar as respetivas funções independentemente estas regras genéricas.

Com o Azure AD, aplicações, como o Salesforce podem ser pré-configurados para-início de sessão único (SSO) e o aprovisionamento automatizado. Assim que a aplicação está configurada, um administrador pode fazer a única ação para criar e atribuir grupos adequados. Neste exemplo, um administrador foi possível executar as atribuições do seguintes:

* [Grupos dinâmicos](active-directory-groups-create-azure-portal.md) pode ser definido para representar automaticamente todos os membros das equipas de marketing e de vendas utilizar atributos como departamento ou função:
  
  * Todos os membros dos grupos de marketing seriam possível atribuir a função "marketing" no Salesforce
  * Todos os membros da equipa de venda grupos seriam atribuídos à função "venda" no Salesforce. Um refinement mais utilizar vários grupos que representam as equipas de vendas regionais atribuídas para diferentes funções do Salesforce.
* Para ativar o mecanismo de exceção, pode ser criado um grupo self-service para cada função. Por exemplo, o grupo "Exceção de marketing do Salesforce" pode ser criado como um grupo self-service. O grupo pode ser atribuído à função de marketing do Salesforce e a equipa de liderança marketing pode ser efetuada proprietários. Membros da equipa de liderança marketing foi possível adicionar ou remover utilizadores, definir uma política de associação, ou mesmo aprovar ou negar pedidos de utilizadores individuais para associar. Esta opção é suportada através de uma trabalho adequada experiência de informações do que não necessite de formação especializada para proprietários ou membros.

Neste caso, todos os utilizadores atribuídos seriam automaticamente aprovisionados Salesforce, à medida que são adicionados para diferentes grupos que a atribuição de função seria atualizada no Salesforce. Os utilizadores seria capazes de detetar e aceder a Salesforce através do painel de acesso de aplicações Microsoft, os clientes do Office web, ou mesmo navegando para os respetivos organizacional página de início de sessão do Salesforce. Os administradores conseguirão visualizar facilmente o estado da atribuição de utilização e utilizando relatórios do Azure AD.

Os administradores podem utilizar [acesso condicional do Azure AD](active-directory-conditional-access-azure-portal.md) para definir políticas de acesso para funções específicas. Estas políticas podem incluir se é permitido acesso fora do ambiente empresarial e o mesmo multi-factor Authentication ou dispositivo requisitos para alcançar o acesso a vários casos.

## <a name="how-can-i-get-started"></a>Como pode começar a utilizar?
Primeiro, se já não estão a utilizar o Azure AD e são o administrador de TI:

* [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) -Pode inscrever-se para uma avaliação gratuita de 30 dias hoje e implementar a sua primeira solução em nuvem em 5 minutos, utilizando a seguinte hiperligação

Funcionalidades do Azure AD que ativar a partilha de conta incluem:

* [Atribuição de grupo](active-directory-accessmanagement-self-service-group-management.md)
* Adicionar aplicações para o Azure AD
* Introdução à atribuição
* Atribuição de aplicação FAQ
* [Dashboard/relatórios de utilização da aplicação](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Onde posso obter mais informações?
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Proteger aplicações com o acesso condicional](active-directory-conditional-access-azure-portal.md)
* [Gestão de grupos self-service/SSAA](active-directory-accessmanagement-self-service-group-management.md)


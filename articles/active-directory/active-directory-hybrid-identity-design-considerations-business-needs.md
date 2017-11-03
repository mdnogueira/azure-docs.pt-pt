---
title: "Azure Active Directory híbrida identidade considerações de design - determinar os requisitos de identidade | Microsoft Docs"
description: "Identifica necessidades de negócio da empresa que irão originar a definir os requisitos para a estrutura de identidade híbrida."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6503034b3f5a17a2a42338c73329eef0b01f2f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de identidade para a sua solução de identidade híbrida
O primeiro passo para conceber uma solução de identidade híbrido consiste em determinar os requisitos da organização de negócio que irá tirar partido desta solução.  Identidade híbrida é iniciada como uma função de suporte (suporta todas as outras soluções de nuvem, fornecer autenticação) e entra para fornecer capacidades novas e interessantes que desbloquear novas cargas de trabalho para os utilizadores.  Estas cargas de trabalho ou serviços que pretende que adotar para os seus utilizadores vai ditar os requisitos para a estrutura de identidade híbrida.  Estes serviços e cargas de trabalho necessário tirar partido de identidade híbrida no local e na nuvem.  

Tem de rever estes aspetos-chave do negócio para compreender o que é um requisito agora e que a empresa planeia para o futuro. Se não tiver a visibilidade da estratégia a longo prazo para o design da identidade híbrida, possibilidades são de que a solução não será dimensionável à medida que as empresas e necessidades mudam.   T ele diagrama abaixo mostra um exemplo de uma arquitetura de identidade híbrida e as cargas de trabalho que estão a ser desbloqueadas para os utilizadores. Esta é apenas um exemplo de todas as possibilidades de novos que podem ser desbloqueadas e entregue com uma estratégia de identidade híbrida sólida. 

Alguns componentes que fazem parte da arquitetura de identidade híbrida![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinar as necessidades de negócio
Cada empresa tem requisitos diferentes, mesmo que essas empresas façam parte da mesma indústria, os requisitos podem variar reais de empresas. Ainda pode tirar partido das melhores práticas da indústria, mas, em última análise é necessidades comerciais da empresa que irão originar a definir os requisitos para a estrutura de identidade híbrida. 

Certifique-se de que responda às questões seguintes para identificar as necessidades de negócio:

* É da sua empresa está à procura ao cortar operacional custos com TI?
* É da sua empresa está à procura a proteger os recursos de nuvem (aplicações SaaS, infraestrutura)?
* É da sua empresa está à procura para modernize o departamento de TI?
  * Os seus utilizadores estão mobilidade e participação mais pedir o seu trabalho TI criar exceções na sua rede de Perímetro para permitir que outro tipo de tráfego para aceder aos recursos diferentes?
  * A sua empresa tem aplicações legadas que é necessário ser publicados para estes utilizadores modernas, mas não são fáceis de reescrever?
  * A sua empresa precisa de realizar todas estas tarefas e colocá-lo sob controlo ao mesmo tempo?
* É da sua empresa está à procura para proteger as identidades dos utilizadores e reduzir o risco ao colocar novas ferramentas que tiram partido os conhecimentos da segurança do Azure conhecimentos no local da Microsoft?
* É da sua empresa tentar remover as contas "externas" dreaded no local e movê-los para a nuvem em que já não são uma ameaça dormant no interior do seu ambiente no local?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analisar a infraestrutura de identidade no local
Agora que tem uma ideia sobre os requisitos de negócio da empresa, terá de avaliar a infraestrutura de identidade no local. Esta avaliação é importante para definir os requisitos técnicos para integrar a sua solução de identidade atual para o sistema de gestão de identidades de nuvem. Certifique-se responder às seguintes questões:

* Que solução de autenticação e autorização da sua empresa utilizar no local? 
* A sua empresa tem atualmente qualquer serviços de sincronização no local?
* A sua empresa utiliza quaisquer fornecedores de identidade (IdP) de terceiros?

Terá também de ter em consideração os serviços em nuvem que poderão ter a sua empresa. Efetuar uma avaliação para compreender a integração com os modelos de SaaS, IaaS ou PaaS no seu ambiente atual é muito importante. Certifique-se responder às seguintes perguntas durante esta avaliação:

* A sua empresa tem qualquer integração com um fornecedor de serviços na nuvem?
* Se Sim, quais os serviços estão a ser utilizados?
* É esta integração atualmente na produção ou é um piloto?

> [!NOTE]
> Se não tiver um mapeamento exata de todas as suas aplicações e serviços em nuvem, pode utilizar a ferramenta de Cloud App Discovery. Esta ferramenta pode fornecer o seu departamento de TI com visibilidade para empresa todos os seus da sua e aplicações em nuvem de consumidor. É assim mais simples do que nunca descobrir a sombra de TI na sua empresa, incluindo detalhes sobre padrões de utilização e quaisquer utilizadores que acedam às suas aplicações na cloud. Para obter consulte [o Cloud app discovery](active-directory-cloudappdiscovery-whatis.md).
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Avaliar requisitos de integração de identidade
Em seguida, terá de avaliar os requisitos de integração de identidade. Esta avaliação é importante definir os requisitos técnicos para a forma como irão autenticar os utilizadores, como a presença da organização será apresentada na nuvem, como a autorização irá permitir que a organização e que a experiência de utilizador vai ser. Certifique-se responder às seguintes questões:

* Organização utilizará Federação, autenticação padrão ou ambos?
* É um requisito de Federação?  Devido ao seguinte:
  * Com base em Kerberos SSO
  * A sua empresa tenha um aplicações no local (um criada interna ou 3rd) que utiliza o SAML ou semelhantes funcionalidades de Federação.
  * MFA através de Smart Cards. RSA SecurID, etc.
  * Regras de acesso de cliente que abordar as perguntas abaixo:
    1. Pode bloquear todo o acesso externo ao Office 365 com base no endereço IP do cliente?
    2. Pode bloquear todo o acesso externo ao Office 365, exceto Exchange ActiveSync?
    3. Pode bloquear todos os acessos externos ao Office 365, exceto aplicações baseadas no browser (OWA, SPO)
    4. Pode bloquear todo o acesso externo ao Office 365 para membros de grupos do AD designados
* Auditoria de segurança/preocupações
* Já investimento em autenticação federada
* O nome o nosso organização utiliza para o nosso domínio na nuvem?
* A organização tem um domínio personalizado?
  1. É o domínio público e facilmente verificável através de DNS?
  2. Se não for, em seguida, tem um domínio público que pode ser utilizado para registar um UPN alternativo no AD?
* Os identificadores de utilizador são consistentes para representação da nuvem? 
* A organização tem aplicações requerem integração com serviços em nuvem?
* A organização tem vários domínios e utilizarão todos eles autenticação federada ou padrão?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Avaliar as aplicações que são executadas no seu ambiente
Agora que tem uma ideia sobre no local e a infraestrutura de nuvem, terá de avaliar as aplicações que são executados nestes ambientes. Esta avaliação é importante definir os requisitos técnicos para integrar estas aplicações para o sistema de gestão de identidades de nuvem. Certifique-se responder às seguintes questões:

* Onde serão as nossas aplicações em direto?
* Os utilizadores acederão aplicações no local?  Na nuvem? Ou ambos?
* Existem planos para colocar as cargas de trabalho de aplicação existente e movê-los para a nuvem?
* Existem planos para desenvolver novas aplicações que irão residir no local ou na nuvem que irá utilizar na nuvem autenticação?

## <a name="evaluate-user-requirements"></a>Avaliar requisitos de utilizador
Também terá de avaliar os requisitos de utilizador. Esta avaliação é importante definir os passos que serão necessária para Dependency e ajudar os utilizadores que possam efetuar a transição para a nuvem. Certifique-se responder às seguintes questões:

* Os utilizadores acederão aplicações no local?
* Os utilizadores acederão aplicações na nuvem?
* Como fazer os utilizadores, normalmente, inicie sessão no respetivo ambiente no local?
* Como irão os utilizadores início de sessão na nuvem?

> [!NOTE]
> Certifique-se de que toma nota de cada resposta e que compreende os fundamentos. [Determinar os requisitos de resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) abordará as opções disponíveis e os profissionais de TI/contras de cada opção.  Ao responder a estas questões, vai selecionar que opções melhor se adapta às sua empresa precisa.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de sincronização de diretórios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Consultar também
[Descrição geral das considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)


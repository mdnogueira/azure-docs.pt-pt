---
title: "Azure Active Directory híbrida identidade considerações de design - descrição geral | Microsoft Docs"
description: "Descrição geral e mapa de conteúdo do guia de considerações de conceção de identidade híbrida"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: e2a70f2474298618dd8ee11c583f8f445d7eba7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considerações de Design da Identidade Híbrida do Azure Active Directory
Dispositivos com base no consumidor são proliferating mundo empresarial e baseado na nuvem aplicações do software-como-um-serviço (SaaS) são fáceis de adotar. Como resultado, é difícil manter o controlo de acesso de aplicação dos utilizadores entre plataformas internas de centros de dados e da nuvem.  

As soluções de identidade da Microsoft span no local e baseado na nuvem capacidades, criar uma identidade de utilizador único para autenticação e autorização para todos os recursos, independentemente da localização. Chamamos esta identidade híbrida. Existem design diferentes e opções de configuração da identidade híbrida soluções da Microsoft e, em alguns casos, poderá ser difícil determinar que combinação será a melhor satisfazem as necessidades da sua organização. 

Este guia de considerações de conceção de identidade híbrida irá ajudá-lo a compreender como conceber uma solução de identidade híbrida que melhor se adequa negócio e necessidades de tecnologia da sua organização.  Este guia descreve em detalhe uma série de passos e tarefas que pode seguir para ajudar a conceber uma solução de identidade híbrida que satisfaça as necessidades exclusivas da sua organização. Ao longo dos passos e tarefas, o guia irá apresentar as tecnologias relevantes e as opções disponíveis para as organizações cumprir funcional e de qualidade de serviço (como disponibilidade, escalabilidade, desempenho, capacidade de gestão e segurança) de funcionalidade nível requisitos. 

Mais concretamente, considerações de estrutura de identidade híbrida guia os objetivos são responder às seguintes questões: 

* A que perguntas preciso pedir e responder para levar a um híbrida específicos de identidade design para um domínio de tecnologia ou problema que melhor corresponda às minhas necessidades?
* Que sequência de atividades deve efetuar para conceber uma solução de identidade híbrida para o domínio de tecnologia ou problema? 
* Que opções de configuração e tecnologia de identidade do híbrida estão disponíveis para ajudar a satisfazer as minhas necessidades? Quais são os compromissos entre essas opções para que possa selecionar a melhor opção para o meu negócio?

## <a name="who-is-this-guide-intended-for"></a>Que este guia destina-se para?
 CIO, CITO, diretor Arquitetos de identidade, Arquitetos de Enterprise e Arquitetos de TI responsáveis pela conceção de uma solução de identidade híbrida para organizações médias ou grandes.

## <a name="how-can-this-guide-help-you"></a>Como pode neste guia ajudá-lo?
Pode utilizar este guia para compreender como conceber uma solução de identidade híbrida que tenha capacidade para integrar um sistema de gestão de identidade baseada na nuvem a sua solução de identidade no local atual. 

O gráfico seguinte mostra um exemplo de uma solução de identidade híbrida que permite aos administradores de TI a gerir para integrar os Windows Server Active Directory solução atual localizado no local com o Microsoft Azure Active Directory para permitir que os utilizadores utilizem o início de sessão único ( SSO) em todas as aplicações localizadas na nuvem e no local.

![](./media/hybrid-id-design-considerations/hybridID-example.png)

A ilustração acima é um exemplo de uma solução de identidade híbrida que tira partido de serviços cloud para integração com capacidades no local para fornecer uma experiência única para o processo de autenticação do utilizador final e para facilitar a IT gerir os recursos. Apesar de este pode ser um cenário muito comum, conceção de identidade híbrida de cada organização é provável que seja diferente do exemplo ilustrado na figura 1 devido a requisitos diferentes. 

Este guia fornece uma série de passos e tarefas que pode seguir para conceber uma solução de identidade híbrida que satisfaça as necessidades exclusivas da sua organização. Ao longo dos seguintes passos e tarefas, o Guia apresenta as tecnologias relevantes e as opções de funcionalidades disponíveis para si cumprir funcional e requisitos de nível de qualidade de serviço para a sua organização.

**Pressupostos**: tem alguma experiência com o Windows Server, serviços de domínio do Active Directory e o Azure Active Directory. Neste documento, partimos do pressuposto que procura como estas soluções podem satisfazer as suas necessidades por si só ou numa solução integrada.

## <a name="design-considerations-overview"></a>Descrição geral das considerações de design
Este documento fornece um conjunto de passos e tarefas que pode seguir para conceber uma solução de identidade híbrida que melhor se adeque aos seus requisitos. Os passos são apresentados numa sequência ordenada. Considerações de design que aprender em passos posteriores podem exigir a alteração das decisões que tomou em passos anteriores, no entanto, devido a conflitos de opções de design. Cada tentativa é efetuada para alertá-lo para potenciais conflitos de conceção ao longo do documento. 

Conseguirá obter o design que melhor corresponda às suas necessidades apenas depois de repetir os passos as vezes necessárias para incorporar todas as considerações no documento. 

| Fase de identidade híbrida | Lista de tópico |
| --- | --- |
| Determinar os requisitos de identidade |[Determinar as necessidades de negócio](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Determinar os requisitos de sincronização de diretórios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definir uma estratégia de adoção de identidade híbrida](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planear a segurança dos dados através da solução de identidade segura de otimização |[Determinar os requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Determinar os requisitos de gestão de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Determinar os requisitos de controlo de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Determinar os requisitos de resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definir a estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Plano para ciclo de vida da identidade híbrida |[Determinar as tarefas de gestão de identidade híbrida](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gestão de sincronização](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Determinar a estratégia de adoção de gestão de identidade híbrida](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="download-this-guide"></a>Transferir este guia
Pode transferir uma versão de pdf do guia de considerações de conceção de identidade híbrida a [Galeria de Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 


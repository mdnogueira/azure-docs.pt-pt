---
title: "Descrição geral e conceitos-chave da Gestão de APIs do Azure | Microsoft Docs"
description: "Saiba mais sobre APIs, produtos, funções, grupos e outros conceitos-chave da API Management."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: e71da405-835a-48f3-956f-45c1a85698d7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 47358c6c209488d7a12e8afbf7a2d9b3f872f0de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-api-management"></a>O que é a API Management?
A API Management ajuda as organizações a publicar APIs para programadores externos, parceiros e internos, de modo a que estes possam explorar ao máximo o potencial dos seus dados e serviços. Um pouco por todo o lado, as empresas procuram expandir as suas operações como uma plataforma digital, criando novos canais, encontrando novos clientes e promovendo um envolvimento mais profundo com os clientes existentes. A API Management fornece as competências essenciais para garantir um programa de API bem-sucedido através do envolvimento dos programadores, das informações de negócio, da análise, da segurança e da proteção.

Veja o seguinte vídeo para obter uma descrição geral da API Management do Azure e saiba como utilizar a API Management para adicionar várias funcionalidades à sua API, incluindo controlo de acesso, limitação de taxa, monitorização, registo de eventos e colocação de respostas em cache, com o mínimo de trabalho da sua parte.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-API-Management-Overview/player]
> 
> 

Para utilizar a API Management, os administradores criam APIs. Cada API é constituída por uma ou mais operações e cada API pode ser adicionada a um ou mais produtos. Para utilizar uma API, os programadores subscrevem um produto que contém essa API e, em seguida, podem chamar a operação da API, estando sujeitos a todas as políticas de utilização que possam estar em vigor.

Este tópico fornece uma descrição geral dos conceitos-chave da API Management.

> [!NOTE]
> Para obter mais informações, consulte o documento técnico em PDF [API Management baseada na nuvem: tirar partido do poder das APIs](http://j.mp/ms-apim-whitepaper). Este documento técnico introdutório sobre a API Management da CITO Research abrange: 
> 
> * Requisitos e desafios de API comuns
> * Desassociar APIs e apresentar fachadas
> * Ajudar os programadores a começar a trabalhar rapidamente
> * Proteger o acesso
> * Análise e métricas
> * Obtenção de controlo e conhecimentos aprofundados com uma plataforma de API Management
> * Utilização de soluções em nuvem versus no local
> * API Management do Azure
> 
> 

## <a name="apis"> </a>APIs e operações
As APIs são a base de uma instância de serviço de API Management. Cada API representa um conjunto de operações disponíveis para os programadores. Cada API contém uma referência ao serviço de back-end que implementa a API e as respetivas operações efetuam o mapeamento para as operações implementadas pelo serviço de back-end. As operações da API Management são altamente configuráveis, com controlo sobre o mapeamento de URL, parâmetros de caminho e consulta, conteúdo do pedido e da resposta e colocação em cache das respostas de operações. Também é possível implementar políticas de limite de taxa, quotas e restrição de IP ao nível da API ou da operação individual.

Para obter mais informações, consulte [Como criar APIs][How to create APIs] e [Como adicionar operações a uma API][How to add operations to an API].

## <a name="products"> </a> Produtos
Os produtos são a forma como as APIs são apresentadas aos programadores. Os produtos na API Management têm uma ou mais APIs e são configurados com um título, uma descrição e termos de utilização. Os produtos podem ser **Abertos** ou **Protegidos**. Os produtos protegidos têm de ser subscritos antes de poderem ser utilizados, enquanto os produtos abertos podem ser utilizados sem uma subscrição. Quando um produto estiver pronto para ser utilizado pelos programadores, pode ser publicado. Assim que for publicado, pode ser visualizado (e subscrito, no caso dos produtos protegidos) pelos programadores. A aprovação de subscrição é configurada ao nível do produto e pode exigir a aprovação do administrador ou ser aprovada automaticamente.

Os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. Os produtos concedem visibilidade aos grupos e os programadores podem ver e subscrever os produtos que estão visíveis para os grupos a que pertencem. 

Para obter mais informações, consulte [Como criar e publicar um produto][How to create and publish a product] e o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Using-Products/player]
> 
> 

## <a name="groups"> </a> Grupos
Os grupos são utilizados para gerir a visibilidade dos produtos para os programadores. A API Management tem os seguintes grupos de sistema imutáveis.

* **Administradores** – os administradores da subscrição do Azure são membros deste grupo. Os administradores gerem as instâncias de serviço de API Management, criando as APIs, as operações e os produtos que são utilizados pelos programadores.
* **Programadores** – os utilizadores autenticados do portal do programador enquadram-se neste grupo. Os programadores são os clientes que criam aplicações utilizando as suas APIs. Os programadores têm acesso ao portal do programador e criam aplicações que chamam as operações de uma API
* **Convidados** – os utilizadores não autenticados do portal do programador, tais como potenciais clientes que visitam o portal do programador de uma instância de API Management, enquadram-se neste grupo. Estes podem ter determinados acessos só de leitura, como a capacidade de ver APIs mas não de as chamar.

Para além destes grupos de sistema, os administradores podem criar grupos personalizados ou [tirar partido de grupos externos em inquilinos do Azure Active Directory associados](api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group). É possível utilizar grupos personalizados e externos em conjunto com grupos de sistema para dar aos programadores acesso e visibilidade a produtos de API. Por exemplo, pode criar um grupo personalizado para os programadores associados a uma organização parceira específica e permitir-lhes o acesso às APIs a partir de um produto que contém apenas APIs relevantes. Um utilizador pode ser membro de mais do que um grupo.

Para obter mais informações, consulte [Como criar e utilizar grupos][How to create and use groups].

## <a name="developers"> </a> Programadores
Os programadores representam as contas de utilizador numa instância de serviço de API Management. Os programadores podem ser criados ou convidados a aderirem por administradores, ou podem inscrever-se a partir do [Portal do programador][Developer portal]. Cada programador é membro de um ou mais grupos e pode subscrever os produtos que concedem visibilidade a esses grupos.

Quando os programadores subscrevem um produto, é-lhes concedida a chave primária e secundária desse produto. Esta chave é utilizada ao efetuar chamadas para as APIs do produto.

Para obter mais informações, consulte [Como criar ou convidar programadores][How to create or invite developers] e [Como associar grupos a programadores][How to associate groups with developers].

## <a name="policies"> </a> Políticas
As políticas são uma funcionalidade poderosa da API Management que permite ao publicador alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. As instruções populares incluem a conversão do formato de XML para JSON e a limitação de taxa de chamadas para restringir a quantidade de chamadas recebidas de um programador, bem como várias outras políticas disponíveis.

As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo](https://msdn.microsoft.com/library/azure/dn894085.aspx#choose) e [Definir variável](https://msdn.microsoft.com/library/azure/dn894085.aspx#set-variable), baseiam-se em expressões de política. Para obter mais informações, consulte [Políticas avançadas](https://msdn.microsoft.com/library/azure/dn894085.aspx#AdvancedPolicies), [Expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) e veja o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

Para obter uma lista completa das políticas de Gestão de API, consulte [Referência de política][Policy reference]. Para obter mais informações sobre como utilizar e configurar as políticas, consulte [Políticas de Gestão de API][API Management policies]. Para consultar um tutorial sobre a criação de um produto com políticas de limite de taxa e quota, consulte [Como criar e configurar definições de produto avançadas][How create and configure advanced product settings]. Para obter uma demonstração, veja o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="developer-portal"> </a> Portal do programador
O portal do programador é o local onde os programadores podem saber mais sobre as suas APIs, ver e chamar operações e subscrever produtos. Os potenciais clientes podem visitar o portal do programador, ver APIs e operações e inscrever-se. O URL do seu portal do programador está localizado no dashboard do Portal Clássico do Azure da sua instância de serviço de API Management.

Pode personalizar o aspeto e a funcionalidade do portal do programador ao adicionar conteúdo personalizado, personalizar os estilos e adicionar a sua imagem corporativa.

## <a name="api-management-and-the-api-economy"></a>API Management e a economia da API
Para obter mais informações sobre a API Management, veja a seguinte apresentação da conferência Microsoft Ignite 2015.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3708/player]
> 
> 

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: api-management-howto-create-apis.md
[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: api-management-howto-product-with-rules.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance





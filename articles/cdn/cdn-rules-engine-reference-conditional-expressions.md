---
title: "As regras CDN do Azure motor expressões condicionais | Microsoft Docs"
description: "Documentação de referência para a CDN do Azure regras de funcionalidades e as condições de correspondência do motor."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>As regras CDN do Azure motor expressões condicionais
Este tópico apresenta uma lista de descrições detalhadas do expressões condicionais para a rede de entrega de conteúdos (CDN) do Azure [motor de regras](cdn-rules-engine.md).

A primeira parte de uma regra está a expressão condicional.

Expressão condicional | Descrição
-----------------------|-------------
SE | Uma expressão IF sempre é uma parte da primeira instrução numa regra. Como todas as outras expressões condicionais nesta instrução se tem de ser associada a uma correspondência. Não se for definidas nenhum expressões condicionais adicionais, esta correspondência determina o critério que deve ser cumprido antes de um conjunto de funcionalidades que pode ser aplicado a um pedido.
E SE | Uma expressão IF e só pode ser adicionada depois dos seguintes tipos de condicional expressões: se, e se. Indica que não há outra condição que deve ser satisfeita para a instrução se inicial.
SE OUTRA PESSOA| Uma expressão de outra pessoa se especifica uma condição alternativa que deve ser satisfeita antes de um conjunto de funcionalidades específicas a esta declaração de outra pessoa se ocorre. A presença de uma instrução se outra pessoa indica o fim da instrução anterior. A expressão condicional apenas que pode ser colocada a seguir a uma instrução se outra pessoa é outra instrução se outra pessoa. Isto significa que uma instrução se outra pessoa só pode ser utilizada para especificar uma condição única adicional que tem de ser cumpridos.

**Exemplo**: ![CDN correspondem à condição](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Uma regra subsequente pode substituir as ações especificadas por uma regra de anterior. Exemplo: Uma regra de catch-all protege todos os pedidos através da autenticação baseada em tokens. Outra regra pode ser criada diretamente abaixo do mesmo para tornar uma exceção para determinados tipos de pedidos.

### <a name="next-steps"></a>Passos seguintes
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Referência do motor de regras](cdn-rules-engine-reference.md)
* [Condições de correspondência do motor de regras](cdn-rules-engine-reference-match-conditions.md)
* [Funcionalidades do motor de regras](cdn-rules-engine-reference-features.md)
* [Substituir o comportamento HTTP predefinido utilizando o motor de regras](cdn-rules-engine.md)

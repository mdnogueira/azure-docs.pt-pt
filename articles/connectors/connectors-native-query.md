---
title: "Adicionar a ação de consulta nas logic apps | Microsoft Docs"
description: "Descrição geral da ação de consulta para efetuar ações como filtro matriz."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: a11ba47d6ec69d31360e9142b7bce97803809c57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-query-action"></a>Começar com a ação de consulta
Ao utilizar a ação de consulta, pode trabalhar com lotes e matrizes para realizar os fluxos de trabalho para:

* Crie uma tarefa para todos os registos de alta prioridade a partir de uma base de dados.
* Guarde anexos de PDF todas as mensagens de correio eletrónico para um blob do Azure.

Para começar a utilizar a ação de consulta numa aplicação lógica, consulte o artigo [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-query-action"></a>Utilize a ação de consulta
Uma ação é uma operação que é efetuada pelo fluxo de trabalho que está definido uma aplicação lógica. [Saiba mais sobre as ações](connectors-overview.md).  

A ação de consulta tem atualmente uma operação, denominada da matriz de filtro, o que é exposta no designer. Isto permite-lhe uma matriz de consulta e devolver um conjunto de resultados filtrados.

Eis como pode adicioná-lo numa aplicação lógica:

1. Selecione o **novo passo** botão.
2. Escolha **adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **filtro** à lista de **matriz de filtro** ação.
   
    ![Selecione a ação de consulta](./media/connectors-native-query/using-action-1.png)
4. Selecione uma matriz para filtrar. (A seguinte captura de ecrã mostra a matriz de resultados a partir de uma pesquisa do Twitter.)
5. Crie uma condição para avaliar em cada item. (A seguinte captura de ecrã filtra tweets dos utilizadores que tenham mais do que 100 followers.)
   
    ![Concluir a ação de consulta](./media/connectors-native-query/using-action-2.png)
   
    A ação irá emitir uma matriz nova que contém apenas os resultados que são cumpridos os requisitos de filtro.
6. Clique em do canto superior esquerdo da barra de ferramentas para guardar e a aplicação de lógica irá guardar e publicar (ativar).

\*Se estiver a chamar um ponto final de HTTP e receber uma resposta JSON, utilize o _analisar JSON_ ação ao analisar a resposta JSON. Sem colocar este passo, _filtro matriz_ verá apenas corpo e não compreender a estrutura do JSON payload.

## <a name="query-action"></a>Ação de consulta
Seguem-se os detalhes para a ação que este conector suporta. O conector tem uma ação possíveis.

| Ação | Descrição |
| --- | --- |
| Matriz de filtro |Avalia uma condição para cada item numa matriz e devolve os resultados |

## <a name="action-details"></a>Detalhes de ação
A ação de consulta é fornecido com uma ação possíveis. As tabelas seguintes descrevem os campos de entrada necessários e opcionais para a ação e os detalhes de resultado correspondente que estão associados utilizando a ação.

### <a name="filter-array"></a>Matriz de filtro
Seguem-se os campos de entrada para a ação, o que faz um pedido de saída de HTTP.
A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| De * |Do |A matriz para filtrar |
| Condição * |onde |A condição para avaliar para cada item |

<br>

### <a name="output-details"></a>Detalhes de saída
Seguem-se detalhes de saída para a resposta HTTP.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Matriz filtrado |Matriz |Uma matriz que contenha um objeto para cada resultado filtrado |

## <a name="next-steps"></a>Passos seguintes
Agora, experimente a plataforma e [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Pode explorar os outros conectores disponíveis nas logic apps observando nosso [lista APIs](apis-list.md).


---
title: "Esquema de atualizações de Junho-1-2016 - Azure Logic Apps | Microsoft Docs"
description: "Criar definições de JSON para o Azure Logic Apps com a versão de esquema 2016-06-01"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 43df04d6478e44c82c88b17d916cfc9fe4afc03e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Atualizações do esquema para o Azure Logic Apps - 1 de Junho de 2016

Este novo esquema e a API versão para o Azure Logic Apps inclui melhoramentos essenciais que tornam as logic apps mais fiável e mais fáceis de utilizar:

* [Âmbitos](#scopes) permitem-lhe agrupar ou aninhar ações como uma coleção de ações.
* [Condições e ciclos](#conditions-loops) estão agora ações de primeira classe.
* A ordenação mais precisas para executar ações com o `runAfter` propriedade, substituindo`dependsOn`

Para atualizar as logic apps do esquema de pré-visualização 1 de Agosto de 2015 para o esquema de 1 de Junho de 2016, [consulte a secção atualizar](##upgrade-your-schema).

<a name="scopes"></a>
## <a name="scopes"></a>âmbitos

Este esquema inclui âmbitos, que lhe permitem ações de grupo em conjunto, ou ações de aninhamento dentro entre si. Por exemplo, uma condição pode conter outra condição. Saiba mais sobre [âmbito sintaxe](../logic-apps/logic-apps-loops-and-scopes.md), ou consulte neste exemplo de âmbito básico:

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

<a name="conditions-loops"></a>
## <a name="conditions-and-loops-changes"></a>Condições e ciclos de alterações

No esquema anterior versões, condições e ciclos foram parâmetros associados uma única ação. Este esquema lifts esta limitação, pelo que as condições e ciclos são agora apresentadas como tipos de ação. Saiba mais sobre [ciclos e âmbitos](../logic-apps/logic-apps-loops-and-scopes.md), ou consulte neste exemplo básico de uma ação de condição:

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>
## <a name="runafter-property"></a>propriedade de 'runAfter'

O `runAfter` propriedade substitui `dependsOn`, fornecer mais precisão quando especificar a ordem de execução de ações com base no estado das ações anteriores.

O `dependsOn` propriedade foi synonymous com "a ação foi executada e foi concluída com êxito", não é relevante o número de vezes que pretendia executar uma ação, com base em se foi bem sucedida, a ação anterior tenha falhado ou foi ignorada. O `runAfter` propriedade fornece dessa flexibilidade como um objeto que especifica todos os nomes de ação após o qual o objeto é executado. Esta propriedade também define uma matriz de Estados que são aceitáveis como acionadores. Por exemplo, se pretendesse executar depois for bem sucedida do passo um e também após passo B com êxito ou falha, pode construir esta `runAfter` propriedade:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Atualizar o esquema

Apenas atualizar para o novo esquema demora alguns passos. O processo de atualização inclui a executar o script de actualização, guardar como uma nova aplicação lógica e, se quiser, possivelmente substituir a aplicação lógica anterior.

1. No portal do Azure, abra a aplicação lógica.

2. Aceda a **descrição geral**. Na barra de ferramentas da aplicação lógica, escolha **atualizar esquema**.
   
    ![Escolha atualizar esquema][1]
   
    A definição atualizada é devolvida, que pode copiar e colar para uma definição do recurso, se necessário. 
    No entanto, iremos **vivamente recomendável** escolher **guardar como** para se certificar de que todas as referências de ligação são válidas na aplicação lógica atualizado.

3. Na barra de ferramentas do painel de atualização, escolha **guardar como**.

4. Introduza o nome de lógica e o estado. Para implementar a sua aplicação lógica atualizado, escolha **criar**.

5. Certifique-se de que a sua aplicação lógica atualizado funciona conforme esperado.
   
   > [!NOTE]
   > Se estiver a utilizar um acionador manual ou a pedido, o URL de chamada de retorno alterações na sua nova aplicação lógica. Teste o novo URL para se certificar de que funciona a experiência de ponto a ponto. Para preservar URLs anteriores, pode clonar ao longo da sua aplicação lógica existente.

6. *Opcional* para substituir a sua aplicação lógica anterior com a nova versão de esquema, na barra de ferramentas, escolha **Clone**, junto a **atualizar esquema**. Este passo é necessário apenas se pretender manter o mesmo ID de recurso ou de pedido de URL de Acionador da sua aplicação lógica.

### <a name="upgrade-tool-notes"></a>Notas de ferramenta de atualização

#### <a name="mapping-conditions"></a>Condições de mapeamento

Na definição atualizada, a ferramenta permite um melhor esforço, em conjunto como um âmbito de agrupamento ações ramo true e false. Especificamente, o padrão de estruturador `@equals(actions('a').status, 'Skipped')` devem ser apresentadas como um `else` ação. No entanto, se a ferramenta detetar padrões irreconhecível, a ferramenta poderá criar condições separadas para o verdadeiro e o ramo false. É possível remapear ações após a atualização, se necessário.

#### <a name="foreach-loop-with-condition"></a>ciclo de 'foreach' com a condição

No novo esquema, pode utilizar a ação de filtro para replicar o padrão de um `foreach` ciclo com uma condição por item, mas esta alteração automaticamente deverá ocorrer durante a atualização. A condição torna-se uma ação de filtro antes do ciclo de foreach para devolver apenas uma matriz de itens que correspondem à condição e essa matriz é transmitida para a ação de foreach. Por exemplo, consulte [ciclos e âmbitos](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Sinalizadores de recursos

Depois de atualizar, os sinalizadores de recursos são removidos, pelo que tem de repô-los para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações

### <a name="renamed-manual-trigger-to-request-trigger"></a>Trigger 'manual' cujo nome foi alterado para 'pedido' acionador

O `manual` tipo acionador foi preterido e mudar o nome para `request` com tipo `http`. Esta alteração cria mais consistência para o tipo de padrão utilizado para criar o acionador.

### <a name="new-filter-action"></a>Nova ação 'filter'

Para filtrar uma grande matriz para baixo para um conjunto de itens, mais pequeno a nova `filter` tipo aceita uma matriz e uma condição, avalia a condição para cada item e devolve uma matriz com itens de reunião, a condição.

### <a name="restrictions-for-foreach-and-until-actions"></a>Restrições para 'foreach' e 'até' ações

O `foreach` e `until` ciclo estão limitadas a uma única ação.

### <a name="new-trackedproperties-for-actions"></a>Novo 'trackedProperties' para ações

Ações agora podem ter uma propriedade adicional denominada `trackedProperties`, que é colateral para o `runAfter` e `type` propriedades. Este objeto especifica determinadas entradas de ação ou saídas que pretende incluir na telemetria do diagnóstico do Azure, emitida como parte de um fluxo de trabalho. Por exemplo:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes
* [Criar definições de fluxo de trabalho para aplicações lógicas](../logic-apps/logic-apps-author-definitions.md)
* [Criar modelos de implementação para aplicações lógicas](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png

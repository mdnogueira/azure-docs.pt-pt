---
title: "Adicionar condições e iniciar os fluxos de trabalho - Azure Logic Apps | Microsoft Docs"
description: "Controla a forma como fluxos de trabalho são executados no Azure Logic Apps adicionando lógica condicional, acionadores, ações e parâmetros."
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: e632c48ed31e82536db55a9c54438bece0c38fd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-logic-apps-features"></a>Utilizar funcionalidades das Logic Apps

Num [tópico anterior](../logic-apps/logic-apps-create-a-logic-app.md), que criou a sua primeira aplicação lógica. Para controlar o fluxo de trabalho da sua aplicação lógica, pode especificar diferentes caminhos para a sua aplicação lógica para ser executada e como processar os dados em matrizes, coleções e lotes. Pode incluir estes elementos no seu fluxo de trabalho de aplicação lógica:

* Condições e [comutador instruções](../logic-apps/logic-apps-switch-case.md) permitem a sua aplicação lógica executar ações diferentes com base em se forem cumpridas condições específicas.

* [Ciclos](../logic-apps/logic-apps-loops-and-scopes.md) permitem a sua aplicação lógica executar passos repetidamente. Por exemplo, pode repetir ações através de uma matriz ao utilizar um **For_each** ciclo. Ou pode repetir ações até que a condição é cumprida quando utiliza um **até** ciclo.

* [Âmbitos](../logic-apps/logic-apps-loops-and-scopes.md) permitem agrupar série de ações em conjunto, por exemplo, para implementar o processamento de exceções.

* [Debatching](../logic-apps/logic-apps-loops-and-scopes.md) permite que a sua aplicação lógica iniciar os fluxos de trabalho separados para os itens numa matriz quando utiliza o **SplitOn** comando.

Este tópico apresenta alguns outros conceitos para criar uma aplicação lógica:

* Vista de código para editar uma aplicação lógica existente
* Opções de início de um fluxo de trabalho

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>Condições: Passos de execução apenas depois de reunião, uma condição

Para que a sua aplicação lógica executar passos apenas quando os dados satisfazem critérios específicos, pode adicionar uma condição que compara os dados no fluxo de trabalho em relação a campos específicos ou valores.

Por exemplo, suponha que tem uma aplicação lógica que envia demasiadas mensagens de correio eletrónico para publicações no feed RSS de um Web site. Pode adicionar uma condição para que a sua aplicação lógica envia correio eletrónico apenas quando a nova mensagem pertence a uma categoria específica.

1. No [portal do Azure](https://portal.azure.com), localize e abra a sua aplicação lógica no Designer de aplicação lógica.

2. Adicione uma condição para a localização de fluxo de trabalho que pretende. 

   Para adicionar a condição entre passos existentes no fluxo de trabalho de aplicação lógica, mova o ponteiro sobre na seta para onde pretende adicionar a condição. 
   Escolha o **sinal** (**+**), em seguida, escolha **adicionar uma condição**. Por exemplo:

   ![Adicionar condição para a aplicação lógica](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > Se pretender adicionar uma condição no fim do fluxo de trabalho atual, aceda à parte inferior da sua aplicação lógica e escolha **+ novo passo**.

3. Agora, defina a condição. Especificar o campo de origem que pretende avaliar, efetuar a operação e o valor de destino ou campo. Para adicionar campos existentes para a condição, escolha o **lista de conteúdo dinâmica adicionar**.

   Por exemplo:

   ![Editar condição no modo básico](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   Segue-se a condição concluída:

   ![Condição concluída](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Para definir a condição no código, escolha **editar no modo avançado**. Por exemplo:
   > 
   > ![Editar condição no código](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. Em **se Sim** e **não se**, adicionar os passos para efetuar com base em se a condição é cumprida.

   Por exemplo:

   ![Condição com Sim e não existem caminhos](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > Pode arrastar ações existentes para o **se Sim** e **não se** caminhos.

5. Quando tiver terminado, guarde a aplicação lógica.

Agora receber e-mails apenas quando as mensagens de satisfazem a condição.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Repetir ações através de uma lista com forEach

O ciclo de forEach Especifica uma matriz para repetir uma ação de ativação pós-falha. Se não for uma matriz, o fluxo de falha. Por exemplo, se tiver action1 devolve uma matriz de mensagens e pretender enviar cada mensagem, pode incluir esta declaração de forEach nas propriedades da ação:`forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Edite a definição de código para uma aplicação lógica

Apesar de ter o Designer de aplicação lógica, pode editar diretamente o código que define uma aplicação lógica.

1. Na barra de comandos, escolha **Code vista**.

    Um editor de completo abre-se e mostra a definição editasse.

    ![Vista de código](media/logic-apps-use-logic-app-features/codeview.png)

    No editor de texto, pode copiar e colar qualquer número de ações na sua aplicação lógica mesmo ou entre as logic apps. 
    Pode adicionar ou remover secções todos a definição também facilmente e definições também pode partilhar com outras pessoas.

2. Para guardar as edições, escolha **guardar**.

## <a name="parameters"></a>Parâmetros

Algumas capacidades de Logic Apps estão disponíveis apenas na vista de código, por exemplo, os parâmetros. Os parâmetros facilitam a reutilizar os valores em toda a sua aplicação lógica. Por exemplo, se tiver um endereço de e-mail que pretende utilizar nas várias ações, deve definir esse endereço de e-mail como um parâmetro.

Os parâmetros são ideais para extrair os valores que poderá alterar muito. São particularmente útil quando for necessário substituir os parâmetros em ambientes diferentes. Para saber como substituir os parâmetros com base no ambiente, consulte [criar definições da aplicação lógica](../logic-apps/logic-apps-author-definitions.md) e [documentação da REST API](https://docs.microsoft.com/rest/api/logic).

Este exemplo mostra como atualizar a sua aplicação lógica existente para que possa utilizar os parâmetros para o termo de consulta.

1. Na vista de código, localizar o `parameters : {}` de objeto e adicionar um `currentFeedUrl` objeto:

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. Vá para o `When_a_feed-item_is_published` ação, localizar o `queries` secção e substitua o valor de consulta com:`"feedUrl": "#@{parameters('currentFeedUrl')}"` 

    Para associar as duas ou mais cadeias, também pode utilizar o `concat` função. 
    Por exemplo, `"@concat('#',parameters('currentFeedUrl'))"` funciona igual acima.

3.  Quando tiver terminado, escolha **guardar**. 

    Agora pode alterar o RSS do Web site através da transmissão de um URL diferente através de feed de `currentFeedURL` objeto.

Saiba mais sobre [como criar definições da aplicação lógica](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Iniciar os fluxos de trabalho de aplicação de lógica

Ter diferentes opções para iniciar o fluxo de trabalho definido na sua aplicação lógica. Pode sempre iniciar uma fluxo de trabalho a pedido no [portal do Azure].

### <a name="recurrence-triggers"></a>Acionadores de periodicidade

Um acionador de recorrência é executada com intervalo que especificou. Quando o acionador tem lógica condicional, o acionador determina se o fluxo de trabalho tem de executar. Um acionador indica deve executar o fluxo de trabalho devolvendo um `200` código de estado. Quando o fluxo de trabalho não tem de executar, o acionador devolve um `202` código de estado.

### <a name="callback-using-rest-apis"></a>Chamada de retorno com REST APIs

Para iniciar um fluxo de trabalho, os serviços podem chamar um ponto final da aplicação lógica. Para iniciar este tipo de lógica aplicação a pedido, escolha **executar agora** na barra de comandos. Consulte [iniciar os fluxos de trabalho chamando lógica pontos finais de aplicação como acionadores](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[portal do Azure]: https://portal.azure.com

## <a name="next-steps"></a>Passos seguintes

* [Instruções de comutador](../logic-apps/logic-apps-switch-case.md) 
* [Ciclos, âmbitos e divisões](../logic-apps/logic-apps-loops-and-scopes.md)
* [Criar definições de aplicação lógica](../logic-apps/logic-apps-author-definitions.md)
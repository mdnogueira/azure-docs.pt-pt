---
title: Resolver problemas e diagnosticar falhas - Azure Logic Apps | Microsoft Docs
description: "Compreender como e por que razão as logic apps falhar"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 10/15/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: de706f711e9c57b2e575d130a2a0cfd0bdc907a1
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="troubleshoot-and-diagnose-logic-app-failures"></a>Resolver problemas e diagnosticar falhas de aplicação lógica

A aplicação lógica gera as informações que podem ajudar a diagnosticar e depurar problemas na sua aplicação. Pode diagnosticar uma aplicação lógica, verificando cada passo no fluxo de trabalho através do portal do Azure. Ou pode adicionar alguns passos para um fluxo de trabalho para depuração de tempo de execução.

## <a name="review-trigger-history"></a>Histórico de Acionador de revisão

Cada aplicação lógica começa com o acionador. Se o acionador não acionados, verifique primeiro o histórico de Acionador. Este histórico apresenta uma lista de todas as tentativas de Acionador que efetuou a sua aplicação lógica e os detalhes sobre as entradas e saídas para cada tentativa de Acionador.

1. Para verificar se o acionador é desencadeado, no menu aplicação lógica, escolha **descrição geral**. Em **histórico de Acionador**, rever o estado do acionador.

   > [!TIP]
   > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra a aplicação lógica.

   ![Histórico de Acionador de revisão](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Se não encontrar os dados que pretende, tente selecionar **atualizar** na barra de ferramentas.
   > * Se a lista mostra muitas acionam tentativas e não é possível localizar a entrada de que pretende, tente filtrar a lista.

   Seguem-se os possíveis Estados para uma tentativa de Acionador:

   | Estado | Descrição | 
   | ------ | ----------- | 
   | **Foi concluída com êxito** | O acionador selecionado o ponto final e localizar dados disponíveis. Normalmente, um Estado "Fired" também é apresentada juntamente com este estado. Se não, a definição do acionador poderá ter uma condição ou `SplitOn` comando que não foi cumprido. <p>Este estado pode aplicar um acionador manual, acionador de recorrência ou acionador de consulta. Um acionador pode ser executado com êxito, mas a ser executada a próprio ainda poderá falhar quando as ações geram erros de não processados. | 
   | **Foi ignorada** | O acionador selecionado o ponto final, mas não existem dados não encontrado. | 
   | **Falha** | Ocorreu um erro. Para rever as mensagens de erro gerado para um acionador de falha, selecione esse tentativa de Acionador e escolha **saídas**. Por exemplo, poderá encontrar entradas que não são válidas. | 
   ||| 

   Pode ter várias entradas de Acionador com a mesma data e hora, o que acontece quando a sua aplicação lógica localiza vários itens. 
   Sempre que desencadeado acionador, o motor de Logic Apps cria uma instância da aplicação lógica para executar o fluxo de trabalho. Por predefinição, cada instância é executado em paralelo de modo a que nenhum fluxo de trabalho tem de aguardar antes de iniciar uma execução.

   > [!TIP]
   > Pode voltar o acionador sem aguardar a próxima periodicidade. Na barra de ferramentas descrição geral, escolha **executar acionador**e selecione o acionador, que força uma verificação. Em alternativa, selecione **executar** na barra de ferramentas do Designer de aplicações lógicas.

3. Para examinar os detalhes de uma tentativa de Acionador em **histórico de Acionador**, selecione esse tentativa de Acionador. 

   ![Selecione uma tentativa de Acionador](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Reveja as entradas e qualquer saídas que gerou o acionador. Saídas de Acionador mostram os dados que provém do acionador. Estes saídas podem ajudá-lo a determinar se todas as propriedades devolvidas conforme esperado.

   > [!NOTE]
   > Se encontrar qualquer conteúdo que não compreender, saiba como Azure Logic Apps [processa diferentes tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

   ![Saídas de Acionador](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Rever o histórico de execuções

Cada acionador fired inicia um fluxo de trabalho a executar. Pode rever o que aconteceu durante que executar, incluindo o estado de cada passo no fluxo de trabalho, plus entradas e saídas de cada passo.

1. No menu da aplicação lógica, escolha **Descrição geral**. Em **executa histórico**, reveja a execução para o acionador fired.

   > [!TIP]
   > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra a aplicação lógica.

   ![Executa o histórico de revisão](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Se não encontrar os dados que pretende, tente selecionar **atualizar** na barra de ferramentas.
   > * Se a lista mostra várias execuções e não é possível localizar a entrada de que pretende, tente filtrar a lista.

   Seguem-se os possíveis Estados para uma execução:

   | Estado | Descrição | 
   | ------ | ----------- | 
   | **Foi concluída com êxito** | Todas as ações com êxito. <p>Se quaisquer falhas foi efetuada a uma ação específica, uma ação seguinte no fluxo de trabalho processadas essa falha. | 
   | **Falha** | Falha de pelo menos uma ação e não existem ações posteriores no fluxo de trabalho foram configurar para lidar com a falha. | 
   | **Foi cancelada** | O fluxo de trabalho estava a ser executado, mas foi recebido um pedido de cancelamento. | 
   | **Em execução** | O fluxo de trabalho está atualmente em execução. <p>Este estado pode acontecer para fluxos de trabalho otimizados ou devido ao plano de preços atual. Para obter mais informações, consulte o [limites de ação na página de preços](https://azure.microsoft.com/pricing/details/logic-apps/). Se configurou [registo de diagnóstico](../logic-apps/logic-apps-monitor-your-logic-apps.md), também pode obter informações sobre quaisquer eventos de limitação que ocorrem. | 
   ||| 

2. Reveja os detalhes de cada passo numa execução específico. Em **executa histórico**, selecione a execução que pretende examinar.

   ![Executa o histórico de revisão](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Indica se a execução próprio foi concluída com êxito ou falha, a vista de detalhes da execução mostra cada passo e foi concluída com êxito ou falha.

   ![Ver detalhes da execução de uma aplicação lógica](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Para examinar as entradas, saídas e as mensagens de erro para um passo específico, selecione esse passo para que a forma expande e mostra os detalhes. Por exemplo:

   ![Ver detalhes do passo](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Efetuar a depuração de tempo de execução

Para ajudar a depuração, pode adicionar os passos para um fluxo de trabalho, juntamente com a rever o acionador e executa o histórico de diagnóstico. Por exemplo, pode adicionar passos que utilizam o [RequestBin](http://requestb.in) para que possam inspecionar os pedidos de HTTP e determinar o respetivo tamanho exato, forma e formato do serviço.

1. Crie um RequestBin, que pode tornar visível apenas no seu browser e privadas.

2. Na sua aplicação lógica, adicione uma ação de HTTP POST com o conteúdo do corpo que pretende testar, por exemplo, uma expressão ou outro resultado de passo.

3. Cole o URL para o seu RequestBin a ação de HTTP POST.

4. Para rever como um pedido está formado quando gerados a partir do motor de Logic Apps, executar a aplicação lógica e atualizar o seu RequestBin.

## <a name="next-steps"></a>Passos seguintes

[Monitorizar a sua aplicação lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md)
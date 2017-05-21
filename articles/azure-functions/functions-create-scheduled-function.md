---
title: "Criar uma função que é executada de acordo com uma agenda no Azure | Microsoft Docs"
description: "Saiba como criar uma função no Azure que é executada com base numa agenda definida por si."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 10461bddeb4d5adf4a6e4f65159ba2653dbef8a4
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
#  <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Criar uma função no Azure que é acionada por um temporizador

Saiba como utilizar as Funções do Azure para criar uma função que é executada com base numa agenda definida por si. 

![Criar uma aplicação de função no portal do Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

Deverá demorar menos de cinco minutos para concluir todos os passos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Neste tópico, vai criar uma função acionada por temporizador na sua aplicação de função. 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Criar uma função acionada por temporizador

1. Expanda a função de aplicação, clique no botão**+** junto a **Funções** e clique no modelo **TimerTrigger** da sua linguagem preferida. Em seguida, utilize as definições especificadas na tabela e clique em **Criar**:

    | Definição      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Dar um nome à função** | TimerTriggerCSharp1 | Define o nome da sua função acionada por temporizador.
    | **[Agenda](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 */1 * * * * | Uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) de seis campos que agenda a função para ser executada todos os minutos. |

    É criada uma função na linguagem que escolheu e que é executada todos os minutos. 

4. Veja as informações de rastreio escritas nos registos para verificar a execução. 

    ![Visualizador de registo de funções no portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Agora, pode alterar a agenda da função, para que seja executada menos vezes, como uma vez por hora. 

## <a name="update-the-timer-schedule"></a>Atualizar a agenda do temporizador

1. Expanda a função e clique em **Integrar**. É aqui que vai definir os enlaces de entrada e saída para a função, bem como a agenda. 

2. Introduza um valor para **Agenda** novo de `0 0 */1 * * *` e clique em **Guardar**.  

![Agenda de temporizador atualizado de funções no portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Tem agora uma função que é executada uma vez por hora. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes
Criou uma função que é executada com base numa agenda. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre os acionadores de temporizadores, veja [Schedule code execution with Azure Functions](functions-bindings-timer.md) (Agendar a execução de código com as Funções do Azure). 





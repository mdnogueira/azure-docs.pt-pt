---
title: "Processar mensagens do lote como um grupo ou uma coleção - Azure Logic Apps | Microsoft Docs"
description: Envie e receba mensagens para lote em processamento nas logic apps
keywords: lote, processo em lote
author: jonfancey
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: e0b7292f25a145c699dbafaf4e31e3f9d072b957
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Enviar, receber e processar mensagens nas logic apps do batch

Para processar mensagens em conjunto em grupos, pode enviar os itens de dados ou mensagens, como um *batch*e, em seguida, processar esses itens como um lote. Esta abordagem é útil quando pretender certificar-se de que os itens de dados são agrupados de uma forma específica e são processados em conjunto. 

Pode criar as logic apps que recebem itens como um lote utilizando o **Batch** acionador. Em seguida, pode criar as logic apps que enviam itens para um lote utilizando o **Batch** ação.

Este tópico mostra como pode construir uma solução de lotes executando estas tarefas: 

* [Criar uma aplicação lógica que recebe e recolhe itens como um lote](#batch-receiver). Esta aplicação de lógica de "recetor de batch" Especifica os critérios de nome e versão de batch para cumprir antes da aplicação de lógica de recetor disponibiliza e processa itens. 

* [Criar uma aplicação lógica que envia itens para um lote](#batch-sender). Esta aplicação de lógica de "remetente batch" Especifica para onde enviar os itens, que têm de ser uma aplicação de lógica de recetor existente do batch. Também pode especificar uma chave exclusiva, como um número de cliente, para "de partição" ou dividir o lote de destino em subconjuntos com base nessa chave. Dessa forma, todos os itens com essa chave são recolhidos e processados em conjunto. 

## <a name="requirements"></a>Requisitos

Para seguir este exemplo, é necessário estes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, pode [inscrever-se numa subscrição Pay As You Go](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/logic-apps-create-a-logic-app.md) 

* Uma conta de e-mail com qualquer [fornecedor e-mail suportado pelo Azure Logic Apps](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Criar as logic apps que receberem mensagens como um lote

Antes de poder enviar mensagens para um lote, primeiro tem de criar uma aplicação de lógica de "recetor de batch" com o **Batch** acionador. Dessa forma, pode selecionar esta aplicação de lógica de recetor ao criar a aplicação de lógica de remetente. Para o recetor, especifique o nome do batch, os critérios de versão e outras definições. 

Logic apps do remetente tem de saber para onde enviar os itens, enquanto as recetor logic apps não precisam de saber tudo sobre os remetentes.

1. No [portal do Azure](https://portal.azure.com), criar uma aplicação lógica com este nome: "BatchReceiver" 

2. No Designer de aplicações lógicas, adicione o **Batch** acionador, que inicia o fluxo de trabalho de aplicação lógica. Na caixa de pesquisa, introduza "batch" como o filtro. Selecione este acionador: **Batch – mensagens do Batch**

   ![Adicionar o acionador do Batch](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Forneça um nome para o batch e especificar critérios para libertar o batch, por exemplo:

   * **Nome do batch**: O nome utilizado para identificar o batch, o que é "TestBatch" neste exemplo.
   * **Critérios de versão**: os critérios de versão do batch, que podem ser baseados na contagem de mensagens, agenda ou ambos.
   
     ![Forneça detalhes de Acionador de Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Contagem de mensagens**: O número de mensagens para armazenar como um lote antes de lançar para processamento, que é "5" neste exemplo.

     ![Forneça detalhes de Acionador de Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Agenda**: O agendamento de versão de batch para processamento, que é "a cada 5 minutos" neste exemplo.

     ![Forneça detalhes de Acionador de Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Adicione outra ação envia um e-mail quando o acionador de batch é acionado. Sempre que o batch tem cinco itens ou os últimos 5 minutos, a aplicação lógica envia uma mensagem de e-mail.

   1. Sob o acionador do batch, escolha **+ novo passo** > **adicionar uma ação**.

   2. Na caixa de pesquisa, introduza "e-mail" como o filtro.
   Com base no seu fornecedor de e-mail, seleccione um conector do e-mail.
   
      Por exemplo, se tiver uma conta profissional ou escolar, selecione o conector do Outlook do Office 365. 
      Se tiver uma conta do Gmail, seleccione o conector do Gmail.

   3. Selecione esta ação para o conector:  **{*fornecedor de correio eletrónico*}-enviar um e-mail * *

      Por exemplo:

      ![Selecione a ação "Enviar uma mensagem de e-mail" para o seu fornecedor de e-mail](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Se anteriormente não tenha criado uma ligação para o seu fornecedor de e-mail, forneça as credenciais de correio eletrónico para autenticação quando lhe for pedido. Saiba mais sobre [autenticar as suas credenciais de correio eletrónico](../logic-apps/logic-apps-create-a-logic-app.md).

6. Defina as propriedades para a ação que acabou de adicionar.

   * Na caixa **Para**, introduza o endereço de e-mail do destinatário. 
   Para fins de teste, pode utilizar o seu próprio endereço de e-mail.

   * No **requerente** caixa, quando o **conteúdo dinâmico** é apresentada a lista, selecione o **nome da partição** campo.

     ![Na lista "Conteúdo dinâmico", selecione "Nome de partição"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Numa secção posterior, pode especificar uma chave de partição exclusiva que divide o lote de destino em define lógica para onde possam enviar mensagens. 
     Cada conjunto tem um número exclusivo que é gerado pela aplicação de lógica do remetente. 
     Esta capacidade permite-lhe utilizar um único lote com vários subconjuntos e defina cada subconjunto com o nome que fornecer.

   * No **corpo** caixa, quando o **conteúdo dinâmico** é apresentada a lista, selecione o **Id de mensagem** campo.

     ![Para "Corpo", selecione "Id de mensagem"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Porque a entrada para a ação de e-mail de envio é uma matriz, o estruturador adiciona automaticamente um **para cada** cíclicas à volta a **enviar um e-mail** ação. 
     Este ciclo executa a ação interna em cada item no batch. 
     Portanto, com o acionador de batch definido como itens de cinco, obter cinco e-mails tempo desencadeado o acionador.

7.  Agora que criou uma aplicação de lógica de recetor de batch, guarde a aplicação lógica.

    ![Guardar a aplicação lógica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Uma partição tem um limite de 5000 mensagens ou 80 MB. Se a condição for cumprida, o batch pode lançado, mesmo quando não for cumprida a condição definidos pelo utilizador.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Criar as logic apps que enviam mensagens para um lote

Crie um ou mais as logic apps que enviam itens para o batch definido pela aplicação de lógica do recetor. Para o remetente, especifique a aplicação de lógica de recetor e o nome do batch, conteúdo da mensagem e todas as outras definições. Opcionalmente, pode fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos para recolher os itens com essa chave.

Logic apps do remetente tem de saber para onde enviar os itens, enquanto as recetor logic apps não precisam de saber tudo sobre os remetentes.

1. Criar outra aplicação lógica com este nome: "BatchSender"

   1. Na caixa de pesquisa, introduza "recurrence" como o filtro. 
   Selecione este acionador: **agenda - periodicidade**

      ![Adicionar o acionador "Recurrence agenda"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Defina a frequência e o intervalo para executar o remetente aplicação lógica cada minuto.

      ![Definir a frequência e o intervalo de Acionador de recorrência](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Adicione um novo passo para enviar mensagens para um lote.

   1. Sob o acionador de recorrência, escolha **+ novo passo** > **adicionar uma ação**.

   2. Na caixa de pesquisa, introduza "batch" como o filtro. 

   3. Selecione esta ação: **enviar mensagens para batch – escolha de um fluxo de trabalho de aplicações lógicas com o acionador de batch**

      ![Selecione "Enviar mensagens para batch"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Agora, selecione a aplicação de lógica de "BatchReceiver" que criou anteriormente, que agora aparece como uma ação.

      ![Selecione a aplicação de lógica de "recetor de batch"](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > A lista mostra também todas as outras lógica aplicações que tenham acionadores de batch.

3. Defina as propriedades de batch.

   * **Nome do batch**: O nome de batch definido pela aplicação de lógica recetor, que é "TestBatch" neste exemplo e está validada no tempo de execução.

     > [!IMPORTANT]
     > Certifique-se de que não altere o nome do batch, que tem de coincidir com o nome de batch que é especificado pela aplicação de lógica do recetor.
     > Alterar o nome do batch faz com que o remetente aplicação lógica falhar.

   * **Conteúdo da mensagem**: O conteúdo de mensagem que pretende enviar. 
   Neste exemplo, adicione esta expressão que insere a data e hora atuais no conteúdo de mensagem a enviar para o batch:

     1. Quando o **conteúdo dinâmico** é apresentada a lista, escolha **expressão**. 
     2. Introduza a expressão **utcnow()**e escolha **OK**. 

        ![Em "Mensagem conteúdo", escolha "Expressão". Introduza "utcnow()".](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. Configure agora uma partição para o batch. A ação "BatchReceiver", escolha **Mostrar opções avançadas**.

   * **Nome de partição**: uma chave de partição exclusivo opcionais a utilizar para dividir o lote de destino. Neste exemplo, adicione uma expressão que gera um número aleatório entre um e cinco.
   
     1. Quando o **conteúdo dinâmico** é apresentada a lista, escolha **expressão**.
     2. Introduza esta expressão: **rand(1,6)**

        ![Configurar uma partição para o lote de destino](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        Isto **rand** função gera um número entre um e cinco. 
        Por isso, são dividindo este lote em cinco partições numeradas, esta expressão define dinamicamente.

   * **Id de mensagem**: um identificador da mensagem opcional e é um GUID gerado quando vazio. 
   Neste exemplo, deixe esta caixa em branco.

5. Guarde a sua aplicação lógica. A aplicação de lógica de remetente agora semelhante para este exemplo:

   ![Guarde a aplicação de lógica de remetente](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>Testar as logic apps

Para testar a sua solução de lotes, deixe as logic apps em execução para alguns minutos. Em breve, iniciar a obtenção de mensagens de correio eletrónico em grupos de cinco, tudo com a mesma chave de partição.

A aplicação de lógica de BatchSender é executada a cada minuto, gera um número aleatório entre um e cinco e utiliza este número gerado como a chave de partição para o lote de destino em que são enviadas as mensagens. Sempre que o batch tem cinco itens com a mesma chave de partição, a aplicação de lógica de BatchReceiver desencadeado e envia correio para cada mensagem.

> [!IMPORTANT]
> Quando tiver terminado de teste, certifique-se de que desativa a aplicação de lógica de BatchSender para parar o envio de mensagens e evitar sobrecarregar a pasta a receber.

## <a name="next-steps"></a>Passos seguintes

* [Criar em definições da aplicação lógica utilizando JSON](../logic-apps/logic-apps-author-definitions.md)
* [Criar uma aplicação sem servidor no Visual Studio com Azure Logic Apps e funções](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Processamento de exceções e registo de erros para aplicações lógicas](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

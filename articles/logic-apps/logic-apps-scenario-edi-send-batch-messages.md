---
title: "Lote processar EDI mensagens como um grupo ou uma coleção - Azure Logic Apps | Microsoft Docs"
description: Enviar mensagens EDI lote em processamento nas logic apps
keywords: processar do batch, lote, codificar do batch
author: divswa
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
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 837cb0d9595da5b5bd4f01fb4576f75e98ab8912
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Mensagens de envio X12 no batch para comerciais parceiros

Em cenários de (B2B) de empresa para empresa, parceiros, muitas vezes, trocam mensagens registadas em grupos ou lotes. Para enviar mensagens de grupos ou lotes para parceiros comerciais, pode criar um lote com vários itens e, em seguida, utilize o X12 ação de batch para processar esses itens como um lote.


Criação de batches para X12 mensagens, como outras mensagens, utiliza um acionador de lote e a ação. Também para X12, o batch realiza uma X12 codificar passo antes de avançar para o parceiro ou qualquer outro tipo de destino. Para obter mais informações sobre o acionador de lote e a ação, consulte [processar mensagens do Batch](logic-apps-batch-process-send-receive-messages.md).

Este tópico mostra como pode processar X12 mensagens como um lote executando estas tarefas:
* [Criar uma aplicação lógica que recebe itens e cria um lote](#receiver). Esta aplicação de lógica de "recetor" executa estas ações:
 
   * Especifica os critérios de nome e versão de batch para cumprir antes de libertar os itens como um lote.

   * Processa ou codifica os itens no batch com o X12 especificado identidades de contrato ou parceiro.

* [Criar uma aplicação lógica que envia itens para um lote](#sender). Esta aplicação de lógica de "remetente" Especifica para onde enviar os itens para a criação de batches, que têm de ser uma aplicação de lógica de recetor existente.


## <a name="prerequisites"></a>Pré-requisitos

Para seguir este exemplo, é necessário estes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, pode [inscrever-se numa subscrição Pay As You Go](https://azure.microsoft.com/pricing/purchase-options/).

* Um [conta integração](logic-apps-enterprise-integration-create-integration-account.md) que já foi definida e associados à subscrição do Azure

* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que definiu na sua conta de integração. Certifique-se de que cada parceiro utiliza o X12 (padrão operadora Alpha código) qualificador nas propriedades do parceiro como uma identidade de negócio.

* Um [X12 contrato](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Criar uma lógica de aplicação que recebe X12 mensagens e cria um lote

Antes de poder enviar mensagens para um lote, primeiro tem de criar uma aplicação de lógica de "recetor" com o **Batch** acionador. Dessa forma, pode selecionar esta aplicação de lógica de recetor ao criar a aplicação de lógica de remetente. Para o recetor, especifique o nome do batch, versão critérios, X12 contrato e outras definições. 


1. No [portal do Azure](https://portal.azure.com), criar uma aplicação lógica com este nome: "BatchX12Messages".

2. No Designer de aplicações lógicas, adicione o **Batch** acionador, que inicia o fluxo de trabalho de aplicação lógica. Na caixa de pesquisa, introduza "batch" como o filtro. Selecione este acionador: **Batch – mensagens do Batch**

   ![Adicionar o acionador do Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Forneça um nome para o batch e especificar critérios para libertar o batch, por exemplo:

   * **Nome do batch**: O nome utilizado para identificar o batch, o que é "TestBatch" neste exemplo.

   * **Critérios de versão**: os critérios de versão do batch, que podem ser baseados na contagem de mensagens, agenda ou ambos.
   
     ![Forneça detalhes de Acionador de Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Contagem de mensagens**: O número de mensagens para armazenar como um lote antes de lançar para processamento, que é "5" neste exemplo.

     ![Forneça detalhes de Acionador de Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Agenda**: O agendamento de versão de batch para processamento, que é de "10 minutos" neste exemplo.

     ![Forneça detalhes de Acionador de Batch](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Adicionar outra ação que o codifica o agrupada ou mensagens de lotes e cria um X12 em lotes mensagem. 

   a. Escolha **+ novo passo** > **adicionar uma ação**.

   b. Na caixa de pesquisa, introduza "X 12 batch" como o filtro e selecione uma ação para **X12-Batch codificar**. Como o X12 codificar o conector, há várias variações lote de ação de codificação. Pode selecionar um deles.

   ![Selecione X12 ação codificar do Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. Defina as propriedades para a ação que acabou de adicionar.

   * No **nome de X12 contrato** caixa, selecione o contrato da lista pendente. Se a lista estiver vazia, certifique-se de que criou uma ligação à sua conta de integração.

   * No **BatchName** caixa, selecione o **nome Batch** campo da lista de conteúdo dinâmica.
   
   * No **PartitionName** caixa, selecione o **nome da partição** campo da lista de conteúdo dinâmica.

   * No **itens** caixa, selecione o **itens em lotes** na lista de conteúdo dinâmico.

   ![Detalhes de ação de codificar do batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. Para fins de teste, adicionar uma ação de HTTP para enviar a mensagem de batch para [serviço pedido Bin](https://requestbin.fullcontact.com/). 

   1. Introduza "HTTP" como o filtro na caixa de pesquisa. Selecione esta ação: **HTTP - HTTP**
    
      ![Selecione a ação de HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. Do **método** lista, selecione **POST**. Para o **Uri** caixa, gerar um URI para a Reciclagem de pedido e introduza esse URI. No **corpo** caixa, quando a lista dinâmica abre, selecione o **corpo** campo no **Batch codificar pelo nome do contrato** secção. Se não vir **corpo**, escolha **ver mais** junto a **Batch codificar pelo nome do contrato**.

      ![Forneça detalhes de ação de HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Agora que criou uma aplicação de lógica de recetor, guarde a aplicação lógica.

    ![Guardar a aplicação lógica](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Uma partição tem um limite de 5000 mensagens ou 80 MB. Se a condição for cumprida, o batch pode lançado, mesmo quando não for cumprida a condição definidos pelo utilizador.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Criar uma aplicação lógica que envia X12 mensagens para um lote

Crie um ou mais as logic apps que enviam itens para o batch definido pela aplicação de lógica do recetor. Para o remetente, especifique a aplicação de lógica de recetor e o nome do batch, conteúdo da mensagem e todas as outras definições. Opcionalmente, pode fornecer uma chave de partição exclusiva para dividir o lote em subconjuntos para recolher os itens com essa chave.

Remetente logic apps precisam de saber para onde enviar os itens, enquanto as recetor logic apps não precisam de saber tudo sobre os remetentes.


1. Criar outra aplicação lógica com este nome: "X12MessageSender". Adicionar este acionador à sua aplicação lógica: **pedido / resposta - pedido** 
   
   ![Adicionar o acionador pedido](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Adicione um novo passo para enviar mensagens para um lote.

   1. Escolha **+ novo passo** > **adicionar uma ação**.

   2. Na caixa de pesquisa, introduza "batch" como o filtro. 

3. Selecione esta ação: **enviar mensagens para batch – escolha de um fluxo de trabalho de aplicações lógicas com o acionador de batch**

   ![Selecione "Enviar mensagens para batch"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Agora, selecione a aplicação de lógica de "BatchX12Messages" que criou anteriormente, que agora aparece como uma ação.

   ![Selecione a aplicação de lógica de "recetor de batch"](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > A lista mostra também todas as outras lógica aplicações que tenham acionadores de batch.

5. Defina as propriedades de batch.

   * **Nome do batch**: O nome de batch definido pela aplicação de lógica recetor, que é "TestBatch" neste exemplo e está validada no tempo de execução.

     > [!IMPORTANT]
     > Certifique-se de que não altere o nome do batch, que tem de coincidir com o nome de batch que é especificado pela aplicação de lógica do recetor.
     > Alterar o nome do batch faz com que o remetente aplicação lógica falhar.

   * **Conteúdo da mensagem**: O conteúdo de mensagem que pretende enviar para o lote
   
   ![Defina as propriedades do batch](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Guarde a sua aplicação lógica. A aplicação de lógica de remetente agora semelhante para este exemplo:

   ![Guarde a aplicação de lógica de remetente](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Testar as logic apps

Para testar a sua solução de lotes, post X12 mensagens à sua aplicação de lógica de remetente do [Postman](https://www.getpostman.com/postman) ou ferramenta semelhante. Em breve, deve iniciar a obtenção de X12 mensagens, como um lote de itens de cinco ou cada 10 minutos, a Reciclagem de pedido - tudo com a mesma chave de partição.

## <a name="next-steps"></a>Passos seguintes

* [Processar mensagens como lotes](logic-apps-batch-process-send-receive-messages.md) 
* [Criar uma aplicação sem servidor no Visual Studio com Azure Logic Apps e funções](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Processamento de exceções e registo de erros para aplicações lógicas](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

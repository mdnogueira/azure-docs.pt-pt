---
title: Saiba como utilizar o conector MQ no Azure Logic Apps | Microsoft Docs
description: "Ligar a um local ou do Azure MQ server do seu fluxo de trabalho de aplicação lógica para procurar, receber e enviar mensagens para WebSphere MQ"
services: logic-apps
author: valthom
manager: anneta
documentationcenter: 
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 06/01/2017
ms.author: valthom; ladocs
ms.openlocfilehash: 9fb0258df3f45d121cda49d3b5c274bd9548c832
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Ligar a um servidor do IBM MQ partir das logic apps através do conector MQ 

Microsoft Connector para MQ envia e obtém mensagens armazenadas no MQ Server no local ou no Azure. Este conector inclui um cliente do Microsoft MQ que comunica com um servidor remoto do IBM MQ através de uma rede TCP/IP. Este documento é um guia de arranque para utilizar o conector MQ. Recomendamos que comece por uma única mensagem uma fila de navegação e, em seguida, tentar outras ações.    

O conector MQ inclui as seguintes ações. Não existem nenhum acionador.

-   Procurar uma única mensagem sem eliminar a mensagem a partir do servidor do IBM MQ
-   Procurar um lote de mensagens em fila sem eliminar as mensagens a partir do servidor do IBM MQ
-   Receber uma mensagem único e eliminar a mensagem a partir do servidor do IBM MQ
-   Recebe um lote de mensagens e eliminar as mensagens a partir do servidor do IBM MQ
-   Enviar uma mensagem único para o IBM MQ Server 

## <a name="prerequisites"></a>Pré-requisitos

* Se utilizar um servidor MQ no local, [instalar o gateway de dados no local](../logic-apps/logic-apps-gateway-install.md) num servidor dentro da sua rede. Se o servidor de MQ publicamente, ou disponíveis no Azure, em seguida, o gateway de dados não é utilizado ou necessário.

    > [!NOTE]
    > O servidor onde está instalado o Gateway de dados no local também deve ter o .net Framework 4.6 instalado para o conector MQ para a função.

* Criar o recurso do Azure para o gateway de dados no local - [configurar a ligação de gateway de dados](../logic-apps/logic-apps-gateway-connection.md).

* Oficialmente IBM WebSphere MQ as versões suportadas:
   * MQ 7.5
   * MQ 8.0

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

1. No **Azure Iniciar quadro**, selecione  **+**  (sinal de adição), **Web + móvel**e, em seguida, **aplicação lógica**. 
2. Introduza o **nome**, tais como MQTestApp, **subscrição**, **grupo de recursos**, e **localização** (utilizar a localização onde no local Ligação de Gateway de dados está configurada). Selecione **afixar ao dashboard**e selecione **criar**.  
![Criar aplicação lógica](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Adicionar um acionador

> [!NOTE]
> O conector MQ não ter acionadores. Por isso, utilize outro acionador para iniciar a sua aplicação lógica, tais como o **periodicidade** acionador. 

1. O **Designer de aplicações lógicas** se abre, selecione **periodicidade** na lista de acionadores comuns.
2. Selecione **editar** dentro do acionador de recorrência. 
3. Definir o **frequência** para **dia**e defina o **intervalo** para **7**. 

## <a name="browse-a-single-message"></a>Procurar uma única mensagem
1. Selecione **+ novo passo**e selecione **adicionar uma ação**.
2. Na caixa de pesquisa, escreva `mq`e, em seguida, selecione **MQ - procurar mensagem**.  
![Procure a mensagem](media/connectors-create-api-mq/Browse_message.png)

3. Se não existir uma ligação de MQ existente, em seguida, crie a ligação:  

    1. Selecione **ligar através do gateway de dados no local**e introduza as propriedades do servidor MQ.  
    Para **servidor**, pode introduzir o nome do servidor MQ ou introduza o endereço IP seguido por um vírgula e o número de porta. 
    2. O **gateway** pendente lista quaisquer ligações de gateway existentes que tenham sido configuradas. Selecione o seu gateway.
    3. Selecione **criar** quando terminar. A ligação semelhante ao seguinte:   
    ![Propriedades de ligação](media/connectors-create-api-mq/Connection_Properties.png)

4. Nas propriedades da ação, pode:  

    * Utilize o **fila** propriedade para um nome de fila diferente do que é definido na ligação de acesso
    * Utilize o **MessageId**, **CorrelationId**, **GroupId**e outras propriedades para procurar uma mensagem com base nas propriedades da mensagem de diferentes MQ
    * Definir **IncludeInfo** para **verdadeiro** para incluir informações de mensagens adicionais no resultado. Em alternativa, defina-o como **falso** por não incluir informações de mensagens adicionais no resultado.
    * Introduza um **tempo limite** valor para determinar o tempo de espera de uma mensagem para chegam numa fila vazia. Se nada é introduzido, a primeira mensagem da fila é obtida e não existe nenhum tempo gasto a aguardar que uma mensagem a apresentar.  
    ![Procurar as propriedades da mensagem](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Guardar** as suas alterações e, em seguida, **executar** sua aplicação lógica:  
![Guarde e executar](media/connectors-create-api-mq/Save_Run.png)

6. Após alguns segundos, são apresentados os passos a execução e pode observar a saída. Selecione a marca de verificação verde para ver detalhes de cada passo. Selecione **Consulte saídas não processadas** para ver detalhes adicionais sobre os dados de saída.  
![Procurar a saída de mensagem](media/connectors-create-api-mq/Browse_message_output.png)  

    Dados de saída não processados:  
    ![Procurar saída em bruto de mensagens](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Quando o **IncludeInfo** opção estiver definida como VERDADEIRO, é apresentado o seguinte resultado:  
![Mensagem de procurar incluem informações](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Procurar várias mensagens
O **procurar mensagens** ação inclui um **BatchSize** opção para indicar deve ser devolvido a quantidade de mensagens da fila.  Se **BatchSize** não tem entradas, são devolvidas todas as mensagens. O resultado devolvido é uma matriz de mensagens em fila.

1. Ao adicionar o **procurar mensagens** ação, a primeira ligação que está configurada está selecionada por predefinição. Selecione **Alterar ligação** para criar uma ligação nova ou selecione uma ligação diferente.

2. A saída da procura mensagens mostra:  
![Procurar a saída de mensagens](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Receber uma mensagem único
O **Receive mensagem** ação tem as mesmas entradas e saídas como o **procurar mensagem** ação. Quando utilizar **Receive mensagem**, a mensagem é eliminada da fila.

## <a name="receive-multiple-messages"></a>Receber várias mensagens
O **receber mensagens** ação tem as mesmas entradas e saídas como o **procurar mensagens** ação. Quando utilizar **receber mensagens**, as mensagens são eliminadas da fila.

Se não existirem não mensagens na fila de espera ao efetuar uma procura ou receive, o passo falhar com o seguinte resultado:  
![Não MQ mensagem de erro](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Enviar uma mensagem
1. Ao adicionar o **enviar mensagem** ação, a primeira ligação que está configurada está selecionada por predefinição. Selecione **Alterar ligação** para criar uma ligação nova ou selecione uma ligação diferente. Válido **tipos de mensagens** são **datagrama**, **resposta**, ou **pedido**.  
![Enviar propriedades tarifas de mensagens](media/connectors-create-api-mq/Send_Msg_Props.png)

2. A saída do enviar mensagem tem o seguinte aspeto:  
![Enviar tarifas de mensagens de saída](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/mq/).

## <a name="next-steps"></a>Passos seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explorar os outros conectores disponíveis em Logic Apps no nosso [lista APIs](apis-list.md).

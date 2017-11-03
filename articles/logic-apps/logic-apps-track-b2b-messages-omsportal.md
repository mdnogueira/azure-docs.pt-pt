---
title: Controlar as mensagens B2B no Operations Management Suite - Azure Logic Apps | Microsoft Docs
description: "Controlar a comunicação de B2B para as suas aplicações de conta e a lógica da integração no Operations Management Suite (OMS) com o Log Analytics do Azure"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3ef7a4054be80547b0d91ad1f13777d915005f8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="track-b2b-communication-in-the-microsoft-operations-management-suite-oms"></a>Controlar a comunicação de B2B no Microsoft Operations Management Suite (OMS)

Após configurar a comunicação de B2B entre duas a executar os processos de negócios ou aplicações através da sua conta de integração, estas entidades podem trocar mensagens entre si. Para verificar se estas mensagens são processadas corretamente, pode controlar AS2, X12, e EDIFACT mensagens com [Log Analytics do Azure](../log-analytics/log-analytics-overview.md) no [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Por exemplo, pode utilizar estas capacidades de controlo baseada na web para o registo de mensagens:

* Contagem de mensagens e o Estado
* Estado em que as confirmações
* Correlacionar mensagens com em que as confirmações
* Descrições de erro detalhadas para falhas
* Capacidades de pesquisa

## <a name="requirements"></a>Requisitos

* Uma aplicação lógica que esteja configurada com o registo de diagnóstico. Saiba [como criar uma aplicação lógica](logic-apps-create-a-logic-app.md) e [como configurar o registo para essa aplicação lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração que está configurada com a monitorização e registo. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar a monitorização e o registo para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se ainda não o fez, [publicar dados de diagnóstico ao Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) no OMS.

> [!NOTE]
> Após cumprir os requisitos anteriores, deve ter uma área de trabalho a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Deve utilizar o mesmo espaço de trabalho do OMS para controlar a sua comunicação B2B no OMS. 
>  
> Se não tiver uma área de trabalho do OMS, saiba [como criar uma área de trabalho do OMS](../log-analytics/log-analytics-get-started.md).

## <a name="add-the-logic-apps-b2b-solution-to-the-operations-management-suite-oms"></a>Adicionar a solução de Logic Apps B2B para o Operations Management Suite (OMS)

Para que o OMS controlar B2B mensagens para a sua aplicação lógica, tem de adicionar o **Logic Apps B2B** solução para o portal do OMS. Saiba mais sobre [adicionar soluções OMS](../log-analytics/log-analytics-get-started.md).

1. No [portal do Azure](https://portal.azure.com), escolha **mais serviços**. Procure "análise de registos" e, em seguida, escolha **Log Analytics** conforme mostrado aqui:

   ![Localizar a análise de registos](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. Em **Log Analytics**, localize e selecione a sua área de trabalho do OMS. 

   ![Selecione a sua área de trabalho do OMS](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Em **gestão**, escolha **Portal do OMS**.

   ![Escolha o portal do OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Depois de abrir a home page do OMS, escolha **soluções galeria**.    

   ![Escolha a Galeria de soluções](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Em **todas as soluções**, localizar e escolha **Logic Apps B2B**.     

   ![Escolha as Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Em **Logic Apps B2B**, escolha **adicionar**.

   ![Escolher adicionar](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   Na página inicial do OMS, o mosaico da **Logic Apps B2B mensagens** aparece. 
   Este mosaico atualiza a contagem de mensagens quando as mensagens B2B são processadas.

   ![Página inicial do OMS, Logic Apps B2B mensagens mosaico](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-the-operations-management-suite"></a>Controlar o estado de mensagem e os detalhes no Operations Management Suite

1. Depois das mensagens B2B são processadas, pode ver o estado e os detalhes para essas mensagens. Na home page do OMS, escolha o **Logic Apps B2B mensagens** mosaico.

   ![Contagem de mensagens atualizado](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > Por predefinição, o **Logic Apps B2B mensagens** mosaico mostra dados com base num único dia. Para alterar o âmbito de dados para um intervalo de diferentes, escolha o controlo de âmbito na parte superior da página OMS:
   > 
   > ![Alterar o âmbito de dados](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. Após a mensagem de dashboard de estado é apresentado, pode ver mais detalhes sobre um tipo de mensagem específico, que mostra os dados com base num único dia. Escolha o mosaico da **AS2**, **X12**, ou **EDIFACT**.

   ![Ver o estado de mensagem](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   É apresentada uma lista de mensagens em fila para o mosaico escolhido. 
   Para saber mais sobre as propriedades para cada tipo de mensagem, consulte estas descrições de propriedade de mensagem:

   * [Propriedades da mensagem AS2](#as2-message-properties)
   * [Propriedades da mensagem X12](#x12-message-properties)
   * [Propriedades da mensagem EDIFACT](#EDIFACT-message-properties)

   Por exemplo, aqui está uma lista de mensagens AS2 poderá aspeto:

   ![Ver mensagens de AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Para ver ou exportação de entradas e saídas para as mensagens específicas, selecione essas mensagens e escolha **transferir**. Quando lhe for pedido, guarde o ficheiro. zip no computador local e, em seguida, extraia esse ficheiro. 

   A pasta extraída inclui uma pasta para cada mensagem selecionada. 
   Se configurar as confirmações, a pasta de mensagem inclui também os ficheiros com detalhes de confirmação. 
   Pasta cada mensagem tem, pelo menos, estes ficheiros: 
   
   * Legível por humanos ficheiros com o payload de entrada e saída payload detalhes
   * Ficheiros codificados com as entradas e saídas

   Para cada tipo de mensagem, pode encontrar a pasta e formatos de nome de ficheiro aqui:

   * [Formatos de nome de pasta e ficheiro AS2](#as2-folder-file-names)
   * [X12 formatos de nome de pasta e ficheiro](#x12-folder-file-names)
   * [Formatos de nome de pasta e ficheiro EDIFACT](#edifact-folder-file-names)

   ![Transferir ficheiros de mensagens](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Para ver todas as ações que tenham o mesmo execute ID, no **pesquisa registo** página, escolha uma mensagem a partir da lista de mensagens.

   Pode ordenar estas ações por coluna ou procure resultados específicos.

   ![ID de execução de ações com o mesmo](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Para procurar os resultados com consultas prebuilt, escolha **Favoritos**.

   * Saiba [como criar consultas, adicionando filtros de](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Ou Saiba mais sobre [como encontrar dados com pesquisas de registo na análise de registos](../log-analytics/log-analytics-log-searches.md).

   * Para alterar a consulta na caixa de pesquisa, atualize a consulta com as colunas e valores que pretende utilizar como filtros.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descrições das propriedades e formatos de nome para AS2, X12 e mensagens EDIFACT

Para cada tipo de mensagem, seguem-se as descrições de propriedade e formatos de nome de ficheiros de mensagens transferido.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descrições de propriedade de mensagem AS2

Seguem-se as descrições de propriedade para cada mensagem AS2.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro do convidado especificado na **receber definições**, ou o parceiro de anfitrião especificado na **enviar definições** para um contrato de AS2 |
| Recetor | O parceiro de anfitrião especificado na **receber definições**, ou o parceiro de convidado especificadas **enviar definições** para um contrato de AS2 |
| Aplicação Lógica | A aplicação lógica em que são configuradas as ações de AS2 |
| Estado | O estado de mensagem AS2 <br>Êxito = recebidos ou enviados uma mensagem de AS2 válida. Não existem MDN está definida. <br>Êxito = recebidos ou enviados uma mensagem de AS2 válida. MDN está configurado e recebido ou MDN é enviado. <br>Não foi possível = recebida uma mensagem de AS2 inválida. Não existem MDN está definida. <br>Pendente = recebidos ou enviados uma mensagem de AS2 válida. Configurar a MDN e MDN é esperado. |
| Confirmação | O estado de mensagem MDN <br>Aceite = recebidos ou enviados um MDN positivo. <br>Pendente = à espera de receber ou enviar um MDN. <br>Rejeitado = recebidos ou enviados um MDN negativo. <br>Não é necessária = MDN não está definido no contrato. |
| Direção | A direção de mensagem AS2 |
| ID de correlação | O ID de que está correlacionada com todos os acionadores e ações numa aplicação lógica |
| ID da mensagem | O ID de mensagem AS2 de cabeçalhos de mensagens AS2 |
| Timestamp | A hora quando a ação de AS2 processar a mensagem |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formatos de nome de AS2 para ficheiros de mensagens transferido

Seguem-se formatos de nome para cada pasta de mensagem AS2 transferida e ficheiros.

| Ficheiro ou pasta | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [remetente] \_[recetor]\_AS2\_[ID de correlação]\_[ID de mensagem]\_[timestamp] |
| Entrada, saída e se configurar os ficheiros de confirmação | **Payload de entrada**: [remetente]\_[recetor]\_AS2\_[ID de correlação]\_input_payload.txt </p>**Payload de saída**: [remetente]\_[recetor]\_AS2\_[ID de correlação]\_saída\_payload.txt </p></p>**Entradas**: [remetente]\_[recetor]\_AS2\_[ID de correlação]\_inputs.txt </p></p>**Saídas**: [remetente]\_[recetor]\_AS2\_[ID de correlação]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descrições das propriedades da mensagem X12

Seguem-se as descrições de propriedade para cada X12 mensagem.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro do convidado especificado na **receber definições**, ou o parceiro de anfitrião especificado na **enviar definições** para um X12 contrato |
| Recetor | O parceiro de anfitrião especificado na **receber definições**, ou o parceiro de convidado especificadas **enviar definições** para um X12 contrato |
| Aplicação Lógica | A aplicação lógica em que o X12 ações estão configuradas |
| Estado | O X12 mensagem de estado <br>Êxito = recebidos ou enviados um X12 válido mensagem. Nenhum ack funcional está definida. <br>Êxito = recebidos ou enviados um X12 válido mensagem. Ack funcional está configurado e recebido ou uma confirmação funcional é enviada. <br>Não foi possível = recebidos ou enviados um X12 inválido mensagem. <br>Pendente = recebidos ou enviados um X12 válido mensagem. Configurar a confirmação funcional e prevê-se uma confirmação funcional. |
| Confirmação | Estado de confirmação (997) funcional <br>Aceite = recebidos ou enviados uma confirmação funcional positivo <br>Rejeitado = recebidos ou enviados uma confirmação negativa de funcional <br>Pendente = era esperado um ack funcional, mas não foi recebido. <br>Pendente = gerou uma confirmação funcional, mas não é possível enviar para o parceiro. <br>Não é necessária = funcional ack não está configurado. |
| Direção | A direção da mensagem X12 |
| ID de correlação | O ID de que está correlacionada com todos os acionadores e ações numa aplicação lógica |
| Tipo de tarifas de mensagens | O tipo de mensagem 12 EDI X |
| ICN | O número de controlo intercâmbio para o X12 mensagem |
| TSCN | O número de controlo de transação definida para o X12 mensagem |
| Timestamp | A hora quando o X12 processar a ação a mensagem |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formatos de nome de X12 para ficheiros de mensagens transferido

Seguem-se formatos de nome de cada transferido X12 pastas e ficheiros de mensagens.

| Ficheiro ou pasta | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [remetente] \_[recetor]\_X12\_[número de controlo de intercâmbio]\_[global-controlo-número]\_[transação-set-controlo-número]\_[timestamp] |
| Entrada, saída e se configurar os ficheiros de confirmação | **Payload de entrada**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_input_payload.txt </p>**Payload de saída**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_saída\_payload.txt </p></p>**Entradas**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_inputs.txt </p></p>**Saídas**: [remetente]\_[recetor]\_X12\_[número de controlo de intercâmbio]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descrições de propriedade de mensagem EDIFACT

Seguem-se as descrições de propriedade para cada mensagem EDIFACT.

| Propriedade | Descrição |
| --- | --- |
| Remetente | O parceiro do convidado especificado na **receber definições**, ou o parceiro de anfitrião especificado na **enviar definições** para um contrato de EDIFACT |
| Recetor | O parceiro de anfitrião especificado na **receber definições**, ou o parceiro de convidado especificadas **enviar definições** para um contrato de EDIFACT |
| Aplicação Lógica | A aplicação lógica em que as ações de EDIFACT estão configuradas |
| Estado | O estado de mensagem EDIFACT <br>Êxito = recebidos ou enviados uma mensagem EDIFACT válida. Nenhum ack funcional está definida. <br>Êxito = recebidos ou enviados uma mensagem EDIFACT válida. Ack funcional está configurado e recebido ou uma confirmação funcional é enviada. <br>Não foi possível = recebidos ou enviados uma mensagem EDIFACT inválida <br>Pendente = recebidos ou enviados uma mensagem EDIFACT válida. Configurar a confirmação funcional e prevê-se uma confirmação funcional. |
| Confirmação | Estado de confirmação (997) funcional <br>Aceite = recebidos ou enviados uma confirmação funcional positivo <br>Rejeitado = recebidos ou enviados uma confirmação negativa de funcional <br>Pendente = era esperado um ack funcional, mas não foi recebido. <br>Pendente = gerou uma confirmação funcional, mas não é possível enviar para o parceiro. <br>Não é necessária = Ack funcional não está configurado. |
| Direção | A direção de mensagem EDIFACT |
| ID de correlação | O ID de que está correlacionada com todos os acionadores e ações numa aplicação lógica |
| Tipo de tarifas de mensagens | O tipo de mensagem EDIFACT |
| ICN | O número de controlo intercâmbio para a mensagem EDIFACT |
| TSCN | O número de controlo de transação definida para a mensagem EDIFACT |
| Timestamp | A hora quando a ação de EDIFACT processar a mensagem |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formatos de nome EDIFACT para ficheiros de mensagens transferido

Seguem-se formatos de nome para cada pasta de mensagem EDIFACT transferida e ficheiros.

| Ficheiro ou pasta | Formato de nome |
| :------------- | :---------- |
| Pasta de mensagem | [remetente] \_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_[global-controlo-número]\_[transação-set-controlo-número]\_[timestamp] |
| Entrada, saída e se configurar os ficheiros de confirmação | **Payload de entrada**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_input_payload.txt </p>**Payload de saída**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_saída\_payload.txt </p></p>**Entradas**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_inputs.txt </p></p>**Saídas**: [remetente]\_[recetor]\_EDIFACT\_[número de controlo de intercâmbio]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Passos seguintes

* [Consulta para mensagens B2B no Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de controlo de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de controlo personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
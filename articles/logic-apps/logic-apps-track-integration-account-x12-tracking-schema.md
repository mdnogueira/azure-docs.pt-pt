---
title: "X12 esquemas de controlo para B2B monitorização - Azure Logic Apps | Microsoft Docs"
description: "Utilize X12 esquemas de controlo para monitorizar as mensagens B2B de transações na sua conta de integração do Azure."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3782c0a76ea8728a146b3d73774f74c31187cbfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Mensagens de início ou controlo de ativação de X12 êxito do monitor, erros e propriedades da mensagem
Pode utilizar estes X12 controlo esquemas na sua conta de integração do Azure para o ajudar a monitorizar transações de empresa-empresa (B2B):

* X12 transação definida esquema de controlo
* X12 transação definida confirmação esquema de controlo
* Esquema de controlo de intercâmbio de X12
* Esquema de controlo de confirmação de intercâmbio de X12
* Esquema de controlo de grupo X12 funcional
* Esquema de controlo de confirmação de grupo X12 funcional

## <a name="x12-transaction-set-tracking-schema"></a>X12 transação definida esquema de controlo
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia | Nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia | Nome do parceiro do recetor da mensagem X12. (Opcional) |
| senderQualifier | Cadeia | Envie o qualificador de parceiro. (Obrigatório) |
| senderIdentifier | Cadeia | Envie o identificador de parceiro. (Obrigatório) |
| receiverQualifier | Cadeia | Receba o qualificador de parceiro. (Obrigatório) |
| receiverIdentifier | Cadeia | Receba o identificador de parceiro. (Obrigatório) |
| agreementName | Cadeia | Nome do X12 contrato ao qual as mensagens são resolvidas. (Opcional) |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia | Intercâmbio do número de controlo. (Opcional) |
| functionalGroupControlNumber | Cadeia | Número de controlo funcional. (Opcional) |
| transactionSetControlNumber | Cadeia | Transação de definir o número de controlo. (Opcional) |
| CorrelationMessageId | Cadeia | ID de mensagem de correlação. Uma combinação de {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Opcional) |
| messageType | Cadeia | Transação definida ou tipo de documento. (Opcional) |
| isMessageFailed | Valor booleano | Se o X12 mensagem de falha. (Obrigatório) |
| isTechnicalAcknowledgmentExpected | Valor booleano | Indica se a confirmação técnica está configurada no X12 contrato. (Obrigatório) |
| isFunctionalAcknowledgmentExpected | Valor booleano | Indica se a confirmação funcional está configurada no X12 contrato. (Obrigatório) |
| needAk2LoopForValidMessages | Valor booleano | Indica se o ciclo de AK2 é necessário para uma mensagem válida. (Obrigatório) |
| segmentsCount | Número inteiro | Número de segmentos no X12 conjunto de transação. (Opcional) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 transação definida confirmação esquema de controlo
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia | Nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia | Nome do parceiro do recetor da mensagem X12. (Opcional) |
| senderQualifier | Cadeia | Envie o qualificador de parceiro. (Obrigatório) |
| senderIdentifier | Cadeia | Envie o identificador de parceiro. (Obrigatório) |
| receiverQualifier | Cadeia | Receba o qualificador de parceiro. (Obrigatório) |
| receiverIdentifier | Cadeia | Receba o identificador de parceiro. (Obrigatório) |
| agreementName | Cadeia | Nome do X12 contrato ao qual as mensagens são resolvidas. (Opcional) |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia | Intercâmbio controlo número a confirmação funcional. Povoa o valor apenas para o lado de envio onde funcional confirmação é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| functionalGroupControlNumber | Cadeia | Grupo de funcional controlo número a confirmação funcional. Povoa o valor apenas para o lado de envio onde funcional confirmação é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| isaSegment | Cadeia | Segmento ISA da mensagem. Povoa o valor apenas para o lado de envio onde funcional confirmação é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| gsSegment | Cadeia | Segmento GS da mensagem. Povoa o valor apenas para o lado de envio onde funcional confirmação é recebida para as mensagens enviadas ao parceiro. (Opcional) |
| respondingfunctionalGroupControlNumber | Cadeia | Número de controlo de intercâmbio de responder. (Opcional) |
| respondingFunctionalGroupId | Cadeia | ID do grupo de funcional, que mapeia para AK101 na confirmação de responder. (Opcional) |
| respondingtransactionSetControlNumber | Cadeia | Resposta de transação definir o número de controlo. (Opcional) |
| respondingTransactionSetId | Cadeia | Resposta de transação definida ID, que mapeia para AK201 na confirmação. (Opcional) |
| statusCode | Valor booleano | Transação definida código de estado de confirmação. (Obrigatório) |
| segmentsCount | Enum | Código de estado de confirmação. Valores permitidos são **aceites**, **rejeitado**, e **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | Estado de processamento da confirmação. Valores permitidos são **recebidos**, **Generated**, e **enviados**. (Obrigatório) |
| CorrelationMessageId | Cadeia | ID de mensagem de correlação. Uma combinação de {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Opcional) |
| isMessageFailed | Valor booleano | Se o X12 mensagem de falha. (Obrigatório) |
| ak2Segment | Cadeia | Confirmação de uma transação definida dentro do grupo de funcional recebido. (Opcional) |
| ak3Segment | Cadeia | Relatórios de erros de um segmento de dados. (Opcional) |
| ak5Segment | Cadeia | Se a transação definida identificada no segmento AK2 seja aceite ou rejeitada e, razão pela qual os relatórios. (Opcional) |

## <a name="x12-interchange-tracking-schema"></a>Esquema de controlo de intercâmbio de X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia | Nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia | Nome do parceiro do recetor da mensagem X12. (Opcional) |
| senderQualifier | Cadeia | Envie o qualificador de parceiro. (Obrigatório) |
| senderIdentifier | Cadeia | Envie o identificador de parceiro. (Obrigatório) |
| receiverQualifier | Cadeia | Receba o qualificador de parceiro. (Obrigatório) |
| receiverIdentifier | Cadeia | Receba o identificador de parceiro. (Obrigatório) |
| agreementName | Cadeia | Nome do X12 contrato ao qual as mensagens são resolvidas. (Opcional) |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia | Intercâmbio do número de controlo. (Opcional) |
| isaSegment | Cadeia | Segmento ISA da mensagem. (Opcional) |
| isTechnicalAcknowledgmentExpected | Valor booleano | Indica se a confirmação técnica está configurada no X12 contrato. (Obrigatório) |
| isMessageFailed | Valor booleano | Se o X12 mensagem de falha. (Obrigatório) |
| isa09 | Cadeia | X12 documentar intercâmbio data. (Opcional) |
| isa10 | Cadeia | Tempo de intercâmbio de documentos X12. (Opcional) |
| isa11 | Cadeia | Controlo de intercâmbio de X12 identificador normas. (Opcional) |
| isa12 | Cadeia | X12 intercâmbio controlo número de versão. (Opcional) |
| isa14 | Cadeia | X12 foi solicitada a confirmação. (Opcional) |
| isa15 | Cadeia | Indicador de teste ou de produção. (Opcional) |
| isa16 | Cadeia | Separador de elemento. (Opcional) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Esquema de controlo de confirmação de intercâmbio de X12
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia | Nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia | Nome do parceiro do recetor da mensagem X12. (Opcional) |
| senderQualifier | Cadeia | Envie o qualificador de parceiro. (Obrigatório) |
| senderIdentifier | Cadeia | Envie o identificador de parceiro. (Obrigatório) |
| receiverQualifier | Cadeia | Receba o qualificador de parceiro. (Obrigatório) |
| receiverIdentifier | Cadeia | Receba o identificador de parceiro. (Obrigatório) |
| agreementName | Cadeia | Nome do X12 contrato ao qual as mensagens são resolvidas. (Opcional) |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia | Intercâmbio controlo número a confirmação técnica que é recebido de parceiros. (Opcional) |
| isaSegment | Cadeia | Segmento ISA para a confirmação técnica que é recebido de parceiros. (Opcional) |
| respondingInterchangeControlNumber |Cadeia | Intercâmbio controlo número para a confirmação técnica que é recebido de parceiros. (Opcional) |
| isMessageFailed | Valor booleano | Se o X12 mensagem de falha. (Obrigatório) |
| statusCode | Enum | Intercâmbio do código de estado de confirmação. Valores permitidos são **aceites**, **rejeitado**, e **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | Estado de confirmação. Valores permitidos são **recebidos**, **Generated**, e **enviados**. (Obrigatório) |
| TA102 | Cadeia | Intercâmbio data. (Opcional) |
| ta103 | Cadeia | Intercâmbio do tempo. (Opcional) |
| ta105 | Cadeia | Intercâmbio nota código. (Opcional) |

## <a name="x12-functional-group-tracking-schema"></a>Esquema de controlo de grupo X12 funcional
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia | Nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia | Nome do parceiro do recetor da mensagem X12. (Opcional) |
| senderQualifier | Cadeia | Envie o qualificador de parceiro. (Obrigatório) |
| senderIdentifier | Cadeia | Envie o identificador de parceiro. (Obrigatório) |
| receiverQualifier | Cadeia | Receba o qualificador de parceiro. (Obrigatório) |
| receiverIdentifier | Cadeia | Receba o identificador de parceiro. (Obrigatório) |
| agreementName | Cadeia | Nome do X12 contrato ao qual as mensagens são resolvidas. (Opcional) |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia | Intercâmbio do número de controlo. (Opcional) |
| functionalGroupControlNumber | Cadeia | Número de controlo funcional. (Opcional) |
| gsSegment | Cadeia | Segmento de mensagem GS. (Opcional) |
| isTechnicalAcknowledgmentExpected | Valor booleano | Indica se a confirmação técnica está configurada no X12 contrato. (Obrigatório) |
| isFunctionalAcknowledgmentExpected | Valor booleano | Indica se a confirmação funcional está configurada no X12 contrato. (Obrigatório) |
| isMessageFailed | Valor booleano | Se o X12 mensagem de falha. (Obrigatório)|
| gs01 | Cadeia | Código de identificador funcional. (Opcional) |
| gs02 | Cadeia | Código do remetente de aplicação. (Opcional) |
| gs03 | Cadeia | Código do recetor da aplicação. (Opcional) |
| gs04 | Cadeia | Data de grupo funcional. (Opcional) |
| gs05 | Cadeia | Hora do grupo de funcional. (Opcional) |
| gs07 | Cadeia | Código de agência responsável. (Opcional) |
| gs08 | Cadeia | Código do identificador da versão/versão/da indústria. (Opcional) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Esquema de controlo de confirmação de grupo X12 funcional
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia | Nome do parceiro do remetente da mensagem X12. (Opcional) |
| receiverPartnerName | Cadeia | Nome do parceiro do recetor da mensagem X12. (Opcional) |
| senderQualifier | Cadeia | Envie o qualificador de parceiro. (Obrigatório) |
| senderIdentifier | Cadeia | Envie o identificador de parceiro. (Obrigatório) |
| receiverQualifier | Cadeia | Receba o qualificador de parceiro. (Obrigatório) |
| receiverIdentifier | Cadeia | Receba o identificador de parceiro. (Obrigatório) |
| agreementName | Cadeia | Nome do X12 contrato ao qual as mensagens são resolvidas. (Opcional) |
| direção | Enum | Direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| interchangeControlNumber | Cadeia | Intercâmbio do número de controlo, o que preenche para o lado de envio quando é recebida uma confirmação técnica de parceiros. (Opcional) |
| functionalGroupControlNumber | Cadeia | Número de controlo de grupo funcional de confirmação técnica, o que preenche para o lado de envio quando é recebida uma confirmação técnica de parceiros. (Opcional) |
| isaSegment | Cadeia | Mesmo que intercâmbio controlam o número, mas preenchidos apenas em casos específicos. (Opcional) |
| gsSegment | Cadeia | Mesmo que funcional controlam o número, mas preenchidos apenas em casos específicos. (Opcional) |
| respondingfunctionalGroupControlNumber | Cadeia | Número de controlo do grupo funcional original. (Opcional) |
| respondingFunctionalGroupId | Cadeia | ID do Maps para AK101 no grupo de funcional de confirmação. (Opcional) |
| isMessageFailed | Valor booleano | Se o X12 mensagem de falha. (Obrigatório) |
| statusCode | Enum | Código de estado de confirmação. Valores permitidos são **aceites**, **rejeitado**, e **AcceptedWithErrors**. (Obrigatório) |
| processingStatus | Enum | Estado de processamento da confirmação. Valores permitidos são **recebidos**, **Generated**, e **enviados**. (Obrigatório) |
| ak903 | Cadeia | Número de conjuntos de transação recebida. (Opcional) |
| ak904 | Cadeia | Número de conjuntos de transação de aceite no grupo de identificados funcional. (Opcional) |
| ak9Segment | Cadeia | Se o grupo de funcional identificado no segmento AK1 seja aceite ou rejeitado e, por isso. (Opcional) |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [monitorização mensagens B2B](logic-apps-monitor-b2b-message.md).
* Saiba mais sobre [esquemas de controlo AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* Saiba mais sobre [B2B esquemas de controlo personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* Saiba mais sobre [controlo mensagens B2B no portal do Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Saiba mais sobre o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).  

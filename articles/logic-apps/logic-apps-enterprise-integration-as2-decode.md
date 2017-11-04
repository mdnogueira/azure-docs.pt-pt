---
title: "Descodificar AS2 mensagens – Azure Logic Apps | Microsoft Docs"
description: Como utilizar o descodificador AS2 no Enterprise Integration Pack para o Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: a7920b2509fe368c6f7d55e17fe0bf0020c4562c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificar AS2 mensagens para o Azure Logic Apps com o pacote de integração do Enterprise 

Para estabelecer a segurança e fiabilidade durante a transmissão de mensagens, utilize o conector de mensagem de descodificar AS2. Este conector fornece a assinatura digital, desencriptação e confirmações das notificações de disposição através de mensagem (MDN).

## <a name="before-you-start"></a>Antes de começar

Segue-se os itens que precisa de:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Um [conta integração](logic-apps-enterprise-integration-create-integration-account.md) que já foi definida e associados à subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem de descodificar AS2.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já são definidas na sua conta de integração
* Um [contratos AS2](logic-apps-enterprise-integration-as2.md) que já está definido na sua conta de integração

## <a name="decode-as2-messages"></a>Descodificar mensagens AS2

1. [Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

2. O conector de mensagem de descodificar AS2 não tem acionadores, pelo que tem de adicionar um acionador para iniciar a sua aplicação lógica, como um acionador pedido. No Designer de aplicação lógica, adicione um acionador e, em seguida, adicionar uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "AS2" para o filtro. Selecione **AS2 - mensagem descodificar AS2**.
   
    ![Procure "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Se não tiver criado anteriormente todas as ligações à sua conta de integração, é-lhe pedido que criar essa ligação agora. A ligação de nome e selecione a conta de integração que pretende ligar.
   
    ![Criar a ligação de integração](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza um nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a aplicação de conta e a lógica de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, os detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para concluir a criação da ligação, escolha **criar**.

    ![detalhes de ligação de integração](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Depois de criar a ligação, conforme mostrado neste exemplo, selecione **corpo** e **cabeçalhos** das saídas de pedido.
   
    ![ligação de integração criada](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Por exemplo:

    ![Selecione corpo e cabeçalhos de pedido saídas](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Detalhes de descodificador AS2

O conector AS2 descodificar executa estas tarefas: 

* Processa os cabeçalhos de AS2/HTTP
* Verifica a assinatura (se configurada)
* Desencriptar as mensagens (se configurada)
* Descomprime a mensagem (se configurada)
* Reconcilia um MDN recebida com a mensagem de saída original
* Atualizações e correlaciona registos na base de dados não rejeição
* Escreve registos para relatórios de estado de AS2
* O conteúdo do payload de saída é codificado em base64
* Determina se um MDN é necessário e se o MDN deve ser síncrona ou assíncrona com base na configuração no contrato AS2
* Gera um MDN síncrona ou assíncrona (com base nas configurações de contrato)
* Define as propriedades e tokens de correlação do MDN

## <a name="try-this-sample"></a>Repita este exemplo

Para tentar implementar um cenário de AS2 totalmente operacional logic app e exemplos, consulte o [AS2 cenário e do modelo de aplicação lógica](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Ver o swagger
Consulte o [swagger detalhes](/connectors/as2/). 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o pacote de integração do Enterprise](logic-apps-enterprise-integration-overview.md) 


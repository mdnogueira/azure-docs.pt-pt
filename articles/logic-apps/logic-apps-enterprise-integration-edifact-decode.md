---
title: Descodificar mensagens EDIFACT - Azure Logic Apps | Microsoft Docs
description: "Validar EDI e gerar confirmações de receção com o descodificador de mensagem EDIFACT na Enterprise Integration Pack para o Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: e3787b48037360bf6066ddce2bacba6842213b2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificar mensagens EDIFACT para Azure Logic Apps com o pacote de integração do Enterprise

Com o conector de mensagem de descodificar EDIFACT, pode validar EDI e propriedades específicos de parceiro, dividir interchanges para conjuntos de transações ou manter todos interchanges e gerar em que as confirmações para transações processadas. Para utilizar este conector, tem de adicionar o conector para um acionador existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Segue-se os itens que precisa de:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Um [conta integração](logic-apps-enterprise-integration-create-integration-account.md) que já foi definida e associados à subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem de descodificar EDIFACT. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já são definidas na sua conta de integração
* Um [contratos EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido na sua conta de integração

## <a name="decode-edifact-messages"></a>Descodificar mensagens EDIFACT

1. [Criar uma aplicação lógica](logic-apps-create-a-logic-app.md).

2. O conector de mensagem de descodificar EDIFACT não tem acionadores, pelo que tem de adicionar um acionador para iniciar a sua aplicação lógica, como um acionador pedido. No Designer de aplicação lógica, adicione um acionador e, em seguida, adicionar uma ação à sua aplicação lógica.

3. Na caixa de pesquisa, introduza "EDIFACT" como o filtro. Selecione **descodificar a mensagem EDIFACT**.
   
    ![EDIFACT de pesquisa](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Se não tiver criado anteriormente todas as ligações à sua conta de integração, é-lhe pedido que criar essa ligação agora. A ligação de nome e selecione a conta de integração que pretende ligar.
   
    ![criar conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza um nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a aplicação de conta e a lógica de integração estão na mesma localização do Azure. |

4. Quando estiver pronto para concluir a criar a ligação, escolha **criar**. Os detalhes de ligação devem ter um aspeto semelhantes a este exemplo:

    ![Detalhes da conta de integração](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Depois de criar a ligação, conforme mostrado neste exemplo, selecione a mensagem de ficheiro simples EDIFACT ao descodificar.

    ![ligação de conta de integração criada](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Por exemplo:

    ![Selecione a mensagem de ficheiro simples EDIFACT para descodificação](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detalhes de descodificador EDIFACT

O conector de descodificar EDIFACT executa estas tarefas: 

* Valida o envelope contra comerciais contrato para parceiros.
* Resolve o contrato por correspondente o qualificador de remetente identificador e o qualificador de recetor & & Identificador.
* Divide um intercâmbio em várias transações quando o intercâmbio tem mais de uma transação com base no contrato de receber a configuração de definições.
* Disassembles o intercâmbio.
* Valida EDI e propriedades específicos de parceiros, incluindo:
  * Validação da estrutura de envelope intercâmbio
  * Validação de esquema do envelope contra o esquema de controlo
  * Validação de esquema dos elementos de conjunto de transação de dados contra o esquema de mensagens
  * A validação de EDI efetuada em elementos de dados do conjunto de transação
* Verifica se os números de controlo de conjunto intercâmbio, grupo e a transação não são duplicados (se configurada) 
  * Verifica o número de controlo de intercâmbio contra interchanges anteriormente recebidos. 
  * Verifica o número de controlo de grupo em relação a outros números de controlo de grupo no intercâmbio. 
  * Verifica que a transação definir o número de controlo em relação a outros números de controlo do conjunto de transação nesse grupo.
* Divide o intercâmbio para conjuntos de transação ou preserva o intercâmbio completo:
  * Intercâmbio de divisão como conjuntos de transação - suspender conjuntos de transação com o erro: divisões intercâmbio numa transação define e analisa cada conjunto de transação. 
  X12 Decode ação produz apenas dessas transações define que falharem a validação para `badMessages`e saídas define as transações restantes para `goodMessages`.
  * Intercâmbio de divisão como conjuntos de transação - suspender intercâmbio com o erro: divisões intercâmbio numa transação define e analisa cada conjunto de transação. 
  Se um ou mais transações define o intercâmbio falharem a validação, X12 Decode ação produz define a todas as transações em que intercâmbio para `badMessages`.
  * Preservar intercâmbio - suspender conjuntos de transação com o erro: preservar o intercâmbio e processar o intercâmbio de batch de todo. 
  X12 Decode ação produz apenas dessas transações define que falharem a validação para `badMessages`e saídas define as transações restantes para `goodMessages`.
  * Preservar intercâmbio - suspender intercâmbio com o erro: preservar o intercâmbio e processar o intercâmbio de batch de todo. 
  Se um ou mais transações define o intercâmbio falharem a validação, X12 Decode ação produz define a todas as transações em que intercâmbio para `badMessages`.
* Gera uma técnica (controlo) e/ou a confirmação funcional (se configurada).
  * Uma confirmação técnica ou o ACK CONTRL reporta os resultados de uma verificação syntactical do intercâmbio recebida concluída.
  * Uma confirmação funcional reconhece aceitar ou rejeitar um intercâmbio recebido ou um grupo

## <a name="view-swagger-file"></a>Ver o ficheiro Swagger
Para ver os detalhes do Swagger para o conector EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise") 


---
title: "Descodificar X12 mensagens – Azure Logic Apps | Microsoft Docs"
description: "Validar EDI e gerar confirmações de receção com o X12 descodificador de mensagem no pacote de integração do Enterprise para o Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 9605fc3a1096d053bfeffb2544499935601b2c0f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Descodificar X12 mensagens para o Azure Logic Apps com o pacote de integração do Enterprise

Com o conector de mensagem Decode X12, pode validar o envelope contra um contrato de parceiro comercial, validar EDI e propriedades específicos de parceiro, dividir interchanges para conjuntos de transações ou manter todos interchanges e gerar em que as confirmações para transações processadas. Para utilizar este conector, tem de adicionar o conector para um acionador existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Segue-se os itens que precisa de:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Um [conta integração](logic-apps-enterprise-integration-create-integration-account.md) que já foi definida e associados à subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem Decode X12.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já são definidas na sua conta de integração
* Um [X12 contrato](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

## <a name="decode-x12-messages"></a>Descodificar X12 mensagens

1. [Criar uma aplicação lógica](logic-apps-create-a-logic-app.md).

2. O conector de mensagem Decode X12 não tem acionadores, pelo que tem de adicionar um acionador para iniciar a sua aplicação lógica, como um acionador pedido. No Designer de aplicação lógica, adicione um acionador e, em seguida, adicionar uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "x12" para o filtro. Selecione **X12-descodificar X12 mensagem**.
   
    ![Procure "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se não tiver criado anteriormente todas as ligações à sua conta de integração, é-lhe pedido que criar essa ligação agora. A ligação de nome e selecione a conta de integração que pretende ligar. 

    ![Forneça detalhes da conta de ligação de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza um nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a aplicação de conta e a lógica de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, os detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para concluir a criação da ligação, escolha **criar**.
   
    ![detalhes de ligação da conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Após a ligação é criada, conforme mostrado neste exemplo, selecione o X12 ficheiro simples mensagem ao descodificar.

    ![ligação de conta de integração criada](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Por exemplo:

    ![Selecione X12 simples mensagem de ficheiro para descodificação](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > O conteúdo da mensagem real ou payload para a matriz de mensagem, boa ou incorreta, é codificado em base64. Por isso, tem de especificar uma expressão que processa este conteúdo.
   > Eis um exemplo que processa o conteúdo como XML que pode introduzir na vista de código ou utilizando o construtor de expressão no designer.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Exemplo de conteúdo](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 descodificar detalhes

O X12 Decode conector executa estas tarefas:

* Valida o envelope contra comerciais contrato para parceiros
* Valida EDI e propriedades específicos de parceiro
  * Validação estrutural EDI e validação de esquema expandido
  * Validação da estrutura do envelope intercâmbio.
  * Validação de esquema do envelope contra o esquema do controlo.
  * Validação de esquema dos elementos de conjunto de transação de dados contra o esquema de mensagem.
  * A validação de EDI efetuada em elementos de dados do conjunto de transação 
* Verifica se os números de controlo de conjunto intercâmbio, grupo e a transação não são duplicados
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
* Gera uma confirmação técnica e/ou funcional (se configurada).
  * Gera uma confirmação técnicos como resultado da validação de cabeçalho. A confirmação técnica reporta o estado de processamento de um cabeçalho de intercâmbio e trailer pelo recetor endereço.
  * Gera uma confirmação funcional como resultado da validação de corpo. A confirmação funcional relatórios cada erro encontrado ao processar o documento recebido

## <a name="view-the-swagger"></a>Ver o swagger
Consulte o [swagger detalhes](/connectors/x12/). 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise") 


---
title: "Codificar X12 mensagens – Azure Logic Apps | Microsoft Docs"
description: "Validar EDI e converter com codificação XML mensagens com o X12 mensagem codificador no pacote de integração do Enterprise para o Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 29d19364b9a98e351c95f13e68a2e63b9f6439f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Codificar X12 mensagens para o Azure Logic Apps com o pacote de integração do Enterprise

Com o conector de mensagem de codificar X12, pode validar EDI e propriedades específicos de parceiro, converter mensagens com codificação XML em conjuntos de transação EDI o intercâmbio e pedir uma confirmação técnicas, funcionais de confirmação ou ambos.
Para utilizar este conector, tem de adicionar o conector para um acionador existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Segue-se os itens que precisa de:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Um [conta integração](logic-apps-enterprise-integration-create-integration-account.md) que já foi definida e associados à subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem de codificar X12.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já são definidas na sua conta de integração
* Um [X12 contrato](logic-apps-enterprise-integration-x12.md) que já está definido na sua conta de integração

## <a name="encode-x12-messages"></a>Codificar X12 mensagens

1. [Criar uma aplicação lógica](logic-apps-create-a-logic-app.md).

2. O conector de mensagem de codificar X12 não tem acionadores, pelo que tem de adicionar um acionador para iniciar a sua aplicação lógica, como um acionador pedido. No Designer de aplicação lógica, adicione um acionador e, em seguida, adicionar uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "x12" para o filtro. Selecione **X12-codifique para X12 mensagem ao nome do contrato** ou **X12-codifique para X12 mensagem ao identidades**.
   
    ![Procure "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Se não tiver criado anteriormente todas as ligações à sua conta de integração, é-lhe pedido que criar essa ligação agora. A ligação de nome e selecione a conta de integração que pretende ligar. 
   
    ![ligação de conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza um nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a aplicação de conta e a lógica de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, os detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para concluir a criação da ligação, escolha **criar**.

    ![ligação de conta de integração criada](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    A ligação está agora criada.

    ![detalhes de ligação da conta de integração](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Codificar X12 mensagens pelo nome do contrato

Se optar por codificar X12 mensagens pelo nome do contrato, abra o **nome de X12 contrato** lista, introduza ou selecione o X12 existente contrato. Introduza a mensagem XML para codificar.

![Introduza X12 nome do contrato e mensagem XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Codificar X12 mensagens por identidades

Se optar por codificar X12 mensagens por identidades, introduza o identificador do remetente, qualificador do remetente, identificador de recetor e qualificador de recetor como configurado na sua X12 contrato. Selecione a mensagem XML para codificar.
   
![Fornecer identidades para o remetente e o recetor, selecione de mensagem XML para codificar](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 codificar detalhes

O X12 codificar conector executa estas tarefas:

* Resolução de contrato por correspondência de propriedades de contexto do remetente e o recetor.
* Serializa o intercâmbio EDI, conversão de mensagens com codificação XML numa EDI os conjuntos de intercâmbio de transação.
* Aplica-se de segmentos de cabeçalho e trailer do conjunto de transação
* Gera um número de controlo do intercâmbio, um número de controlo de grupo e um número de controlo do conjunto de transação para cada intercâmbio de saída
* Substitui os separadores de dados payload
* Valida EDI e propriedades específicos de parceiro
  * Validação de esquema dos elementos de conjunto de transação de dados contra a mensagem de esquema
  * Validação de EDI efetuada em elementos de conjunto de transação de dados.
  * A validação expandida efetuada em elementos de dados do conjunto de transação
* Solicita uma confirmação técnica e/ou funcional (se configurada).
  * Gera uma confirmação técnicos como resultado da validação de cabeçalho. A confirmação técnica reporta o estado de processamento de um cabeçalho de intercâmbio e trailer pelo recetor de endereço
  * Gera uma confirmação funcional como resultado da validação de corpo. A confirmação funcional relatórios cada erro encontrado ao processar o documento recebido

## <a name="view-the-swagger"></a>Ver o swagger
Consulte o [swagger detalhes](/connectors/x12/). 

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise") 


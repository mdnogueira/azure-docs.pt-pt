---
title: Codificar mensagens EDIFACT - Azure Logic Apps | Microsoft Docs
description: Validar EDI e gerar XML com o codificador de mensagens EDIFACT na Enterprise Integration Pack para o Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: b8d577326d23ec45cb4a9ec0e450ebf7afd945f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Codificar mensagens EDIFACT para Azure Logic Apps com o pacote de integração do Enterprise

Com o conector de mensagem de codificar EDIFACT, pode validar EDI e propriedades específicos de parceiro, gerar um documento XML para cada conjunto de transação e pedir uma confirmação técnicas, funcionais de confirmação ou ambos.
Para utilizar este conector, tem de adicionar o conector para um acionador existente na sua aplicação lógica.

## <a name="before-you-start"></a>Antes de começar

Segue-se os itens que precisa de:

* Uma conta do Azure; Pode criar um [conta gratuita](https://azure.microsoft.com/free)
* Um [conta integração](logic-apps-enterprise-integration-create-integration-account.md) que já foi definida e associados à subscrição do Azure. Tem de ter uma conta de integração para utilizar o conector de mensagem EDIFACT de codificar. 
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já são definidas na sua conta de integração
* Um [contratos EDIFACT](logic-apps-enterprise-integration-edifact.md) que já está definido na sua conta de integração

## <a name="encode-edifact-messages"></a>Codificar mensagens EDIFACT

1. [Criar uma aplicação lógica](logic-apps-create-a-logic-app.md).

2. O conector de mensagem de codificar EDIFACT não tem acionadores, pelo que tem de adicionar um acionador para iniciar a sua aplicação lógica, como um acionador pedido. No Designer de aplicação lógica, adicione um acionador e, em seguida, adicionar uma ação à sua aplicação lógica.

3.  Na caixa de pesquisa, introduza "EDIFACT" como o filtro. Selecione **codificar a mensagem EDIFACT pelo nome do contrato** ou **codificar a mensagem EDIFACT por identidades**.
   
    ![EDIFACT de pesquisa](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Se não tiver criado anteriormente todas as ligações à sua conta de integração, é-lhe pedido que criar essa ligação agora. A ligação de nome e selecione a conta de integração que pretende ligar.

    ![criar a ligação de conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Propriedades com um asterisco são necessárias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da ligação * |Introduza um nome para a sua ligação. |
    | Conta de integração * |Introduza um nome para a sua conta de integração. Certifique-se de que a aplicação de conta e a lógica de integração estão na mesma localização do Azure. |

5.  Quando tiver terminado, os detalhes de ligação devem ter um aspeto semelhantes a este exemplo. Para concluir a criação da ligação, escolha **criar**.

    ![detalhes de ligação da conta de integração](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    A ligação está agora criada.

    ![ligação de conta de integração criada](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Codificar a mensagem de EDIFACT pelo nome do contrato

Se tiver escolhido codificar mensagens EDIFACT pelo nome do contrato, abra o **contrato de nome de EDIFACT** lista, introduza ou selecione o nome do contrato EDIFACT. Introduza a mensagem XML para codificar.

![Introduza o nome do contrato EDIFACT e mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Codificar a mensagem de EDIFACT por identidades

Se optar por codificar mensagens EDIFACT por identidades, introduza o identificador do remetente, qualificador do remetente, identificador de recetor e qualificador de recetor como configurado na sua contratos EDIFACT. Selecione a mensagem XML para codificar.

![Fornecer identidades para o remetente e o recetor, selecione de mensagem XML para codificar](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT codificar detalhes

O conector de codificar EDIFACT executa estas tarefas: 

* Resolva o contrato de correspondência o qualificador do remetente & identificador e o qualificador de recetor e identificador
* Serializa o intercâmbio EDI, conversão de mensagens com codificação XML numa EDI os conjuntos de intercâmbio de transação.
* Aplica-se de segmentos de cabeçalho e trailer do conjunto de transação
* Gera um número de controlo do intercâmbio, um número de controlo de grupo e um número de controlo do conjunto de transação para cada intercâmbio de saída
* Substitui os separadores de dados payload
* Valida EDI e propriedades específicos de parceiro
  * Validação de esquema dos elementos de conjunto de transação de dados contra o esquema de mensagem.
  * Validação de EDI efetuada em elementos de conjunto de transação de dados.
  * A validação expandida efetuada em elementos de dados do conjunto de transação
* Gera um documento XML para cada conjunto de transação.
* Solicita uma confirmação técnica e/ou funcional (se configurada).
  * Como uma confirmação técnica, a mensagem CONTRL indica a receção de um intercâmbio.
  * Como uma confirmação funcional, a mensagem CONTRL indica a aceitação ou rejeição da intercâmbio recebido, grupo ou mensagem, com uma lista de erros ou funcionalidades não suportadas

## <a name="view-swagger-file"></a>Ver o ficheiro Swagger
Para ver os detalhes do Swagger para o conector EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração do Enterprise") 


---
title: "Criar soluções de B2B - Azure Logic Apps | Microsoft Docs"
description: "Receber dados em logic apps, utilizando as funcionalidades de B2B no pacote de integração do Enterprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0625787ddcbc0091e70b111f687e25929720ad15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Receber dados em aplicações lógicas com as funcionalidades de B2B no pacote de integração do Enterprise

Depois de criar uma conta de integração com parceiros e de contratos, está pronto para criar um fluxo de trabalho do negócio para empresas (B2B) para a sua aplicação lógica com o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o AS2 e X12 ações, tem de ter uma conta de integração empresarial. Saiba [como criar um Enterprise Integration Account](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Criar uma aplicação lógica com conectores de B2B

Siga estes passos para criar uma aplicação de lógica de B2B que utiliza o AS2 e X12 ações para receber dados a partir de um parceiro comercial:

1. Criar uma aplicação lógica, em seguida, [associar aplicação à sua conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Adicionar um **pedido - pedido de HTTP um quando é recebido** acionador à sua aplicação lógica.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Para adicionar o **descodificar AS2** ação, selecione **adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Para filtrar todas as ações para aquela que pretende, introduza a palavra **as2** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Selecione o **AS2 - mensagem descodificar AS2** ação.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Adicionar o **corpo** que pretende utilizar como entrada. Neste exemplo, selecione o corpo do pedido HTTP que aciona a aplicação lógica. Ou introduza uma expressão que entradas cabeçalhos no **CABEÇALHOS** campo:

    @triggerOutputs() ['headers']

7. Adicionar necessários **cabeçalhos** para AS2, que se encontram nos cabeçalhos do pedido HTTP. Neste exemplo, selecione os cabeçalhos do pedido HTTP que acionam a aplicação lógica.

8. Agora, adicione a ação de mensagem Decode X12. Selecione **adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Para filtrar todas as ações para aquela que pretende, introduza a palavra **x12** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Selecione o **X12-descodificar X12 mensagem** ação.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Agora tem de especificar a entrada para esta ação. Esta entrada é o resultado da ação anterior AS2.

    O conteúdo da mensagem real está a ser um objeto JSON e é a codificação base64, pelo que tem de especificar uma expressão como entrada. 
    Introduza a seguinte expressão no **X12 simples ficheiro mensagem para DECODE** campo entrada:
    
    @base64ToString(body('Decode_AS2_message')? ['AS2Message']? ['Content'])

    Agora adicione passos ao descodificar o X12 dados receberam do parceiro comercial e itens de um objeto JSON de saída. 
    Para notificar o parceiro que os dados foram recebidos, pode enviar novamente uma resposta que contém a notificação de disposição de mensagem (MDN) o AS2 numa ação de resposta de HTTP.

12. Para adicionar o **resposta** ação, escolha **adicionar uma ação**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Para filtrar todas as ações para aquela que pretende, introduza a palavra **resposta** na caixa de pesquisa.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Selecione o **resposta** ação.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Para aceder a MDN da saída do **Decode X12 mensagem** ação, defina a resposta **corpo** campo com esta expressão:

    @base64ToString(body('Decode_AS2_message')? ['OutgoingMdn']? ['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Guarde o seu trabalho.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Terminou agora a configurar a sua aplicação de lógica de B2B. Uma aplicação do mundo real, pode querer armazenar o X12 descodificados dados de um linha de negócio (LOB) aplicação ou arquivo de dados. Para ligar as suas aplicações LOB e utilize estas APIs na sua aplicação lógica, pode adicionar mais ações ou escrever APIs personalizadas.

## <a name="features-and-use-cases"></a>Funcionalidades e casos de utilização

* O AS2 X12 descodificar e codificar ações permitem trocar dados entre comerciais parceiros ao utilizar protocolos padrão da indústria em logic apps.
* Para trocar dados com parceiros comerciais, pode utilizar o AS2 e X12 com ou sem entre si.
* As ações de B2B ajudam a criar facilmente parceiros e contratos na sua conta de integração e aceder aos mesmos numa aplicação lógica.
* Quando expande a sua aplicação lógica com outras ações, pode enviar e receber dados, entre outras aplicações e serviços como o SalesForce.

## <a name="learn-more"></a>Saiba mais
[Saiba mais sobre o pacote de integração do Enterprise](logic-apps-enterprise-integration-overview.md)

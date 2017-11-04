---
title: "Mudar a instrução de ações diferentes Azure Logic Apps | Microsoft Docs"
description: "Escolha as diferentes ações para executar aplicações lógicas com base nos valores da expressão utilizando uma instrução de comutador"
services: logic-apps
keywords: "Falha da instrução"
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: 338b6a5b549d7bf81186550295608438ac4aee32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>Efetuar ações diferentes aplicações lógicas com uma instrução de comutador

Durante a criação de um fluxo de trabalho, muitas vezes, tem de efetuar ações diferentes com base no valor de um objeto ou expressão. Por exemplo, é aconselhável a aplicação lógica para comportar-se de forma diferente, consoante o código de estado de um pedido de HTTP ou uma opção selecionada numa mensagem de e-mail.

Pode utilizar uma instrução para implementar estes cenários. A aplicação lógica pode avaliar a expressão ou um token e escolha as maiúsculas e minúsculas com o mesmo valor para executar a ação especificada. Apenas um cenário deve corresponder da instrução switch.

> [!TIP]
> Como todos os idiomas de programação, declarações de comutador suportam apenas os operadores de igualdade. Se precisar de outros operadores relacionais, tais como "superior", utilize uma instrução de condição.
> Para garantir um comportamento de execução determinista, casos tem de conter um valor exclusivo e estático em vez de tokens dinâmicos ou expressão.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure Active Directory, [criar uma conta gratuita](https://azure.microsoft.com/free/), ou tente [Logic Apps para libertar](https://tryappservice.azure.com/).
- [Conhecimentos básicos sobre as logic apps](logic-apps-what-are-logic-apps.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>Adicione uma instrução de comutador para o fluxo de trabalho

Para mostrar como funciona uma instrução de comutador, este exemplo cria uma aplicação lógica que monitoriza os ficheiros carregados Dropbox. Quando os novos ficheiros são carregados, a aplicação lógica envia e-mail para um aprovador que escolhe se pretende transferir os ficheiros para SharePoint. A aplicação utiliza uma instrução de comutador que executa as ações diferentes com base no valor que seleciona o aprovador.

1. Criar uma aplicação lógica e selecione este acionador: **Dropbox - quando é criado um ficheiro**.

   ![Utilizar o Dropbox - quando é criado um ficheiro acionador](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. Sob o acionador, adicione esta ação: **Outlook.com - enviar e-mail de aprovação**

   > [!TIP]
   > As Logic apps também suportam cenários envio de correio eletrónico de aprovação de uma conta do Outlook do Office 365.

   - Se não tiver uma ligação existente, é-lhe pedido que crie uma.
   - Preencha os campos obrigatórios. Por exemplo, em **para**, especifique o endereço de correio eletrónico para enviar a mensagem de e-mail do aprovador.
   - Em **User Options**, introduza `Approve, Reject`.

   ![Configurar a ligação](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Adicione uma instrução de comutador.

   - Selecione **+ novo passo** > **... Mais** > **adicionar minúsculas**. 
   - Agora que pretende selecionar a ação a executar com base no `SelectedOptions` o resultado do *enviar e-mail de aprovação* ação. 
   Pode encontrar este campo no **adicionar conteúdo dinâmico** Seletor.
   - Utilize *caso 1* para processar Quando seleciona o aprovador `Approve`.
     - Se for aprovado, copie o ficheiro original ao SharePoint Online com o [ **SharePoint Online - criar o ficheiro** ação](../connectors/connectors-create-api-sharepointonline.md).
     - Adicione outra ação dentro as maiúsculas e minúsculas para notificar os utilizadores que um novo ficheiro está disponível no SharePoint.
   - Adicionar outro caso para processar quando o utilizador seleciona `Reject`.
     - Se rejeitado, envie um e-mail de notificação informa outros aprovadores que o ficheiro é rejeitado e é necessária nenhuma ação adicional.
   - `SelectedOptions`Fornece apenas duas opções, pelo que iremos pode deixar o **predefinido** caso vazio.

   ![Falha da instrução](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > Uma instrução de comutador necessita de, pelo menos, um cenário para além das maiúsculas e minúsculas de predefinição.

4. Depois da instrução switch, elimine o ficheiro original carregado para a Dropbox ao adicionar esta ação: **Dropbox - eliminar ficheiro**

5. Guarde a sua aplicação lógica. Teste a aplicação através do carregamento de um ficheiro para a Dropbox. Deverá receber um e-mail de aprovação em breve. Selecione uma opção e observar o comportamento.

   > [!TIP]
   > Verificar como [monitorizar as logic apps](logic-apps-monitor-your-logic-apps.md).

## <a name="understand-the-code-behind-switch-statements"></a>Compreender o code-behind declarações de comutador

Vamos ver agora que criou com êxito uma aplicação lógica, utilizando uma instrução de comutador, a definição de código atrás da instrução switch.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

* `"Switch"`é o nome da instrução switch, que pode mudar o nome de para legibilidade. 
* `"type": "Switch"`indica que a ação de uma instrução de comutador. 
* `"expression"`opção de selecionado o aprovador neste exemplo e é avaliada em comparação com cada caso declarado mais tarde na definição do. 
* `"cases"`pode conter qualquer número de cenários. Para cada caso, `"Case *"` é o nome predefinido do cenário, o que pode mudar o nome de para legibilidade. 
`"case"`Especifica a etiqueta de maiúsculas, que utiliza a switch expression para comparação, e tem de ser um valor constante e é exclusivo. Se nenhum dos cenários de corresponder à expressão comutador, ações em `"default"` são executados.

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e ver o que os outros utilizadores do Azure Logic Apps estão a fazer, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar o Azure Logic Apps e os conectores, vote ou submeta ideais no [site de comentários dos utilizadores do Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [adicionar condições](logic-apps-use-logic-app-features.md)
- Saiba mais sobre [erros e de processamento de exceções](logic-apps-exception-handling.md)
- Explorar mais [capacidades de idioma do fluxo de trabalho](logic-apps-author-definitions.md)
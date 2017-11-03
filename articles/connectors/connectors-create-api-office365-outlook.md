---
title: Adicione o conector do Outlook do Office 365 nas suas Logic Apps | Microsoft Docs
description: "Crie aplicações lógicas com o conector do Office 365 para permitir a interação com o Office 365. Por exemplo: criar, editar e atualizar itens de calendário e os contactos."
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 5335dae62e61659b68e8befb4ed0d404dffb800c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Começar a utilizar o conector do Outlook do Office 365
O conector do Outlook do Office 365 permite a interação com o Outlook no Office 365. Utilize este conector para criar, editar, contactos e itens de calendário, atualização e também obter, enviar e responder ao e-mail.

Com o Outlook do Office 365, pode:

* Crie o fluxo de trabalho utilizando as funcionalidades de e-mail e calendário dentro do Office 365. 
* Utilize acionadores para iniciar o fluxo de trabalho quando existe um novo e-mail, quando um item de calendário é atualizado e muito mais.
* Utilize ações para enviar uma mensagem de e-mail, crie um novo evento de calendário e muito mais. Por exemplo, quando existe um novo objeto no Salesforce (um acionador), envie um e-mail para o Office 365 Outlook (uma ação). 

Este tópico mostra-lhe como utilizar o conector do Outlook do Office 365 numa aplicação lógica e também apresenta uma lista de acionadores e ações.

> [!NOTE]
> Esta versão do artigo aplica-se a disponibilidade de aplicações lógicas geral (DG).
> 
> 

Para saber mais sobre Logic Apps, consulte o artigo [que são logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) e [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-office-365"></a>Ligar ao Office 365
Antes da aplicação lógica pode aceder a qualquer serviço, tem primeiro de criar um *ligação* ao serviço. Uma ligação oferece a conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar ao Outlook do Office 365, terá primeiro um Office 365 *ligação*. Para criar uma ligação, introduza as credenciais que normalmente utiliza para aceder ao serviço que pretende ligar. Por isso, com o Outlook do Office 365, introduza as credenciais para a sua conta Office 365 para criar a ligação.

## <a name="create-the-connection"></a>Criar a ligação
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilizar um acionador
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação lógica. Acionadores "consultam" o serviço de um intervalo e a frequência com que pretende. [Saiba mais sobre acionadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Na aplicação lógica, escreva "do office 365" para obter uma lista dos acionadores:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Selecione **Outlook do Office 365 - quando um evento futuros está a começar em breve**. Se já existe uma ligação, em seguida, selecione um calendário de lista pendente.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Se lhe for pedido para iniciar sessão, em seguida, introduza o início de sessão nos detalhes para criar a ligação. [Criar a ligação](connectors-create-api-office365-outlook.md#create-the-connection) este tópico lista os passos. 
   
   > [!NOTE]
   > Neste exemplo, a aplicação lógica, é executado quando um evento de calendário é atualizado. Para ver os resultados deste acionador, adicione outra ação que envia-lhe uma mensagem de texto. Por exemplo, adicione o Twilio *enviar mensagem* ação que textos quando o evento de calendário é a partir de 15 minutos. 
   > 
   > 
3. Selecione o **editar** botão e defina o **frequência** e **intervalo** valores. Por exemplo, se quiser acionar a consultar a cada 15 minutos, em seguida, defina o **frequência** para **minuto**e defina o **intervalo** para **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Guardar** as suas alterações (canto superior esquerdo da barra de ferramentas). A aplicação lógica é guardada e pode ser ativada automaticamente.

## <a name="use-an-action"></a>Utilizar uma ação
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selecione o sinal de adição. Pode ver várias opções: **adicionar uma ação**, **adicionar uma condição**, ou um do **mais** opções.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Escolha **adicionar uma ação**.
3. Na caixa de texto, escreva "do office 365" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. No nosso exemplo, escolha **Outlook do Office 365 - criar contacte**. Se já existe uma ligação, em seguida, escolha o **ID da pasta**, **o nome fornecido**e outras propriedades:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Se lhe for pedido para obter as informações de ligação, em seguida, introduza os detalhes para criar a ligação. [Criar a ligação](connectors-create-api-office365-outlook.md#create-the-connection) neste tópico descreve estas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, vamos criar um novo contacto no Outlook do Office 365. Pode utilizar o resultado de Acionador de outro para criar o contacto. Por exemplo, adicione o SalesForce *quando é criado um objecto* acionador. Em seguida, adicione o Office 365 Outlook *criar contacte* ação que utiliza os campos do SalesForce para criar o novo contacto novo no Office 365. 
   > 
   > 
5. **Guardar** as suas alterações (canto superior esquerdo da barra de ferramentas). A aplicação lógica é guardada e pode ser ativada automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/office365connector/). 

## <a name="next-steps"></a>Passos Seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explorar os outros conectores disponíveis em Logic Apps no nosso [lista APIs](apis-list.md).


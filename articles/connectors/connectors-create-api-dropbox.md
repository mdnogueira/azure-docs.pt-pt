---
title: Conector de Dropbox no Azure Logic Apps | Microsoft Docs
description: "Crie aplicações lógicas com o App service do Azure. Ligar a Dropbox para gerir os seus ficheiros. Pode efetuar várias ações, como o carregamento, atualizar, obter e eliminar ficheiros da Dropbox."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0d09580c60fd620811b539147439d0922839fe7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-dropbox-connector"></a>Começar a utilizar o conector Dropbox
Ligar a Dropbox para gerir os seus ficheiros. Pode efetuar várias ações, como o carregamento, atualizar, obter e eliminar ficheiros da Dropbox.

Para utilizar [qualquer conector](apis-list.md), terá primeiro de criar uma aplicação lógica. Pode começar a utilizar pelo [criar uma aplicação lógica agora](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Ligar a Dropbox
Antes da aplicação lógica pode aceder a qualquer serviço, terá primeiro de criar um *ligação* ao serviço. Uma ligação oferece a conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar a Dropbox, primeiro precisa de um Dropbox *ligação*. Para criar uma ligação, terá de fornecer as credenciais que normalmente utiliza para aceder ao serviço que pretende ligar. Por isso, no exemplo Dropbox, terá as credenciais para a sua conta Dropbox para criar a ligação ao Dropbox. [Saiba mais sobre ligações]()

### <a name="create-a-connection-to-dropbox"></a>Criar uma ligação a Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Utilizar um acionador Dropbox
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre acionadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, utilizaremos o **quando é criado um ficheiro** acionador. Quando ocorre este acionador, que iremos chamar o **obter conteúdo de ficheiros utilizando caminho** ação Dropbox. 

1. Introduza *dropbox* na caixa de pesquisa no estruturador de Logic Apps, em seguida, selecione o **Dropbox - quando é criado um ficheiro** acionador.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Selecione a pasta na qual pretende controlar a criação de ficheiros. Selecione... (identificado na caixa vermelha) e navegue para a pasta que pretende selecionar para o acionador de entrada.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Utilizar uma ação de Dropbox
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Agora que o acionador foi adicionado, siga estes passos para adicionar uma ação que irá obter conteúdo do novo ficheiro.

1. Selecione **+ novo passo** para adicionar a ação que gostaria de tomar quando é criado um novo ficheiro.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Selecione **adicionar uma ação**. Este é aberta a caixa de pesquisa, onde pode procurar qualquer ação pretende efetuar.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Introduza *dropbox* para procurar ações relacionadas com o Dropbox.  
4. Selecione **Dropbox - utilizar caminho de conteúdo do ficheiro de Get** como a ação a tomar quando é criado um novo ficheiro na pasta selecionada Dropbox. É aberto o bloco de controlo de ação. Será solicitado para autorizar a aplicação lógica para aceder à sua conta Dropbox se não o fez, anteriormente.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Selecione... (localizado no lado direito do **caminho do ficheiro** controlo) e navegue para o caminho do ficheiro que pretende utilizar. Em alternativa, utilizar o **caminho do ficheiro** token para acelerar a criação da aplicação lógica.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Guarde o trabalho e criar um novo ficheiro no Dropbox para ativar o fluxo de trabalho.  

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/dropbox/).

## <a name="more-connectors"></a>Mais conectores
Volte à [lista APIs](apis-list.md).
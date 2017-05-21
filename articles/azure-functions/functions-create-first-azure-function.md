---
title: "Criar a sua primeira função a partir do portal do Azure | Microsoft Docs"
description: "Saiba como criar a sua primeira Função do Azure para execução sem servidor através do portal do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: abd508631787ba5f839a4ae2ea82e76c4bfab425
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar a sua primeira função no portal do Azure

As Funções do Azure permitem-lhe executar o seu código num ambiente sem servidor, sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web. Neste tópico, aprenda a utilizar as Funções para criar uma função “hello world” no portal do Azure.

![Criar uma aplicação de função no portal do Azure](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Deverá demorar menos de cinco minutos para concluir todos os passos neste tópico.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. As aplicações App Function permitem-lhe agrupar funções como unidades lógicas para uma gestão, implementação e partilha de recursos mais fácil. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicação Function App criada com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-function"></a>Criar uma função acionada por HTTP

1. Expanda a aplicação Function App nova e clique no botão **+**, junto a **Functions**.

2.  Na página **Começar rapidamente**, clique em **WebHook + API**, escolha uma linguagem para a função e clique em **Criar esta função**. 
   
    ![Início rápido de funções no portal do Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

É criada uma função na linguagem que escolheu com o modelo de função acionada por HTTP. Pode enviar um pedido HTTP para executar a função nova.

## <a name="test-the-function"></a>Testar a função

1. Na função nova, clique en **</> Obter URL da função** e copie o **URL da Função**. 

    ![Copiar o URL da função a partir do portal do Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Cole o URL do pedido HTTP na barra de endereço do browser. Anexe a cadeia de consulta `&name=<yourname>` a este URL e execute o pedido. A imagem abaixo mostra a resposta no browser ao pedido GET devolvido pela função:

    ![Resposta da função no browser.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP.   

## <a name="view-the-function-logs"></a>Ver os registos da função 

Quando a sua função é executada, são escritas informações de rastreio nos registos. Para ver a saída de rastreio da execução anterior, regresse à função no portal e clique na seta para cima, na parte inferior do ecrã, para expandir **Registos**. 

![Visualizador de registo de funções no portal do Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma aplicação App Function com uma função acionada por HTTP simples.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, veja [Azure Functions HTTP and webhook bindings](functions-bindings-http-webhook.md) (Enlaces de HTTP e webhook das Funções do Azure).





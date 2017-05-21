---
title: "Criar uma função no Azure acionada por mensagens de fila | Microsoft Docs"
description: "Utilize as Funções do Azure para criar uma função sem servidores que é invocada por mensagens que são submetidas para filas do Armazenamento do Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b3910ba2f95bb6f3228926a51f71f96ccd1e23
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Criar uma função acionada pelo Armazenamento de filas do Azure

Saiba como criar funções que são acionadas quando são submetidas mensagens para uma fila do Armazenamento do Azure.  

![Ver mensagem nos registos.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

Deverá demorar menos de cinco minutos para concluir todos os passos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Também tem de transferir e instalar o [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Criar uma função acionada por fila

Expanda a aplicação Function App, clique no botão **+** junto a **Functions** e clique no modelo **QueueTrigger** da sua linguagem preferida. Em seguida, utilize as definições especificadas na tabela e clique em **Criar**.

![Crie a função acionada pela fila de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)
    
| Definição      |  Valor sugerido   | Descrição                                        |
| ------------ |  ----------------- | -------------------------------------------------- |
| **Nome da fila**   | myqueue-items    | O nome da fila à qual ligar na sua conta de Armazenamento. |
| **Ligação da conta de armazenamento** | AzureWebJobStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |
| **Dar um nome à função** | Exclusivo na aplicação Function App | O nome desta função acionada por fila. |  

Em seguida, vai ligar à sua conta de Armazenamento do Azure e criar a fila de armazenamento **myqueue-items**.

## <a name="create-the-queue"></a>Criar a fila

1. Na sua função, clique em **Integrar**, expanda **Documentação** e copie **Nome da conta** e **Chave da conta**. Vai utilizar estas credenciais para ligar à conta de armazenamento. Se já tiver ligado a sua conta de armazenamento, avance para o passo 4.
 
    ![Obtenha as credenciais de ligação da conta de Armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)v

2. Execute a ferramenta [Microsoft Azure Storage Explorer](http://storageexplorer.com/), clique no ícone de ligação à esquerda, escolha **Utilizar um nome e uma chave de conta de armazenamento** e clique em **Seguinte**.

    ![Execute a ferramenta Microsoft Azure Storage Explorer.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)
    
3. Introduza o **Nome da conta** e a **Chave da conta** do passo 1, clique em **Seguinte** e em **Ligar**. 
  
    ![Introduza as credenciais de armazenamento e ligue-se.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

4. Expanda a conta de armazenamento anexada, clique com o botão direito do rato em **Filas**, clique em **Criar fila**, escreva `myqueue-items` e prima Enter.
 
    ![Crie uma fila de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Agora que tem uma fila de armazenamento, pode adicionar uma mensagem à mesma para testar a função.  

## <a name="test-the-function"></a>Testar a função

1. Novamente no portal do Azure, navegue para a função, expanda os **Registos**, na parte inferior da página, e confirme que a transmissão de registos não está em pausa.

2. No Storage Explorer, expanda a conta de armazenamento, **Filas** e **myqueue-items** e clique em **Adicionar mensagem**. 

    ![Adicione uma mensagem à fila.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

2. Escreva a sua mensagem “Hello World” em **Texto da mensagem** e clique em **OK**.
 
3. Aguarde alguns segundos, regresse aos registos da sua função e certifique-se de que a mensagem nova foi lida a partir da fila. 

    ![Ver mensagem nos registos.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

4. Novamente no Storage Explorer, clique em **Atualizar** e verifique se a mensagem foi processada e já não está na fila.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada quando uma mensagem é adicionada a uma fila de armazenamento. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre os acionadores do Armazenamento de filas, veja [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Enlaces da fila de Armazenamento das Funções do Azure). 





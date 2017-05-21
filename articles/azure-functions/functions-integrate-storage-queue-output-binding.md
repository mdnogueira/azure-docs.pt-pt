---
title: "Criar uma função no Azure acionada por mensagens de fila | Microsoft Docs"
description: "Utilize as Funções do Azure para criar uma função sem servidores que é invocada por mensagens que são submetidas para filas do Armazenamento do Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0e2501b0eb218d3c8a62dd4959b08ff85ec565eb
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Utilizar as Funções para adicionar mensagens a uma fila do Armazenamento do Azure

Nas Funções do Azure, os enlaces de entrada e saída proporcionam uma forma declarativa para ligar aos dados do serviço externo a partir da sua função. Neste tópico, aprenda a atualizar funções existentes ao adicionar um enlace de saída que envia mensagens para o Armazenamento de filas do Azure.  

![Ver mensagem nos registos.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

Deverá demorar menos de cinco minutos para concluir todos os passos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

Também tem de transferir e instalar o [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="add-binding"></a>Adicionar um enlace de saída
 
1. Expanda a aplicação Function App e a função.

2. Clique em **Integrar** e em **+ Nova saída**, clique em **Armazenamento de filas do Azure** e clique em **Selecionar**.
    
    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Utilize as definições especificadas na tabela e clique em **Guardar**: 

    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Definição      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da fila**   | myqueue-items    | O nome da fila à qual ligar na sua conta de Armazenamento. |
    | **Ligação da conta de armazenamento** | AzureWebJobStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |
    | **Nome do parâmetro da mensagem** | outQueueItem | O nome do parâmetro de enlace de saída. | 

Agora que tem um enlace de saída definido, tem de atualizar o código para utilizar o enlace para adicionar mensagens a uma fila.  

## <a name="update-the-function-code"></a>Atualizar o código da função

1. Clique na sua função para apresentar o código da mesma no editor 

2. Para funções C#, atualize a definição da função da seguinte forma da seguinte forma, para adicionar o parâmetro de enlace de armazenamento **outQueueItem**. Ignore este passo para funções JavaScript.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Adicione o seguinte código à função imediatamente antes de o método ser devolvido. Utilize o fragmento adequado à linguagem da sua função.

    ```javascript
    context.bindings.outQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outQueueItem.Add("Name passed to the function: " + name);     
    ```

4. Clique em **Guardar** para guardar as alterações.

O valor transmitido ao acionador HTTP está incluído numa mensagem adicionada à fila.
 
## <a name="test-the-function"></a>Testar a função 

1. Depois de as alterações ao código serem guardadas, clique em **Executar**. 

    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Verifique os registos para se certificar de que a função foi bem-sucedida. Da primeira vez que o enlace de saída é utilizado, o runtime das Funções cria uma fila nova com o nome **outqueue** na sua conta de Armazenamento.

Em seguida, ligue à sua conta de armazenamento para verificar a fila nova e a mensagem que adicionou à mesma. 

## <a name="connect-to-the-queue"></a>Ligar à fila

Ignore os três primeiros passos se já tiver instalado o Storage Explorer e ligado à sua conta de armazenamento.    

1. Na sua função, clique em **Integrar** e no enlace de saída do **Armazenamento de filas do Azure** novo e expanda **Documentação**. Copie o **Nome da conta** e a **Chave da conta**. Vai utilizar estas credenciais para ligar à conta de armazenamento.
 
    ![Obtenha as credenciais de ligação da conta de Armazenamento.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Execute a ferramenta [Microsoft Azure Storage Explorer](http://storageexplorer.com/), clique no ícone de ligação à esquerda, escolha **Utilizar um nome e uma chave de conta de armazenamento** e clique em **Seguinte**.

    ![Execute a ferramenta Microsoft Azure Storage Explorer.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Introduza o **Nome da conta** e a **Chave da conta** do passo 1, clique em **Seguinte** e em **Ligar**. 
  
    ![Introduza as credenciais de armazenamento e ligue-se.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Expanda a conta de armazenamento anexada, clique com o botão direito do rato em **Filas** e confirme se existe uma fila com o nome **myqueue itens**. Também deverá ver uma mensagem já na fila.  
 
    ![Crie uma fila de armazenamento.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Adicionou um enlace de saída a uma função já existente. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre o enlace para o Armazenamento de filas, veja [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Enlaces da fila de Armazenamento das Funções do Azure). 





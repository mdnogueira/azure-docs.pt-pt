---
title: "Adicionar mensagens de uma fila de armazenamento do Azure utilizando funções | Microsoft Docs"
description: "Utilize as Funções do Azure para criar uma função sem servidores que é invocada por mensagens que são submetidas para filas do Armazenamento do Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 822879861ee8189cdd413f0061f26fb91819d88d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Utilizar as Funções para adicionar mensagens a uma fila do Armazenamento do Azure

Nas Funções do Azure, os enlaces de entrada e saída proporcionam uma forma declarativa para ligar aos dados do serviço externo a partir da sua função. Neste tópico, aprenda a atualizar funções existentes ao adicionar um enlace de saída que envia mensagens para o Armazenamento de filas do Azure.  

![Ver mensagem nos registos.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

* Instale o [Explorador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).

## <a name="add-binding"></a>Adicionar um enlace de saída
 
1. Expanda a aplicação Function App e a função.

2. Selecione **integrar** e **+ nova saída**, em seguida, escolha **armazenamento de filas do Azure** e escolha **selecione**.
    
    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Utilize as definições especificadas na tabela: 

    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Definição      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da fila**   | myqueue-items    | O nome da fila à qual ligar na sua conta de Armazenamento. |
    | **Ligação da conta de armazenamento** | AzureWebJobStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |
    | **Nome do parâmetro da mensagem** | outputQueueItem | O nome do parâmetro de enlace de saída. | 

4. Clique em **Guardar** para adicionar o enlace.
 
Agora que tem um enlace de saída definido, tem de atualizar o código para utilizar o enlace para adicionar mensagens a uma fila.  

## <a name="update-the-function-code"></a>Atualizar o código da função

1. Selecione a sua função para apresentar o código da mesma no editor. 

2. Para uma função de c#, atualizar a definição de função da seguinte forma para adicionar o **outputQueueItem** parâmetro de enlace de armazenamento. Ignore este passo para funções JavaScript.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outputQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Adicione o seguinte código à função imediatamente antes de o método ser devolvido. Utilize o fragmento adequado à linguagem da sua função.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);     
    ```

4. Selecione **Guardar** para guardar as alterações.

O valor transmitido ao acionador HTTP está incluído numa mensagem adicionada à fila.
 
## <a name="test-the-function"></a>Testar a função 

1. Depois de as alterações ao código serem guardadas, clique em **Executar**. 

    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Verifique os registos para se certificar de que a função foi bem-sucedida. Da primeira vez que o enlace de saída é utilizado, o runtime das Funções cria uma fila nova com o nome **outqueue** na sua conta de Armazenamento.

Em seguida, ligue à sua conta de armazenamento para verificar a fila nova e a mensagem que adicionou à mesma. 

## <a name="connect-to-the-queue"></a>Ligar à fila

Ignore os três primeiros passos se já tiver instalado o Storage Explorer e ligado à sua conta de armazenamento.    

1. Na sua função, escolha **integrar** e o novo **armazenamento de filas do Azure** vínculo de saída, em seguida, expanda **documentação**. Copie o **Nome da conta** e a **Chave da conta**. Vai utilizar estas credenciais para ligar à conta de armazenamento.
 
    ![Obtenha as credenciais de ligação da conta de Armazenamento.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Execute a ferramenta [Explorador de Armazenamento do Microsoft Azure](http://storageexplorer.com/), selecione o ícone de ligação à esquerda, escolha **Utilizar um nome e uma chave da conta de armazenamento** e selecione **Seguinte**.

    ![Execute a ferramenta Microsoft Azure Storage Explorer.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Cole o **Nome da conta** e a **Chave da conta** do passo 1 nos campos correspondentes e, em seguida, selecione **Seguinte** e **Ligar**. 
  
    ![Cole as credenciais de armazenamento e ligue-se.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Expanda a conta de armazenamento ligado, expanda **filas** e certifique-se de que existe uma fila com o nome **myqueue itens** existe. Também deverá ver uma mensagem já na fila.  
 
    ![Crie uma fila de armazenamento.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Adicionou um enlace de saída a uma função já existente. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre o enlace para o Armazenamento de filas, veja [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Enlaces da fila de Armazenamento das Funções do Azure). 




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
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d1ddfbe9a0a0c7c7e0a060776938bd68a87e1ba5
ms.contentlocale: pt-pt
ms.lasthandoff: 06/26/2017

---
<a id="add-messages-to-an-azure-storage-queue-using-functions" class="xliff"></a>

# Utilizar as Funções para adicionar mensagens a uma fila do Armazenamento do Azure

Nas Funções do Azure, os enlaces de entrada e saída proporcionam uma forma declarativa para ligar aos dados do serviço externo a partir da sua função. Neste tópico, aprenda a atualizar funções existentes ao adicionar um enlace de saída que envia mensagens para o Armazenamento de filas do Azure.  

![Ver mensagem nos registos.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

* Instale o [Explorador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="add-binding"></a>Adicionar um enlace de saída
 
1. Expanda a aplicação Function App e a função.

2. Selecione **Integrar** e **+ Nova saída**, selecione **Armazenamento de Filas do Azure** e selecione **Selecionar**.
    
    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Utilize as definições especificadas na tabela e selecione **Guardar**: 

    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Definição      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da fila**   | myqueue-items    | O nome da fila à qual ligar na sua conta de Armazenamento. |
    | **Ligação da conta de armazenamento** | AzureWebJobStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |
    | **Nome do parâmetro da mensagem** | outQueueItem | O nome do parâmetro de enlace de saída. | 

Agora que tem um enlace de saída definido, tem de atualizar o código para utilizar o enlace para adicionar mensagens a uma fila.  

<a id="update-the-function-code" class="xliff"></a>

## Atualizar o código da função

1. Selecione a sua função para apresentar o código da mesma no editor. 

2. Para funções C#, atualize a definição da função da seguinte forma, para adicionar o parâmetro de enlace de armazenamento **outQueueItem**. Ignore este passo para funções JavaScript.

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

4. Selecione **Guardar** para guardar as alterações.

O valor transmitido ao acionador HTTP está incluído numa mensagem adicionada à fila.
 
<a id="test-the-function" class="xliff"></a>

## Testar a função 

1. Depois de as alterações ao código serem guardadas, clique em **Executar**. 

    ![Adicione um enlace de saída do Armazenamento de filas a uma função no portal do Azure.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Verifique os registos para se certificar de que a função foi bem-sucedida. Da primeira vez que o enlace de saída é utilizado, o runtime das Funções cria uma fila nova com o nome **outqueue** na sua conta de Armazenamento.

Em seguida, ligue à sua conta de armazenamento para verificar a fila nova e a mensagem que adicionou à mesma. 

<a id="connect-to-the-queue" class="xliff"></a>

## Ligar à fila

Ignore os três primeiros passos se já tiver instalado o Storage Explorer e ligado à sua conta de armazenamento.    

1. Na sua função, selecione **Integrar** e o novo enlace de saída do **Armazenamento de Filas do Azure** e, em seguida, expanda **Documentação**. Copie o **Nome da conta** e a **Chave da conta**. Vai utilizar estas credenciais para ligar à conta de armazenamento.
 
    ![Obtenha as credenciais de ligação da conta de Armazenamento.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Execute a ferramenta [Explorador de Armazenamento do Microsoft Azure](http://storageexplorer.com/), selecione o ícone de ligação à esquerda, escolha **Utilizar um nome e uma chave da conta de armazenamento** e selecione **Seguinte**.

    ![Execute a ferramenta Microsoft Azure Storage Explorer.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Cole o **Nome da conta** e a **Chave da conta** do passo 1 nos campos correspondentes e, em seguida, selecione **Seguinte** e **Ligar**. 
  
    ![Cole as credenciais de armazenamento e ligue-se.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Expanda a conta de armazenamento anexada, clique com o botão direito do rato em **Filas** e confirme se existe uma fila com o nome **myqueue itens**. Também deverá ver uma mensagem já na fila.  
 
    ![Crie uma fila de armazenamento.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

<a id="clean-up-resources" class="xliff"></a>

## Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

<a id="next-steps" class="xliff"></a>

## Passos seguintes

Adicionou um enlace de saída a uma função já existente. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre o enlace para o Armazenamento de filas, veja [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Enlaces da fila de Armazenamento das Funções do Azure). 





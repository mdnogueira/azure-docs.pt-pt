---
title: "Criar uma Função do Azure vinculada a um serviço Azure | Microsoft Docs"
description: "Crie uma Função do Azure, uma aplicação sem servidor que interage com outros Serviços do Azure."
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Criar uma Função do Azure vinculada a um serviço Azure
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

Neste breve vídeo, vai aprender a criar uma Função do Azure que escuta as mensagens numa Fila do Azure e copia as mensagens para um Blob do Azure.

## <a name="watch-the-video"></a>Ver o vídeo
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>Criar uma função de acionador de fila de entrada
O objetivo desta função é escrever uma mensagem numa fila a cada 10 segundos. Para tal, tem de criar a função e filas de mensagens, e adicionar o código para escrever mensagens nas filas recentemente criadas.

1. Aceda ao Portal do Azure e localize a Function App do Azure.
2. Clique em **Nova Função** > **TimerTrigger - Node**. Atribua à função o nome **FunctionsBindingsDemo1**
3. Introduza um valor de "0/10 * * * * *" para a Agenda. Este valor está sob a forma de uma expressão cron. Esta ação agenda o temporizador para ser executado a cada 10 segundos.
4. Clique no botão **Criar** para criar a função.
   
    ![Adicionar funções de um temporizador de acionador](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. Verifique se a função funciona, visualizando atividade no registo. Poderá ter de clicar na hiperligação **Registos**, no canto superior direito, para apresentar o painel de registo.
   
   ![Verifique se a função funciona, visualizando o registo](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>Adicionar uma fila de mensagens
1. Aceda ao separador **Integrar**.
2. Escolha **Nova Saída** > **Fila de Armazenamento do Azure** > **Selecionar**.
3. Introduza **myQueueItem** na caixa de texto **Nome do parâmetro de mensagem**.
4. Selecione uma conta de armazenamento ou clique em **nova** para criar uma conta de armazenamento, caso não tenha uma.
5. Introduza **functions-bindings** na caixa de texto **Nome da fila**.
6. Clique em **Guardar**.  
   
   ![Adicionar funções de um temporizador de acionador](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>Escrever na fila de mensagens
1. Volte ao separador **Desenvolver** e adicione o seguinte código à função depois do código existente:
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. Modifique o código da função existente para chamar o código adicionado no Passo 1. Insira o seguinte código à volta da linha 9 da função, a seguir à instrução *if*.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    Este código cria um **myQueueItem** e define a respetiva propriedade **time** como o timeStamp atual. Em seguida, adiciona o novo item da fila para enlace de myQueue do contexto.
3. Clique em **Guardar e executar**.
4. Verifique se o código funciona, visualizando a fila no Visual Studio.
   
   * Abra o Visual Studio e aceda a **Ver** > **Nuvem** **Explorer**.
   * Localize a conta de armazenamento e a fila **functions-bindings** que utilizou quando criou a fila myQueue. Deverá ver linhas de dados de registo. Poderá ser necessário iniciar sessão no Azure através do Visual Studio.  

## <a name="create-an-output-queue-trigger-function"></a>Criar uma função de acionador de fila de saída
1. Clique em **Nova Função** > **QueueTrigger - C#**. Atribua à função o nome **FunctionsBindingsDemo2**. Tenha em atenção que pode combinar vários idiomas na mesma function app (Node e C# neste caso).
2. Introduza **functions-bindings** no campo **Nome da fila**.
3. Selecione uma conta de armazenamento para utilizar ou crie uma nova.
4. Clique em **Criar**.
5. Verifique se a nova função funciona, visualizando o registo da função e o Visual Studio relativamente à existência de atualizações. O registo da função mostra que a função está a ser executada e os itens estão removidos da fila. Uma vez que a função está vinculada à fila de saída **functions-bindings** como um acionador de entrada, atualizar a Fila **functions-bindings** no Visual Studio deve revelar que os itens desapareceram. Foram removidos da fila.   
   
   ![Adicionar funções de um temporizador de fila de saída](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>Modificar o tipo de item de fila de JSON para objeto
1. Substitua o código em **FunctionsBindingsDemo2** pelo código seguinte:    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    Este código adiciona duas classes, **TableItem** e **QItem**, que utiliza para ler e escrever em filas. Além disso, a função **Executar** foi modificada para aceitar o parâmetro **QItem** e **TraceWriter**, em vez de uma **cadeia** e um **TraceWriter**. 
2. Clique no botão **Guardar**.
3. Verifique se o código funciona, verificando o registo. Repare que as funções do Azure serializam e anulam a serialização do objeto automaticamente, facilitando o acesso à fila de uma forma orientada para objetos para passar dados. 

## <a name="store-messages-in-an-azure-table"></a>Armazenar mensagens numa Tabela do Azure
Agora que tem as filas a trabalhar em conjunto, está na altura de adicionar uma tabela do Azure para o armazenamento permanente dos dados da fila.

1. Aceda ao separador **Integrar**.
2. Crie uma Tabela de Armazenamento do Azure para Saída e dê-lhe o nome **myTable**.
3. Responda **functionsbindings** à pergunta "Em que tabela os dados devem ser escritos?".
4. Altere a definição **PartitionKey** de **{project-id}** para **{partition}**.
5. Escolha uma conta de armazenamento ou crie uma nova.
6. Clique em **Guardar**.
7. Aceda ao separador **Desenvolver**.
8. Crie uma classe **TableItem** para representar uma tabela do Azure e modifique a função Executar para aceitar o objeto TableItem recentemente criado. Tenha em atenção que tem de utilizar as propriedades **PartitionKey** e **RowKey** para que funcione.
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. Clique em **Guardar**.
10. Verifique se o código funciona, visualizando os registos da função e no Visual Studio. Para verificar no Visual Studio, utilize o **Cloud Explorer** para navegar até à Tabela do Azure **functionbindings** e verificar se existem linhas na mesma.

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->



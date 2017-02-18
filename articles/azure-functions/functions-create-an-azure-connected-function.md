---
title: "Criar uma função que se liga aos serviços do Azure | Microsoft Docs"
description: "Utilize as Funções do Azure para criar uma aplicação sem servidor que se liga a outros serviços do Azure."
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
ms.date: 01/23/2017
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: a8f6d111a010666bf4aaaf05e061381cc8fffed0
ms.openlocfilehash: 634781189459f26e2ffa42b25a2ffb229d3371d4


---
# <a name="use-azure-functions-to-create-a-function-that-connects-to-other-azure-services"></a>Utilizar as Funções do Azure para criar uma função que se liga a outros serviços do Azure

Este tópico mostra-lhe como criar uma função nas Funções do Azure que escuta as mensagens numa fila do Armazenamento do Azure e as copia para linhas numa tabela do Armazenamento do Azure. Uma função acionada por um temporizador é utilizada para carregar mensagens na fila. Uma segunda função lê a partir da fila e escreve mensagens na tabela. Tanto a fila como a tabela são criadas para si pelas Funções do Azure com base nas definições de enlace. 

Para tornar as coisas mais interessantes, uma função é escrita em JavaScript e a outra é escrita em script C#. Isto demonstra como uma aplicação de função pode ter funções em várias linguagens. 

Pode ver este cenário demonstrado num [vídeo no Canal 9](https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player).

## <a name="create-a-function-that-writes-to-the-queue"></a>Criar uma função que escreve na fila

Para poder ligar a uma fila de armazenamento, tem de criar primeiro uma função que carregue a fila de mensagens. Esta função de JavaScript utiliza um acionador de temporizador que escreve uma mensagem na fila cada 10 segundos. Se ainda não tiver uma conta do Azure, veja a experiência [Experimentar Funções do Azure](https://functions.azure.com/try) ou [crie a sua conta do Azure gratuita](https://azure.microsoft.com/free/).

1. Aceda ao portal do Azure e localize a sua Function App.

2. Clique em **Nova Função** > **TimerTrigger JavaScript**. 

3. Dê o nome **FunctionsBindingsDemo1** à função, introduza o valor de expressão cron `0/10 * * * * *` para **Agendar** e clique em **Criar**.
   
    ![Adicionar uma função acionada por temporizador](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)

    Criou uma função acionada por temporizador que é executada a cada 10 segundos.

5. No separador **Desenvolver**, clique em **Registos** e veja a atividade no registo. Verá uma entrada de registo escrita a cada 10 segundos.
   
    ![Ver o registo para verificar se a função funciona](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

## <a name="add-a-message-queue-output-binding"></a>Adicionar um enlace de saída da fila de mensagens

1. No separador **Integrar**, escolha **Nova Saída** > **Armazenamento de Filas do Azure** > **Selecionar**.

    ![Adicionar uma função de temporizador de acionador](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

2. Introduza `myQueueItem` para **Nome do parâmetro de mensagem** e `functions-bindings` para **Nome da fila**, selecione uma **Ligação da conta de armazenamento** existente ou clique em **Nova** para criar uma ligação de conta de armazenamento e, em seguida, clique em **Guardar**.  

    ![Criar o enlace de saída para a fila de armazenamento](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab2.png)

1. De volta ao separador **Desenvolver**, acrescente o seguinte código à função:
   
    ```javascript
   
    function myQueueItem() 
    {
        return {
            msg: "some message goes here",
            time: "time goes here"
        }
    }
   
    ```
2. Localize a instrução *if* à volta da linha 9 da função e insira o seguinte código a seguir a essa instrução.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueueItem = toBeQed;
   
    ```  
   
    Este código cria um **myQueueItem** e define a respetiva propriedade **time** como o timeStamp atual. Em seguida, adiciona o novo item da fila ao enlace **myQueueItem** do contexto.

3. Clique em **Guardar e executar**.

## <a name="view-storage-updates-by-using-storage-explorer"></a>Ver atualizações de armazenamento através do Explorador de Armazenamento
Pode verificar se a função está a funcionar, visualizando as mensagens na fila que criou.  Pode ligar à sua fila de armazenamento através do Cloud Explorer no Visual Studio. No entanto, o portal facilita a ligação à sua conta de armazenamento através do Explorador de Armazenamento do Microsoft Azure.

1. No separador **Integrar**, clique no enlace de saída da fila > **Documentação**, mostre a Cadeia de Ligação para a sua conta de armazenamento e copie o valor. Utilize este valor para ligar à sua conta de armazenamento.

    ![Transferir o Explorador de Armazenamento do Azure](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab3.png)


2. Se ainda não o fez, transfira e instale o [Explorador de Armazenamento do Microsoft Azure](http://storageexplorer.com). 
 
3. No Explorador de Armazenamento, clique no ícone Ligar ao Armazenamento do Azure, cole a cadeia de ligação no campo e conclua o assistente.

    ![Explorador de Armazenamento adicionar uma ligação](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-storage-explorer.png)

4. Em **Local e ligado**, expanda **Contas de Armazenamento** > a sua conta de armazenamento > **Filas** > **functions-bindings** e verifique se as mensagens estão escritas na fila.

    ![Vista de mensagens na fila](./media/functions-create-an-azure-connected-function/functionsbindings-azure-storage-explorer.png)

    Se a fila não existir ou estiver vazia, é muito provável que exista um problema no código ou enlace de função.

## <a name="create-a-function-that-reads-from-the-queue"></a>Criar uma função que lê a partir da fila

Agora que tem mensagens que estão a ser adicionadas à fila, pode criar outra função que lê a partir da fila e escreve as mensagens de forma permanente numa tabela do Armazenamento do Azure.

1. Clique em **Nova Função** > **QueueTrigger CSharp**. 
 
2. Dê o nome `FunctionsBindingsDemo2` à função, introduza **functions-bindings** no campo **Nome da fila**, selecione uma conta de armazenamento existente ou crie uma e, em seguida, clique em **Criar**.

    ![Adicionar uma função de temporizador de fila de saída](./media/functions-create-an-azure-connected-function/function-demo2-new-function.png) 

3. (Opcional) Pode verificar se a nova função funciona ao visualizar a nova fila no Explorador de Armazenamento como anteriormente. Também pode utilizar o Cloud Explorer no Visual Studio.  

4. (Opcional) Atualize a fila **functions-bindings** e repare que os itens foram removidos da fila. A remoção ocorre porque a função está vinculada à fila **functions-bindings** como um acionador de entrada e a função lê a fila. 
 
## <a name="add-a-table-output-binding"></a>Adicionar um enlace de saída de tabela

1. Em FunctionsBindingsDemo2, clique em **Integrar** > **Nova Saída** > **Armazenamento de Tabelas do Azure** > **Selecionar**.

    ![Adicionar um enlace a uma tabela de Armazenamento do Azure](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png) 

2. Introduza `TableItem` para **Nome da tabela** e `functionbindings` para **Nome do parâmetro de tabela**, escolha um **Ligação da conta de armazenamento** ou crie uma nova e, em seguida, clique em **Guardar**.

    ![Configurar o enlace da tabela de Armazenamento](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab2.png)
   
3. No separador **Desenvolver**, substitua o código da função existente pelo seguinte:
   
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
        
        // Add the item to the table binding collection.
        myTable.Add(myItem);
    
        log.Verbose($"C# Queue trigger function processed: {myItem.RowKey} | {myItem.Msg} | {myItem.Time}");
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
    A classe **TableItem** representa uma linha na tabela de armazenamento e o utilizador adiciona o item à coleção `myTable` de objetos **TableItem**. Tem de definir as propriedades **PartitionKey** e **RowKey** para conseguir inserir na tabela.

4. Clique em **Guardar**.  Por último, pode verificar se a função funciona, visualizando a tabela no Explorador de armazenamento ou no Cloud Explorer do Visual Studio.

5. (Opcional) Na sua conta de armazenamento no Explorador de Armazenamento, expanda **Tabelas** > **functionsbindings** e verifique se as linhas são adicionadas à tabela. Pode fazer o mesmo no Cloud Explorer no Visual Studio.

    ![Vista de linhas na tabela](./media/functions-create-an-azure-connected-function/functionsbindings-azure-storage-explorer2.png)

    Se a tabela não existir ou estiver vazia, é muito provável que exista um problema no código ou enlace de função. 
 
[!INCLUDE [More binding information](../../includes/functions-bindings-next-steps.md)]

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes tópicos para obter mais informações sobre o Funções do Azure.

* [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
* [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.
* [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de alojamento de Consumo e como escolher o plano adequado. 

[!INCLUDE [Getting help note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO1-->



---
title: "Cenário – acionador lógica aplicações com as funções do Azure e Azure Service Bus | Microsoft Docs"
description: "Criar uma função para acionar uma aplicação lógica, utilizando as funções do Azure e o Service Bus do Azure"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 088f10bc32dd492f82f0a10a7e5829e76f588758
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scenario-trigger-a-logic-app-with-azure-functions-and-azure-service-bus"></a>Cenário: Acionar uma aplicação lógica com as funções do Azure e o Service Bus do Azure

Pode utilizar as funções do Azure para criar um acionador para uma aplicação lógica, quando precisar de implementar uma execução longa escuta ou tarefas. Por exemplo, pode criar uma função que escuta uma fila e, em seguida, acionados imediatamente uma aplicação lógica como um acionador de push.

## <a name="build-the-logic-app"></a>Criar a aplicação lógica
Neste exemplo, poderá ter uma função em execução para cada aplicação lógica que tem de ser acionado. Em primeiro lugar, crie uma aplicação lógica que tem um acionador de pedido HTTP. A função chama esse ponto final sempre que é recebida uma mensagem de fila.  

1. Crie uma aplicação lógica.
2. Selecione o **Manual - quando é recebido um pedido HTTP** acionador.
   Opcionalmente, pode especificar um esquema JSON para utilizar com a mensagem da fila, utilizando uma ferramenta como o [jsonschema.net](http://jsonschema.net). Cole o esquema no acionador. Esquemas ajudam o estruturador de compreender a forma das propriedades do fluxo de dados e mais fácil através do fluxo de trabalho.
2. Adiciona quaisquer passos adicionais que pretende ocorrer depois de uma mensagem de fila é recebida. Por exemplo, envie uma mensagem de e-mail através do Office 365.  
3. Guarde a aplicação lógica para gerar o URL de chamada de retorno para acionar a esta aplicação lógica. O URL é apresentado no cartão acionador.

![A chamada de retorno URL aparece no cartão acionador][1]

## <a name="build-the-function"></a>A função de compilação
Em seguida, tem de criar uma função que age como o acionador e escuta para a fila.

1. No [portal das funções do Azure](https://functions.azure.com/signin), selecione **nova função**e, em seguida, selecione o **ServiceBusQueueTrigger - c#** modelo.
   
    ![Portal de funções do Azure][2]
2. Configurar a ligação para a fila de barramento de serviço, que utiliza o SDK do Service Bus `OnMessageReceive()` serviço de escuta.
3. Escreva uma função básica para chamar o ponto final da aplicação lógica (criado anteriormente) utilizando a mensagem da fila como um acionador. Eis um exemplo completo de uma função. O exemplo utiliza um `application/json` tipo de conteúdo de mensagem, mas pode alterar este tipo conforme necessário.
   
   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
   
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Para testar, adicione uma mensagem da fila através de uma ferramenta como o [Explorador do Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer). Consulte a aplicação de lógica acionados imediatamente após a função recebe a mensagem.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png

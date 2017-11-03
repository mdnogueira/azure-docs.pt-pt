---
title: "Criar uma função no Azure acionado por um webhook genérico | Microsoft Docs"
description: "Utilize as funções do Azure para criar uma função sem servidor que é invocada por um webhook no Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: fafc10c0-84da-4404-b4fa-eea03c7bf2b1
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/12/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: f283f8d79c5ae5fb6a72c84c9e9edb7bb8de4a83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-triggered-by-a-generic-webhook"></a>Criar uma função acionada por um webhook genérico

As Funções do Azure permitem-lhe executar o seu código num ambiente sem servidor, sem que tenha de criar primeiro uma VM ou publicar uma aplicação Web. Por exemplo, pode configurar uma função de acionada por um alerta gerado pelo Monitor do Azure. Este tópico mostra como executar código c#, quando um grupo de recursos é adicionado à sua subscrição.   

![Webhook genérico acionada função no portal do Azure](./media/functions-create-generic-webhook-triggered-function/function-completed.png)

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir este tutorial:

+ Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, vai criar uma função na aplicação Function App nova.

## <a name="create-function"></a>Criar uma função de webhook genérico acionada

1. Expanda a aplicação de funções e clique no botão **+**, junto a **Funções**. Se esta função é o primeiro na sua aplicação de função, selecione **função personalizada**. É apresentado o conjunto completo de modelos de função.

    ![Início rápido das funções no portal do Azure](./media/functions-create-generic-webhook-triggered-function/add-first-function.png)

2. Selecione o **WebHook genérico - c#** modelo. Escreva um nome para a função de c#, em seguida, selecione **criar**.

     ![Criar uma função de webhook genérico acionada no portal do Azure](./media/functions-create-generic-webhook-triggered-function/functions-create-generic-webhook-trigger.png) 

2. Na sua nova função, clique em **<> / Get função URL**, em seguida, copie e guarde o valor. Utilize este valor para configurar o webhook. 

    ![Reveja o código da função](./media/functions-create-generic-webhook-triggered-function/functions-copy-function-url.png)
         
Em seguida, crie um ponto final de webhook num alerta de registo de atividade no Monitor do Azure. 

## <a name="create-an-activity-log-alert"></a>Criar um alerta de registo de atividade

1. No portal do Azure, navegue para o **Monitor** serviço, selecione **alertas**e clique em **Adicionar alerta de registo de atividade**.   

    ![Monitorizar](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert.png)

2. Utilize as definições especificadas na tabela:

    ![Criar um alerta de registo de atividade](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings.png)

    | Definição      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome de alerta de registo de atividade** | recursos-grupo-criar-alerta | Nome do alerta de registo de atividade. |
    | **Subscrição** | A sua subscrição | A subscrição que está a utilizar para este tutorial. | 
    |  **Grupo de Recursos** | myResourceGroup | O grupo de recursos que o alerta recursos são implementados. Utilizar o mesmo grupo de recursos que a sua aplicação de função torna mais fácil limpar depois de concluir o tutorial. |
    | **Categoria de evento** | Administrativos | Esta categoria inclui as alterações efetuadas aos recursos do Azure.  |
    | **Tipo de recurso** | Grupos de recursos | Filtros de alertas para as atividades do grupo de recursos. |
    | **Grupo de Recursos**<br/>e **recursos** | Todos | Monitorize todos os recursos. |
    | **Nome da operação** | Criar Grupo de Recursos | Filtros de alertas para operações de criar. |
    | **Nível** | Informativo | Inclua alertas informativos de nível. | 
    | **Estado** | Bem-sucedido | Filtros de alertas para as ações que foram concluídos com êxito. |
    | **Grupo de ação** | novo | Crie um novo grupo de ação, que define a ação demora quando for gerado um alerta. |
    | **Nome do grupo de ação** | função de webhook | Um nome para identificar o grupo de ação.  | 
    | **Nome abreviado** | funcwebhook | Um nome abreviado para o grupo de ação. |  

3. No **ações**, adicionar uma ação com as definições conforme especificado na tabela: 

    ![Adicionar um grupo de ação](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings-2.png)

    | Definição      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | CallFunctionWebhook | Um nome para a ação. |
    | **Tipo de ação** | Webhook | A resposta para o alerta é que é chamado um URL do Webhook. |
    | **Detalhes** | URL de função | Cole o URL do webhook da função que copiou anteriormente. |v

4. Clique em **OK** ao criar o grupo de alerta e a ação.  

O webhook é agora designado quando é criado um grupo de recursos na sua subscrição. Em seguida, atualize o código na sua função para processar os dados de registo JSON no corpo do pedido.   

## <a name="update-the-function-code"></a>Atualizar o código da função

1. Navegue de volta para a sua aplicação de função no portal e expandir a sua função. 

2. Substitua o código de script do c# na função no portal pelo código seguinte:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"Webhook was triggered!");
    
        // Get the activityLog object from the JSON in the message body.
        string jsonContent = await req.Content.ReadAsStringAsync();
        JToken activityLog = JObject.Parse(jsonContent.ToString())
            .SelectToken("data.context.activityLog");
    
        // Return an error if the resource in the activity log isn't a resource group. 
        if (activityLog == null || !string.Equals((string)activityLog["resourceType"], 
            "Microsoft.Resources/subscriptions/resourcegroups"))
        {
            log.Error("An error occured");
            return req.CreateResponse(HttpStatusCode.BadRequest, new
            {
                error = "Unexpected message payload or wrong alert received."
            });
        }
    
        // Write information about the created resource group to the streaming log.
        log.Info(string.Format("Resource group '{0}' was {1} on {2}.",
            (string)activityLog["resourceGroupName"],
            ((string)activityLog["subStatus"]).ToLower(), 
            (DateTime)activityLog["submissionTimestamp"]));
    
        return req.CreateResponse(HttpStatusCode.OK);    
    }
    ```

Agora pode testar a função ao criar um novo grupo de recursos na sua subscrição.

## <a name="test-the-function"></a>Testar a função

1. Clique no ícone de grupo de recursos no lado esquerdo do portal do Azure, selecione **+ adicionar**, escreva um **nome do grupo de recursos**e selecione **criar** para criar um grupo de recursos em branco.
    
    ![Crie um grupo de recursos.](./media/functions-create-generic-webhook-triggered-function/functions-create-resource-group.png)

2. Volte à sua função e expanda o **registos** janela. Depois de criar o grupo de recursos, o alerta de registo de atividade aciona o webhook e executa a função. Pode ver o nome do novo grupo de recursos escrito nos registos.  

    ![Adicione uma definição de aplicação de teste.](./media/functions-create-generic-webhook-triggered-function/function-view-logs.png)

3. (Opcional) Voltar atrás e elimine o grupo de recursos que criou. Tenha em atenção que esta actividade não acionar a função. Isto acontece porque eliminar operações são filtradas pelo alerta. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Foi criada com uma função que é executado quando é recebido um pedido de um webhook genérico. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre os acionadores de webhook, veja [Enlaces de HTTP e webhook das Funções do Azure](functions-bindings-http-webhook.md). Para obter mais informações sobre como desenvolver as funções em c#, consulte [referência para programadores script Azure funções c#](functions-reference-csharp.md).


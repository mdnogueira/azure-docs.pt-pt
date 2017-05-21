---
title: "Criar uma função no Azure acionada por um webhook do GitHub | Microsoft Docs"
description: "Utilize as Funções do Azure para criar uma função sem servidores que é invocada por um webhook do GitHub."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 34988ef05a27062ca109a1640e39695b52b8773f
ms.contentlocale: pt-pt
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Criar uma função acionada por um webhook do GitHub

Saiba como criar uma função que é acionada por um pedido de webhook HTTP com um payload específico do GitHub. 

![Função acionada por um webhook do GitHub no portal do Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

Deverá demorar menos de cinco minutos para concluir todos os passos neste tópico.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Também precisa de uma conta do GitHub com um projeto, no mínimo. Pode [inscrever-se numa conta gratuita do GitHub](https://github.com/join), se ainda não tiver uma.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Criar uma função acionada por um webhook do GitHub

1. Expanda a aplicação Function App, clique no botão **+** junto a **Functions** e clique no modelo **GitHubWebHook** para a linguagem que pretende. **Dê um nome à função** e clique em **Criar**. 

2. Na função nova, clique em **</> Obter URL da função** e, em seguida, copie e guarde os valores. Faça a mesma coisa para **</> / Obter segredo do GitHub**. Utiliza estes valores para configurar o webhook no GitHub. 

    ![Reveja o código da função](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
Em seguida, crie o webhook no repositório do GitHub. 

## <a name="configure-the-webhook"></a>Configurar o webhook
1. No GitHub, navegue para um repositório do qual seja proprietário. Também pode utilizar qualquer repositório bifurcado. Se precisar de bifurcar um repositório, utilize <https://github.com/Azure-Samples/functions-quickstart>. 
 
2. Clique em **Definições** e, em seguida, clique em **Webhooks** e **Adicionar webhook**.
   
    ![Adicionar um webhook do GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Utilize as definições especificadas na tabela e clique em **Adicionar webhook**.
 
    ![Definir URL e segredo do webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

    | Definição      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **URL do payload** | Valor copiado | Utilize o valor devolvido por **</> Obter ULR da função**. |
    | **Segredo**   | Valor copiado | Utilize o valor devolvido por **</> Obter segredo do GitHub**. |
    | **Tipo de Conteúdo** | application/json | A função espera um payload JSON. |
    | Acionadores de eventos | Permitir selecionar eventos individuais | Só queremos acionar em eventos de comentários de problemas.  |
    |                | Comentário de problema                    |  |

Agora, o webhook está configurado para acionar a função quando é adicionado um novo comentário de problema. 

## <a name="test-the-function"></a>Testar a função
1. No seu repositório do GitHub, abra o separador **Problemas** numa nova janela do browser.

2. Numa janela nova, clique em **Novo Problema**, escreva um título e, em seguida, clique em **Submeter novo problema**. 

2. No problema, introduza um comentário e clique em **Comentário**. 

    ![Adicione um comentário de problema do GitHub.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png) 

3. Regresse ao portal e veja os registos. Deverá ver uma entrada de rastreio com o texto do comentário novo. 
    
     ![Veja o texto do comentário nos registos.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)
 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada quando é recebido um pedido de um webhook do GitHub. 
[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)] Para obter mais informações sobre os acionadores de webhook, veja [Azure Functions HTTP and webhook bindings](functions-bindings-http-webhook.md) (Enlaces de HTTP e webhook das Funções do Azure). 





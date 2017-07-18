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
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 6e91d095c8a89ead513a8ea63f74fd4b9384cfd5
ms.contentlocale: pt-pt
ms.lasthandoff: 07/10/2017

---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Criar uma função acionada por um webhook do GitHub

Saiba como criar uma função que é acionada por um pedido de webhook HTTP com um payload específico do GitHub.

![Função acionada por um webhook do GitHub no portal do Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Pré-requisitos

+ Uma conta do GitHub com um projeto, no mínimo.
+ Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicação Function App criada com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Criar uma função acionada de um webhook do GitHub

1. Expanda a aplicação de funções e clique no botão **+**, junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **Função personalizada**. É apresentado o conjunto completo de modelos de função.

    ![Início rápido das funções no portal do Azure](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. Selecione o modelo **GitHubWebHook** para o idioma desejado. **Atribua um nome à função** e selecione **Criar**.

     ![Criar uma função acionada de um webhook do GitHub no portal do Azure](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

3. Na função nova, clique em **</> Obter URL da função** e, em seguida, copie e guarde os valores. Faça a mesma coisa para **</> / Obter segredo do GitHub**. Utiliza estes valores para configurar o webhook no GitHub.

    ![Reveja o código da função](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

Em seguida, crie o webhook no repositório do GitHub.

## <a name="configure-the-webhook"></a>Configurar o webhook

1. No GitHub, navegue para um repositório do qual seja proprietário. Também pode utilizar qualquer repositório bifurcado. Se precisar de bifurcar um repositório, utilize <https://github.com/Azure-Samples/functions-quickstart>.

1. Clique em **Definições** e, em seguida, clique em **Webhooks** e **Adicionar webhook**.

    ![Adicionar um webhook do GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Utilize as definições especificadas na tabela e clique em **Adicionar webhook**.

    ![Definir URL e segredo do webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Definição | Valor sugerido | Descrição |
|---|---|---|
| **URL do payload** | Valor copiado | Utilize o valor devolvido por **</> Obter ULR da função**. |
| **Segredo**   | Valor copiado | Utilize o valor devolvido por **</> Obter segredo do GitHub**. |
| **Tipo de Conteúdo** | application/json | A função espera um payload JSON. |
| Acionadores de eventos | Permitir selecionar eventos individuais | Só queremos acionar em eventos de comentários de problemas.  |
| | Comentário de problema |  |

Agora, o webhook está configurado para acionar a função quando é adicionado um novo comentário de problema.

## <a name="test-the-function"></a>Testar a função

1. No seu repositório do GitHub, abra o separador **Problemas** numa nova janela do browser.

1. Numa janela nova, clique em **Novo Problema**, escreva um título e, em seguida, clique em **Submeter novo problema**.

1. No problema, introduza um comentário e clique em **Comentário**.

    ![Adicione um comentário de problema do GitHub.](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Regresse ao portal e veja os registos. Deverá ver uma entrada de rastreio com o texto do comentário novo.

     ![Veja o texto do comentário nos registos.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada quando é recebido um pedido de um webhook do GitHub.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre os acionadores de webhook, veja [Enlaces de HTTP e webhook das Funções do Azure](functions-bindings-http-webhook.md).


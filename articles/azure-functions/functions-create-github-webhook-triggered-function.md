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
ms.date: 04/18/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d4354546f3342d65353a86a4cec7d02547ab92e7
ms.lasthandoff: 04/22/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Criar uma função acionada por um webhook do GitHub

Saiba como criar uma função que é acionada por um webhook do GitHub. 

![Criar uma aplicação de função no portal do Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

Este tópico necessita dos recursos criados no tópico [Criar a primeira função a partir do portal do Azure](functions-create-first-azure-function.md).

Também precisa de uma conta do GitHub. Pode [inscrever-se numa conta gratuita do GitHub](https://github.com/join), se ainda não tiver uma. 

Deverá demorar menos de cinco minutos para concluir todos os passos neste tópico.

## <a name="find-your-function-app"></a>Encontrar a aplicação de função    

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/). 

2. Na barra de pesquisa na parte superior do portal, escreva o nome da sua aplicação de função e selecione-a na lista.

## <a name="create-function"></a>Criar uma função acionada por um webhook do GitHub

1. Na sua aplicação de função, clique no botão **+** junto a **Funções**, clique no modelo **GitHubWebHook** do seu idioma pretendido e clique em **Criar**.
   
    ![Criar uma função acionada de um webhook do GitHub no portal do Azure.](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Clique em **</> Obter URL da função** e, em seguida, copie e guarde os valores. Faça a mesma coisa para **</> / Obter segredo do GitHub**. Utiliza estes valores para configurar o webhook no GitHub. 

    ![Reveja o código da função](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png) 
         
Em seguida, crie o webhook no repositório do GitHub. 

## <a name="configure-the-webhook"></a>Configurar o webhook
1. No GitHub, navegue para um repositório do qual seja proprietário. Também pode utilizar qualquer repositório bifurcado.
 
2. Clique em **Definições** e, em seguida, clique em **Webhooks** e **Adicionar webhook**.
   
    ![Adicionar um webhook do GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

3. Cole o URL e o segredo da função no **URL de Payload** e **Segredo** e selecione **aplicação/json** para **Tipo de conteúdo**.

4. Clique em **Selecionar eventos individuais**, selecione **Emitir comentário** e clique em **Adicionar webhook**.
   
    ![Definir URL e segredo do webhook](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

Agora, o webhook está configurado para acionar a função quando é adicionado um novo comentário de problema. 

## <a name="test-the-function"></a>Testar a função
1. No seu repositório do GitHub, abra o separador **Problemas** numa nova janela do browser.

2. Numa nova janela, clique em **Novo Problema**, escreva um título e, em seguida, clique em **Submeter novo problema**. 

2. No problema, introduza um comentário e clique em **Comentário**. 

3. Na outra janela do GitHub, clique em **Editar** junto ao seu novo webhook, desloque para baixo para **Entregas Recentes** e certifique-se de que foi processado pela sua função. 
 
    ![Definir URL e segredo do webhook](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-triggered.png)

   A resposta da sua função deve conter `New GitHub comment: <Your issue comment text>`.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]



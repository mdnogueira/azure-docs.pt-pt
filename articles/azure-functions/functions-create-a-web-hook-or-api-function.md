---
title: "Criar um web hook ou a Função de Azure API | Microsoft Docs"
description: "Utilize as Funções do Azure para criar uma função que é invocada por uma chamada WebHook ou API."
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
ms.date: 08/30/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9484a637a1876f2cae644e43986bd5ef3201da1e


---
# <a name="create-a-webhook-or-api-azure-function"></a>Criar um webhook ou uma Função Azure API
As Funções do Azure são uma experiência baseada em eventos, de cálculo a pedido que lhe permite criar unidades agendadas ou acionadas de código implementado em várias linguagens de programação. Para saber mais acerca das Funções do Azure, veja a [Descrição Geral das Funções do Azure](functions-overview.md).

Este tópico mostra-lhe como criar uma nova função Node.js, que é invocada por um webhook do GitHub. A nova função é criada com base num modelo predefinido no portal Funções do Azure. Também pode ver um breve vídeo para verificar como estes passos são efetuados no portal.

## <a name="watch-the-video"></a>Ver o vídeo
O vídeo seguinte mostra como efetuar os passos básicos neste tutorial 

>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-a-Web-Hook-or-API-Azure-Function/player]
>
>

## <a name="create-a-webhooktriggered-function-from-the-template"></a>Criar uma função acionada por webhook a partir do modelo
Uma aplicação de função aloja a execução das suas funções no Azure. Antes de poder criar uma função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/). 

1. Aceda ao [portal das Funções do Azure](https://functions.azure.com/signin) e inicie sessão com a sua conta do Azure.
2. Se tiver uma aplicação de função existente para utilizar, selecione-a partir de **As suas aplicações de função** e, em seguida, clique em **Abrir**. Para criar uma nova aplicação de função, escreva um **Nome** exclusivo para a nova aplicação de função ou aceite a gerada, selecione a **Região** preferencial e, em seguida, clique em **Criar + começar**. 
3. Na sua aplicação de função, clique em **+ Nova função** > **GitHub Webhook - Nó** > **Criar**. Isto cria uma função com um nome predefinido que é baseado no modelo especificado. 
   
    ![Criar uma nova função de webhook GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 
4. Em **Desenvolver**, tenha em conta a função express.js na janela **Código**. Esta função recebe um pedido de GitHub a partir de um webhook de comentário ao problema, regista o texto de problema e envia uma resposta para o webhook como `New GitHub comment: <Your issue comment text>`.

    ![Criar uma nova função de webhook GitHub](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

1. Copie os valores **URL de função** e **Segredo do GitHub**. Estes são necessários quando cria o webhook no GitHub. 
2. Desloque para baixo para **Executar**, tenha em atenção o corpo JSON predefinido de um comentário ao problema no corpo do pedido e, em seguida, clique em **Executar**. 
   
    Pode sempre testar uma nova função baseada em modelos no separador **Desenvolver** fornecendo dados JSON de corpo esperados e clicando no botão **Executar**. Neste caso, o modelo tem um corpo predefinido para um comentário ao problema. 

Em seguida, crie o webhook no repositório do GitHub.

## <a name="configure-the-webhook"></a>Configurar o webhook
1. No GitHub, navegue para um repositório de que é o proprietário. Isto inclui quaisquer repositórios que tenha escolhido.
2. Clique em **Definições** > **Webhooks e serviços** > **Adicionar webhook**.
   
    ![Criar uma nova função de webhook GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   
3. Cole o URL e o segredo da função no **URL de Payload** e **Segredo**. Em seguida, clique em **Deixar-me selecionar eventos individuais**, selecione **Emitir comentário** e clique em **Adicionar webhook**.
   
    ![Criar uma nova função de webhook GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

Neste momento, o webhook GitHub está configurado para acionar a função quando é adicionado um novo comentário de problema.  
Agora, está na altura de testá-lo.

## <a name="test-the-function"></a>Testar a função
1. No seu repositório do GitHub, abra o separador **Problemas** numa nova janela do browser, clique em **Novo problema**, escreva um título e, em seguida, clique em **Submeter novo problema**. Também pode abrir um problema existente.
2. No problema, introduza um comentário e clique em **Comentário**. Neste momento, pode voltar para o novo webhook no GitHub e, em **Entregas Recentes** verifique se foi enviado um pedido de webhook e que o corpo da resposta é `New GitHub comment: <Your issue comment text>`.
3. De novo no portal de Funções, desloque para baixo para os registos e verifique se a função foi acionada e o valor `New GitHub comment: <Your issue comment text>` é escrito nos registos de transmissão em fluxo.

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes tópicos para obter mais informações sobre o Funções do Azure.

* [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência do programador para funções de codificação.
* [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.
* [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de serviço Dinâmico e como escolher o plano adequado.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->



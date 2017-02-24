---
title: "Criar a sua primeira Função do Azure | Microsoft Docs"
description: "Crie a sua primeira Função do Azure, uma aplicação sem servidor, em menos de dois minutos."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/09/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 7743bfb98552f7fa2334d8daa6a6f6935969f393
ms.openlocfilehash: d76630e0be4a021720d88e6d7e64cf2258f84266


---
# <a name="create-your-first-azure-function"></a>Criar a sua primeira Função do Azure
## <a name="overview"></a>Descrição geral
As Funções do Azure são uma experiência de computação a pedido condicionada por eventos que expande a plataforma de aplicações do Azure existente com capacidades para implementar código acionado por eventos que ocorrem noutros serviços do Azure, produtos SaaS e sistemas no local. Com as Funções do Azure, as suas aplicações são dimensionadas a pedido e paga apenas os recursos que consumir. As Funções do Azure permitem-lhe criar unidades agendadas ou acionadas de código implementado em várias linguagens de programação. Para saber mais acerca das Funções do Azure, veja a [Descrição Geral das Funções do Azure](functions-overview.md).

Este tópico mostra como utilizar o início rápido das Funções do Azure no portal para criar uma função JavaScript "olá, mundo" simples invocada por um acionador de HTTP. Também pode ver um breve vídeo para verificar como estes passos são efetuados no portal.

## <a name="watch-the-video"></a>Ver o vídeo
O vídeo seguinte mostra como realizar os passos básicos neste tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Criar uma função a partir do início rápido
Uma aplicação de função aloja a execução das suas funções no Azure. Siga estes passos para criar uma aplicação de função com a função nova. A aplicação de função é criada com uma configuração predefinida. Para obter um exemplo de como criar explicitamente a sua Function App, veja [o outro tutorial de início rápido das Funções do Azure](functions-create-first-azure-function-azure-portal.md).

Antes de poder criar a sua primeira função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/).

1. Aceda ao [portal das Funções do Azure](https://functions.azure.com/signin) e inicie sessão com a sua conta do Azure.
2. Escreva um **Nome** exclusivo para a nova aplicação de função ou aceite o gerado automaticamente, selecione a **Região** preferencial e, em seguida, clique em **Criar + começar**. Tenha em atenção que tem de introduzir um nome válido, que pode conter apenas letras, números e hífenes. O caráter de sublinhado (**_**) não é um caráter permitido.
3. No separador **Início Rápido**, clique em **WebHook + API** e **JavaScript** e, em seguida, clique em **Criar uma função**. É criada uma nova função JavaScript predefinida. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Opcional) Neste momento no início rápido, pode optar por fazer uma visita guiada das funcionalidades das Funções do Azure no portal. Depois de concluir ou ignorar a visita guiada, pode testar a sua nova função com o acionador de HTTP.

## <a name="test-the-function"></a>Testar a função
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Feb17_HO2-->



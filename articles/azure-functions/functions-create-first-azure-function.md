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
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>Criar a sua primeira Função do Azure

Este tópico mostra como utilizar o início rápido das Funções do Azure no portal para criar uma função "olá, mundo" simples invocada por um pedido de HTTP. Para saber mais acerca das Funções do Azure, veja a [Descrição Geral das Funções do Azure](functions-overview.md).

Antes de começar, tem de ter uma conta do Azure. Existem [contas gratuitas](https://azure.microsoft.com/free/) disponíveis. Também pode [experimentar as Funções do Azure](https://azure.microsoft.com/try/app-service/functions/) sem ter de registar-se no Azure.

## <a name="create-a-function-from-the-portal-quickstart"></a>Criar uma função a partir do início rápido do portal

1. Aceda ao [portal das Funções do Azure](https://functions.azure.com/signin) e inicie sessão com a sua conta do Azure. 
 
2. Escreva um **Nome** exclusivo para a nova aplicação de função ou aceite o gerado automaticamente, selecione a **Região** preferencial e, em seguida, clique em **Criar + começar**. Um nome válido só pode conter letras, números e hífenes. O caráter de sublinhado (**_**) não é um caráter permitido.

3. No separador **Início Rápido**, clique em **WebHook + API** e escolha um idioma para a função e, em seguida, clique em **Criar uma função**. Uma nova função predefinida é criada no idioma escolhido. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Opcional) Neste momento no início rápido, pode optar por fazer uma visita guiada das funcionalidades das Funções do Azure no portal. Depois de concluir ou ignorar a visita guiada, pode testar a nova função enviando um pedido de HTTP.

## <a name="test-the-function"></a>Testar a função
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>Ver o vídeo
O vídeo seguinte mostra como realizar os passos básicos neste tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]



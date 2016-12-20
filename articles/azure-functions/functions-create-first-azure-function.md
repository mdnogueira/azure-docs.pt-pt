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
ms.date: 11/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: ae5837b4fce52aad4c8b39434c27c450aafc1310
ms.openlocfilehash: 6f42f79abed80df12148463e25935893a4bbdcde


---
# <a name="create-your-first-azure-function"></a>Criar a sua primeira Função do Azure
## <a name="overview"></a>Descrição geral
As Funções do Azure são uma experiência de computação a pedido condicionada por eventos que expande a plataforma de aplicações do Azure existente com capacidades para implementar código acionado por eventos que ocorrem noutros serviços do Azure, produtos SaaS e sistemas no local. Com as Funções do Azure, as suas aplicações são dimensionadas a pedido e paga apenas os recursos que consumir. As Funções do Azure permitem-lhe criar unidades agendadas ou acionadas de código implementado em várias linguagens de programação. Para saber mais acerca das Funções do Azure, veja a [Descrição Geral das Funções do Azure](functions-overview.md).

Este tópico mostra como utilizar o início rápido das Funções do Azure no portal para criar uma função Node.js "olá, mundo" simples invocada por um acionador de HTTP. Também pode ver um breve vídeo para verificar como estes passos são efetuados no portal.

## <a name="watch-the-video"></a>Ver o vídeo
O vídeo seguinte mostra como realizar os passos básicos neste tutorial. 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 
> 

## <a name="create-a-function-from-the-quickstart"></a>Criar uma função a partir do início rápido
Uma aplicação de função aloja a execução das suas funções no Azure. Siga estes passos para criar uma aplicação de função com a função nova. A aplicação de função é criada com uma configuração predefinida. Para obter um exemplo de como criar explicitamente a sua Function App, veja [o outro tutorial de início rápido das Funções do Azure](functions-create-first-azure-function-azure-portal.md).

Antes de poder criar a sua primeira função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/).

1. Aceda ao [portal das Funções do Azure](https://functions.azure.com/signin) e inicie sessão com a sua conta do Azure.
2. Escreva um **Nome** exclusivo para a nova aplicação de função ou aceite o gerado, selecione a **Região** preferencial e, em seguida, clique em **Criar + começar**. 
3. No separador **Início Rápido**, clique em **WebHook + API** e **JavaScript** e, em seguida, clique em **Criar uma função**. É criada uma nova função Node.js predefinida. 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)
4. (Opcional) Neste momento no início rápido, pode optar por fazer uma visita guiada das funcionalidades das Funções do Azure no portal. Depois de concluir ou ignorar a visita guiada, pode testar a sua nova função com o acionador de HTTP.

## <a name="test-the-function"></a>Testar a função
Uma vez que os inícios rápidos das Funções do Azure contêm código funcional, pode testar a nova função imediatamente.

1. No separador **Desenvolver**, reveja a janela **Código** e tenha em atenção que este código Node.js espera um pedido HTTP com um valor *nome* transmitido no corpo da mensagem ou numa cadeia de consulta. Quando a função é executada, este valor é devolvido na mensagem de resposta.
   
2. Clique em **Testar** para apresentar o painel de pedido de teste HTTP da função.
 
    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Na caixa de texto **Corpo do pedido**, altere o valor da propriedade *nome* para o seu nome e clique em **Executar**. Verá que a execução é acionada por um pedido HTTP de teste, as informações são escritas nos registos de transmissão em fluxo e a resposta "olá" é apresentada no **Resultado**.
 
3. Para acionar a execução da mesma função a partir de outra janela ou separador do browser, copie o valor **URL da Função** do separador **Desenvolver** e cole-o numa barra de endereço do browser. Acrescente o valor da cadeia de consulta `&name=yourname` ao URL e prima Enter. As mesmas informações são escritas nos registos e o browser apresenta a resposta "olá" como anteriormente.

## <a name="next-steps"></a>Passos seguintes
Este início rápido demonstra uma execução simples de uma função básica acionada por HTTP. Para saber mais sobre como utilizar as Funções de Azure nas suas aplicações, veja os tópicos seguintes:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
* [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.
* [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de alojamento de Consumo e como escolher o plano adequado. 
* [O que é o Serviço de Aplicações do Azure?](../app-service/app-service-value-prop-what-is.md)  
  As Funções do Azure utilizam a plataforma do Serviço de Aplicações do Azure para funcionalidades essenciais como implementações, variáveis de ambiente e diagnósticos. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO4-->



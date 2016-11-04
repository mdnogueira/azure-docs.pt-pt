---
title: Criar uma função a partir do Portal do Azure | Microsoft Docs
description: Crie a sua primeira Função do Azure, uma aplicação sem servidor, em menos de dois minutos.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: ''
tags: ''

ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/08/2016
ms.author: glenga

---
# Criar uma função a partir do portal do Azure
## Descrição geral
As Funções do Azure são uma experiência de computação a pedido condicionada por eventos que expande a plataforma de aplicações do Azure existente com capacidades para implementar código acionado por eventos que ocorrem noutros serviços do Azure, produtos SaaS e sistemas no local. Com as Funções do Azure, as suas aplicações são dimensionadas a pedido e paga apenas os recursos que consumir. As Funções do Azure permitem-lhe criar unidades agendadas ou acionadas de código implementado em várias linguagens de programação. Para saber mais acerca das Funções do Azure, veja a [Descrição Geral das Funções do Azure](functions-overview.md).

Este tópico mostra-lhe como utilizar o portal do Azure para criar uma Função do Azure Node.js "olá, mundo" simples invocada por um acionador de HTTP. Antes de poder criar uma função no portal do Azure, tem de criar explicitamente uma aplicação de função no Serviço de Aplicações do Azure. Para que a aplicação de função seja criada automaticamente para si, veja [o outro tutorial de início rápido das Funções do Azure](functions-create-first-azure-function.md), que é uma experiência de início rápido mais simples com um vídeo incluído.

## Criar uma aplicação de função
Uma aplicação de função aloja a execução das suas funções no Azure. Siga estes passos para criar uma aplicação de função no portal do Azure.

Antes de poder criar a sua primeira função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/).

1. Aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Clique em **+Novo** > **Web + Móvel** > **Function App**, selecione a sua **Subscrição**, escreva um **Nome de aplicação** exclusivo que identifique a sua aplicação de função e, em seguida, especifique as seguintes definições:
   
   * **[Grupo de Recursos](../azure-portal/resource-group-portal.md)**: selecione **Criar novo** e introduza um nome para o seu novo grupo de recursos. Também pode escolher um grupo de recursos existente. No entanto, poderá não conseguir criar um plano do Serviço de Aplicações dinâmico para a sua aplicação de função.
   * **[Plano do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**: escolha *dinâmico* ou *clássico*. 
     * **Dinâmico**: o tipo de plano predefinido das Funções do Azure. Quando escolhe um plano dinâmico, também tem de escolher a **Localização** e definir a **Alocação de Memória** (em MB). Para obter informações sobre como a alocação de memória afeta os custos, veja o artigo [preços das Funções do Azure](https://azure.microsoft.com/pricing/details/functions/). 
     * **Clássico**: um plano do Serviço de Aplicações clássico requer a criação de um **plano/localização do Serviço de Aplicações** ou a seleção de um plano/localização existente. Estas definições determinam a [localização, as funcionalidades, o custo e os recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados à aplicação.  
   * **Conta de armazenamento**: cada aplicação de função requer uma conta de armazenamento. Pode escolher uma conta de armazenamento existente ou criar uma conta. 
     
     ![Criar uma nova aplicação de função no portal do Azure](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)
3. Clique em **Criar** para aprovisionar e implementar a nova aplicação de função.  

Agora que a aplicação de função está aprovisionada, pode criar a sua primeira função.

## Criar uma função
Siga estes passos para criar uma função a partir do início rápido das Funções do Azure.

1. No separador **Início Rápido**, clique em **WebHook + API** e **JavaScript** e, em seguida, clique em **Criar uma função**. É criada uma nova função Node.js predefinida. 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)
2. (Opcional) Neste momento no início rápido, pode optar por fazer uma visita guiada das funcionalidades das Funções do Azure no portal.   Depois de concluir ou ignorar a visita guiada, pode testar a sua nova função com o acionador de HTTP.

## Testar a função
Uma vez que os inícios rápidos das Funções do Azure contêm código funcional, pode testar a nova função imediatamente.

1. No separador **Desenvolver**, reveja a janela **Código** e tenha em atenção que este código Node.js espera um pedido HTTP com um valor *nome* transmitido no corpo da mensagem ou numa cadeia de consulta. Quando a função é executada, este valor é devolvido na mensagem de resposta.
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-develop-tab-testing.png)
2. Desloque-se para baixo para a caixa de texto **Corpo do pedido**, altere o valor da propriedade *nome* para o seu nome e clique em **Executar**. Verá que a execução é acionada por um pedido HTTP de teste, as informações são escritas nos registos de transmissão em fluxo e a resposta "olá" é apresentada no **Resultado**. 
3. Para acionar a execução da mesma função a partir de outra janela ou separador do browser, copie o valor **URL da Função** do separador **Desenvolver** e cole-o numa barra de endereço do browser e, em seguida, acrescente o valor da cadeia de consulta `&name=yourname` e prima Enter. As mesmas informações são escritas nos registos e o browser apresenta a resposta "olá" como anteriormente.

## Passos seguintes
Este início rápido demonstra uma execução muito simples de uma função básica acionada por HTTP. Veja estes tópicos para obter mais informações sobre como utilizar o poder das Funções do Azure nas suas aplicações.

* [Referência para programadores das Funções do Azure](functions-reference.md)  
  Referência para programadores para codificar funções e definir acionadores e enlaces.
* [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.
* [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de serviço Dinâmico e como escolher o plano adequado. 
* [O que é o Serviço de Aplicações do Azure?](../app-service/app-service-value-prop-what-is.md)  
  As Funções do Azure utilizam a plataforma do Serviço de Aplicações do Azure para funcionalidades essenciais como implementações, variáveis de ambiente e diagnósticos. 

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]

<!--HONumber=sep12_HO2-->



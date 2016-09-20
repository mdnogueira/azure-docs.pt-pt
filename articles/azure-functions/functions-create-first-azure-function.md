<properties
   pageTitle="Criar a sua primeira Função do Azure | Microsoft Azure"
   description="Crie a sua primeira Função do Azure, uma aplicação sem servidor, em menos de dois minutos."
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="hero-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="glenga"/>

#Criar a sua primeira Função do Azure

##Descrição geral
As Funções do Azure são uma experiência de computação a pedido condicionada por eventos que expande a plataforma de aplicações do Azure existente com capacidades para implementar código acionado por eventos que ocorrem noutros serviços do Azure, produtos SaaS e sistemas no local. Com as Funções do Azure, as suas aplicações são dimensionadas a pedido e paga apenas os recursos que consumir. As Funções do Azure permitem-lhe criar unidades agendadas ou acionadas de código implementado em várias linguagens de programação. Para saber mais acerca das Funções do Azure, veja a [Descrição Geral das Funções do Azure](functions-overview.md).

Este tópico mostra como utilizar o início rápido das Funções do Azure no portal para criar uma função Node.js "olá, mundo" simples invocada por um acionador de HTTP. Também pode ver um breve vídeo para verificar como estes passos são efetuados no portal.

## Ver o vídeo

O vídeo seguinte mostra como efetuar os passos básicos neste tutorial. 

[AZURE.VIDEO create-your-first-azure-function-simple]

##Criar uma função a partir do início rápido

Uma aplicação de função aloja a execução das suas funções no Azure. Siga estes passos para criar uma nova aplicação de função, bem como a nova função. A nova Function App é criada com uma configuração predefinida. Para obter um exemplo de como criar explicitamente a sua Function App, veja [o outro tutorial de início rápido das Funções do Azure](functions-create-first-azure-function-azure-portal.md).

Antes de poder criar a sua primeira função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/).

1. Aceda ao [portal das Funções do Azure](https://functions.azure.com/signin) e inicie sessão com a sua conta do Azure.

2. Escreva um **Nome** exclusivo para a nova aplicação de função ou aceite o gerado, selecione a **Região** preferencial e, em seguida, clique em **Criar + começar**. 

3. No separador **Início Rápido**, clique em **WebHook + API** e **JavaScript** e, em seguida, clique em **Criar uma função**. É criada uma nova função Node.js predefinida. 

    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (Opcional) Neste momento no início rápido, pode optar por fazer uma visita guiada das funcionalidades das Funções do Azure no portal.   Depois de concluir ou ignorar a visita guiada, pode testar a sua nova função com o acionador de HTTP.

##Testar a função

Uma vez que os inícios rápidos das Funções do Azure contêm código funcional, pode testar a nova função imediatamente.

1. No separador **Desenvolver**, reveja a janela **Código** e tenha em atenção que este código Node.js espera um pedido HTTP com um valor *nome* transmitido no corpo da mensagem ou numa cadeia de consulta. Quando a função é executada, este valor é devolvido na mensagem de resposta.

    ![](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Desloque para baixo para a caixa de texto **Corpo do pedido**, altere o valor da propriedade *nome* para o seu nome e clique em **Executar**. Verá que a execução é acionada por um pedido HTTP de teste, as informações são escritas nos registos de transmissão em fluxo e a resposta "olá" é apresentada no **Resultado**. 

3. Para acionar a execução da mesma função a partir de outra janela ou separador do browser, copie o valor **URL da Função** do separador **Desenvolver** e cole-o numa barra de endereço do browser, em seguida, acrescente o valor da cadeia de consulta `&name=yourname` e prima Enter. As mesmas informações são escritas nos registos e o browser apresenta a resposta "olá" como anteriormente.

##Passos seguintes

Este início rápido demonstra uma execução muito simples de uma função acionada por HTTP básico. Veja estes tópicos para obter mais informações sobre como tirar partido do poder das Funções do Azure nas suas aplicações.

+ [Referência para programadores das Funções do Azure](functions-reference.md)  
Referência para programadores para codificar funções e definir acionadores e enlaces.
+ [Testar as Funções do Azure](functions-test-a-function.md)  
Descreve várias ferramentas e técnicas para testar as suas funções.
+ [Como dimensionar as Funções do Azure](functions-scale.md)  
Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de serviço Dinâmico e como escolher o plano adequado. 
+ [O que é o Azure App Service?](../app-service/app-service-value-prop-what-is.md)  
Funções do Azure melhora a plataforma do Azure App Service para funcionalidades essenciais como implementações, variáveis de ambiente e diagnósticos. 

[AZURE.INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]



<!--HONumber=sep16_HO2-->



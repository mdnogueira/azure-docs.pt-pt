---
title: "Cenário – criar um dashboard de conhecimentos de cliente com sem servidor do Azure | Microsoft Docs"
description: "Um exemplo de como pode criar um dashboard para gerir a comentários de clientes, dados sociais e muito mais com Azure Logic Apps e as funções do Azure."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
ms.openlocfilehash: 0b6e118cb13ab8185d8eeb42bec6147155967967
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Criar um dashboard de conhecimentos de cliente em tempo real com Azure Logic Apps e as funções do Azure

Ferramentas de sem servidor do Azure fornecem capacidades poderosas para rapidamente criar e alojar aplicações na nuvem, sem ter de refletir sobre a infraestrutura.  Neste cenário, iremos criar um dashboard para acionar a comentários de clientes, analisar comentários com machine learning e publicar insights uma origem como o Power BI ou do Azure Data Lake.

## <a name="overview-of-the-scenario-and-tools-used"></a>Descrição geral do cenário e ferramentas utilizadas

Para implementar esta solução, iremos tirar partido os dois componentes principais de aplicações sem servidor no Azure: [das funções do Azure](https://azure.microsoft.com/services/functions/) e [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).

As Logic Apps é um motor de fluxo de trabalho sem servidor na nuvem.  Fornece orquestração componentes sem servidor e também estabelece ligação com mais de 100 APIs e serviços.  Para este cenário, iremos criar uma aplicação lógica para acionar a comentários de clientes.  Alguns dos conectores que podem ajudar a reagir a comentários de clientes incluem Outlook.com, Office 365, Monkey de inquérito, Twitter e um pedido de HTTP [de um formulário web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/).  Para o fluxo de trabalho abaixo, iremos irá monitorizar um hashtag no Twitter.

As funções fornecem sem servidor computação na nuvem.  Neste cenário, utilizaremos as funções do Azure para o sinalizador tweets de clientes com base numa série de palavras de chave predefinidas.

A solução completa pode ser [compilar no Visual Studio](logic-apps-deploy-from-vs.md) e [implementada como parte de um modelo do resource](logic-apps-create-deploy-template.md).  Também é vídeo com instruções sobre o cenário [no Channel 9](http://aka.ms/logicappsdemo).

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>Criar a aplicação lógica para acionar nos dados de cliente

Depois de [criar uma aplicação lógica](logic-apps-create-a-logic-app.md) no Visual Studio ou no portal do Azure:

1. Adicionar um acionador para **novos Tweets** do Twitter
2. Configure o acionador para escutar tweets num hashtag ou palavra-chave.

   > [!NOTE]
   > A propriedade de periodicidade no acionador irá determinar a frequência a aplicação lógica verifica a existência de novos itens na baseada em consulta acionadores

   ![Exemplo de Acionador do Twitter][1]

Agora, esta aplicação serão acionados em todos os novos tweets.  Em seguida, iremos pode demorar dados tweet e compreender melhor o sentimento expressado.  Para este utilizamos o [serviço cognitivos Azure](https://azure.microsoft.com/services/cognitive-services/) para detetar dados de sentimento do texto.

1. Clique em **novo passo**
1. Selecione ou procure o **análise de texto** conector
1. Selecione o **detetar sentimento** operação
1. Se lhe for solicitado, forneça uma chave de serviços cognitivos válida para o serviço de análise de texto
1. Adicionar o **Tweet texto** como o texto a analisar.

Agora que temos o tweet dados e das informações sobre o tweet, um número de outros conectores pode ser relevante:
* Power BI - adicionar linhas ao conjunto de dados de transmissão em fluxo: vista tweets em tempo real num dashboard do Power BI.
* Azure Data Lake - acrescentar o ficheiro: adicionar dados de cliente para um conjunto de dados do Azure Data Lake para incluir nas tarefas de análise.
* SQL Server - adicionar linhas: armazenar dados numa base de dados para obtenção posterior.
* Slack - enviar a mensagem: um canal slack nos comentários negativos que necessita de ações de alerta.

Uma função do Azure também pode ser utilizada para efetuar computação personalizada mais sobre os dados.

## <a name="enriching-the-data-with-an-azure-function"></a>Enriquecer os dados com uma função do Azure

Antes, pode criar uma função, é necessário ter uma aplicação de função na nossa subscrição do Azure.  Podem obter detalhes sobre a criação de uma função do Azure no portal do [ser encontrada aqui](../azure-functions/functions-create-first-azure-function-azure-portal.md)

Para uma função a chamar diretamente a partir de uma aplicação lógica, tem de ter um HTTP acionar o enlace.  Recomendamos que utilize o **HttpTrigger** modelo.

Neste cenário, o corpo do pedido da função do Azure seria o texto de tweet.  O código de função, basta defina lógica no se o texto de tweet contém uma palavra-chave ou uma expressão.  Foi possível manter a função de si próprio como simples ou complexas, conforme necessário para o cenário.

No final da função, basta devolva uma resposta para a aplicação lógica com alguns dados.  Isto pode ser um valor booleano simple (por exemplo, `containsKeyword`), ou um objeto complexo.

![Passo de função do Azure configurado][2]

> [!TIP]
> Ao aceder a uma resposta complexa de uma função de uma aplicação lógica, utilize a ação de analisar JSON.

Depois da função é guardada, é possível adicionar a aplicação de lógica criada acima.  Na aplicação lógica:

1. Clique para adicionar um **novo passo**
1. Selecione o **das funções do Azure** conector
1. Selecione para escolher uma função existente e navegue para a função criada
1. Enviar o **Tweet texto** para o **corpo do pedido**

## <a name="running-and-monitoring-the-solution"></a>Em execução e a solução de monitorização

Uma das vantagens de criação sem servidor orchestrations em Logic Apps é a depuração avançada e as capacidades de monitorização.  Qualquer run (histórico ou atual) pode ser visualizada no Visual Studio, o portal do Azure, ou através da REST API e SDKs.

Uma das formas mais fácil para testar uma aplicação lógica está a utilizar o **executar** botão no designer.  Ao clicar em **executar** irá continuar a consultar o acionador a cada cinco segundos até for detetado um evento e dê uma vista em direto como avança de ser execução.

Histories execução anteriores podem ser visualizados no painel de descrição geral no portal do Azure, ou utilizando o Explorador de nuvem do Visual Studio.

## <a name="creating-a-deployment-template-for-automated-deployments"></a>Criar um modelo de implementação para implementações automáticas

Depois de uma solução foi desenvolveu, podem ser capturada e implementada através de um modelo de implementação do Azure a qualquer região do Azure no mundo.  Isto é útil para os dois parâmetros de modificação para versões diferentes deste fluxo de trabalho, mas também para integrar esta solução num pipeline compilação e a versão.  Podem ser encontrados detalhes sobre como criar um modelo de implementação [neste artigo](logic-apps-create-deploy-template.md).

As funções do Azure também podem ser incorporadas no modelo de implementação - para a solução completa com todas as dependências pode ser gerida como um único modelo.  Um exemplo de um modelo de implementação de função pode ser encontrado no [repositório de modelo de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Passos seguintes

* [Veja outros exemplos e cenários para Azure Logic Apps](logic-apps-examples-and-scenarios.md)
* [Veja um vídeo com instruções sobre como criar esta solução ponto-a-ponto](http://aka.ms/logicappsdemo)
* [Saiba como processar e detetar exceções dentro de uma aplicação lógica](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
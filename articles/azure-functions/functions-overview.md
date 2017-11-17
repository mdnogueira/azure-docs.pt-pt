---
title: "Descrição geral das Funções do Azure | Microsoft Docs"
description: "Compreenda como utilizar as Funções do Azure para otimizar cargas de trabalho assíncronas em minutos."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d60c898225b944801504f38d536262134a31e021
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="an-introduction-to-azure-functions"></a>Uma introdução às Funções do Azure  
Funções do Azure é uma solução para uma fácil execução de pequenos blocos de código, ou "funções", na nuvem. É possível escrever apenas o código que necessita para o problema em questão, sem ter de se preocupar com toda a aplicação ou a infraestrutura para executá-la. As funções podem tornar o desenvolvimento ainda mais produtivo, e pode utilizar a linguagem de programação de escolha, tal como c#, F #, Node.js, Java ou PHP. Paga apenas para a hora que o seu código é executado e confie no Azure para dimensionar conforme necessário. As funções do Azure permite-lhe desenvolver [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) aplicações no Microsoft Azure.

Este tópico fornece uma descrição geral de alto nível das Funções do Azure. Se pretender avançar diretamente e começar a utilizar com as funções, comece por [criar a sua primeira função do Azure](functions-create-first-azure-function.md). Se estiver à procura de mais informações técnicas acerca das Funções, consulte a [referência para programadores](functions-reference.md).

## <a name="features"></a>Funcionalidades
Seguem-se algumas funcionalidades-chave de funções:

* **Escolha de idioma** - escreva funções com a sua escolha de c#, F # ou Javascript. Consulte [idiomas suportados](supported-languages.md) para outras opções.
* **Modelo de preços de pagamento por utilização** - Pague apenas o tempo despendido a executar o seu código. Veja a opção do plano de alojamento de Consumo na [secção preços](#pricing).  
* **Traga as suas próprias dependências** - As funções são compatíveis com NuGet e NPM, pelo que pode utilizar as suas bibliotecas favoritas.  
* **Segurança integrada** - Proteja as funções acionadas por HTTP com fornecedores de OAuth, como o Azure Active Directory, Facebook, Google, Twitter e Conta Microsoft.  
* **Integração simplificada** - Tire facilmente partido dos serviços do Azure e ofertas de software como serviço (SaaS). Consulte a [secção Integrações](#integrations) para obter alguns exemplos.  
* **Desenvolvimento flexível** - Code o direito de funções no portal ou configurar a integração contínua e implemente o seu código através do [GitHub](../app-service/scripts/app-service-cli-continuous-deployment-github.md), [Visual Studio Team Services](../app-service/scripts/app-service-cli-continuous-deployment-vsts.md)e outros [ferramentas de programação suportadas](../app-service/app-service-deploy-local-git.md).  
* **Código aberto** - O tempo de execução das Funções é de código aberto e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com as Funções?
As funções são uma excelente solução para processar os dados, integrar sistemas, trabalhar com a internet das coisas (IoT) e a criação de API simples e micro-serviços. Considere utilizar as Funções para tarefas como processamento de imagem ou encomendas, manutenção de ficheiros ou para quaisquer tarefas que pretende executar com base numa agenda. 

Funções fornece modelos para ajudar a começar com cenários-chave, que incluem:

* **HTTPTrigger** - Acionar a execução do seu código através da utilização de um pedido de HTTP. Por exemplo, consulte [criar a sua primeira função](functions-create-first-azure-function.md).
* **TimerTrigger** - Executar tarefas de limpeza ou outras tarefas de lote numa agenda predefinida. Por exemplo, consulte [criar uma função acionada por um temporizador](functions-create-scheduled-function.md).
* **GitHub webhook** - Responder a eventos que ocorrem nos seus repositórios do GitHub. Por exemplo, consulte [criar uma função acionada por um webhook do GitHub](functions-create-github-webhook-triggered-function.md).
* **Webhook genérico** - Processar os pedidos de webhook HTTP a partir de qualquer serviço que suporte webhooks. Por exemplo, consulte [criar uma função acionada por um webhook genérico](functions-create-generic-webhook-triggered-function.md).
* **CosmosDBTrigger** -base de dados do processo do Azure Cosmos documentos quando são adicionados ou atualizados na coleções numa base de dados NoSQL. Por exemplo, consulte [criar uma função acionada por base de dados do Azure Cosmos](functions-create-cosmos-db-triggered-function.md).
* **BlobTrigger** - Processa blobs de armazenamento do Azure quando são adicionados a contentores. Pode utilizar esta função para redimensionar a imagem. Para obter mais informações, consulte [enlaces de armazenamento de BLOBs](functions-bindings-storage-blob.md).
* **QueueTrigger** - Responder a mensagens à medida que chegam a uma fila de Armazenamento do Azure. Por exemplo, consulte [criar uma função acionada por armazenamento de filas do Azure](functions-create-storage-queue-triggered-function.md).
* **EventHubTrigger** - Responder a eventos fornecidos a um Hub de Eventos do Azure. É especialmente útil em cenários de instrumentação de aplicações, experiência do utilizador ou processamento de fluxo de trabalho e Internet das Coisas (IoT). Para obter mais informações, consulte [enlaces de Event Hubs](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger** - Ligar o seu código a outros serviços do Azure ou serviços no local através da escuta de filas de mensagens. Para obter mais informações, consulte [enlaces de Service Bus](functions-bindings-service-bus.md).
* **ServiceBusQueueTrigger** - Ligar o seu código a outros serviços do Azure ou serviços no local através da subscrição de tópicos. Para obter mais informações, consulte [enlaces de Service Bus](functions-bindings-service-bus.md).

Funções do Azure suporta *acionadores*, que são formas de iniciar a execução do seu código, e *enlaces*, que são formas para simplificar a codificação de dados de entrada e saída. Para obter uma descrição detalhada dos acionadores e enlaces fornecidos pelas Funções do Azure, consulte o artigo [Referência para programadores de acionadores e enlaces das Funções do Azure](functions-triggers-bindings.md).

## <a name="integrations"></a>ntegrações
Funções do Azure integra-se com vários serviços do Azure e de terceiros. Estes serviços podem acionar a sua função e iniciar a execução ou podem servir como entrada e saída para o seu código. As seguintes integrações de serviço são suportadas funções do Azure:

* Azure Cosmos DB
* Azure Event Hubs 
* Azure Event Grid
* Azure Mobile Apps (tabelas)
* Azure Notification Hubs
* Azure Service Bus (filas e tópicos)
* Armazenamento do Azure (blob, filas e tabelas) 
* GitHub (webhooks)
* No local (utilizando o Service Bus)
* Twilio (mensagens SMS)

## <a name="pricing"></a>Quanto custam as Funções?
As funções do Azure tem dois tipos de planos de preços. Escolha a que melhor se adequa às suas necessidades: 

* **Plano de Consumo** - Quando a função é executada, o Azure fornece todos os recursos informáticos necessários. Não tem de se preocupar com a gestão de recursos, paga apenas o tempo de execução do seu código. 
* **Plano do App Service** - Executar as suas funções, como aplicações da Web, telemóveis e API. Se já utilizar o App Service para outras aplicações, pode executar as suas funções no mesmo plano sem custos adicionais. 

Para obter mais informações sobre os planos de alojamento, consulte [das funções do Azure a comparação do plano de alojamento](functions-scale.md). Os detalhes completos sobre os preços estão disponíveis na [Preços das Funções](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Passos Seguintes
* [Criar a sua primeira Função do Azure](functions-create-first-azure-function.md)  
  Comece de imediato e crie a sua primeira função com o guia de introdução das Funções do Azure. 
* [Referência para programadores das Funções do Azure](functions-reference.md)  
  Fornece mais informações técnicas sobre o tempo de execução das Funções do Azure e uma referência para as funções de codificação e definição de acionadores e enlaces.
* [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.
* [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de alojamento de Consumo e como escolher o plano adequado. 
* [Saiba mais sobre o Azure App Service](../app-service/app-service-web-overview.md)  
  Tira partido de funções do Azure App Service do Azure para a funcionalidade principal, como implementações, variáveis de ambiente e diagnósticos. 


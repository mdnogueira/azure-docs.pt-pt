---
title: "Descrição geral das Funções do Azure | Microsoft Docs"
description: "Compreenda como utilizar as Funções do Azure para otimizar cargas de trabalho assíncronas em minutos."
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: cfowler;mahender;glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2fd12dd32ed3c8479c7460cbc0a1cac3330ff4f4
ms.openlocfilehash: 045ec40811468eeffb25ed28ec5bfc84f074c19d
ms.lasthandoff: 02/28/2017


---
# <a name="an-introduction-to-azure-functions"></a>Uma introdução às Funções do Azure  
Funções do Azure é uma solução para uma fácil execução de pequenos blocos de código, ou "funções", na nuvem. É possível escrever apenas o código que necessita para o problema em questão, sem ter de se preocupar com toda a aplicação ou a infraestrutura para executá-la. As funções podem tornar o desenvolvimento ainda mais produtivo, e podem utilizar uma linguagem de desenvolvimento à sua escolha, tal como C#, F#, Node.js, Python ou PHP. Paga apenas para a hora que o seu código é executado e confie no Azure para dimensionar conforme necessário. As Funções do Azure permitem desenvolver aplicações sem servidor no Microsoft Azure.

Este tópico fornece uma descrição geral de alto nível das Funções do Azure. Se quiser iniciar de imediato e utilizar as Funções do Azure, comece por [Criar a sua primeira Função do Azure](functions-create-first-azure-function.md). Se estiver à procura de mais informações técnicas acerca das Funções, consulte a [referência para programadores](functions-reference.md).

## <a name="features"></a>Funcionalidades
Seguem-se algumas funcionalidades-chave das Funções do Azure:

* **Escolha de idioma** - escreva funções com C#, F#, Node.js, Python, PHP, batch, bash ou qualquer executável.
* **Modelo de preços de pagamento por utilização** - Pague apenas o tempo despendido a executar o seu código. Veja a opção do plano de alojamento de Consumo na [secção preços](#pricing).  
* **Traga as suas próprias dependências** - As funções são compatíveis com NuGet e NPM, pelo que pode utilizar as suas bibliotecas favoritas.  
* **Segurança integrada** - Proteja as funções acionadas por HTTP com fornecedores de OAuth, como o Azure Active Directory, Facebook, Google, Twitter e Conta Microsoft.  
* **Integração simplificada** - Tire facilmente partido dos serviços do Azure e ofertas de software como serviço (SaaS). Consulte a [secção Integrações](#integrations) para obter alguns exemplos.  
* **Desenvolvimento flexível** - Codifique as suas funções no portal ou configure a integração contínua e implemente o seu código através do GitHub, dos Visual Studio Team Services e de outras [ferramentas de programação suportadas](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Código aberto** - O tempo de execução das Funções é de código aberto e [está disponível no GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>O que posso fazer com as Funções?
Funções do Azure é uma excelente solução para processar dados, integrar sistemas, trabalhar com a Internet das Coisas (IoT) e a criação de API simples e micro-serviços. Considere utilizar as Funções para tarefas como processamento de imagem ou encomendas, manutenção de ficheiros ou para quaisquer tarefas que pretende executar com base numa agenda. 

Funções fornece modelos para ajudar a começar com cenários-chave, que incluem:

* **BlobTrigger** - Processa blobs de armazenamento do Azure quando são adicionados a contentores. Pode utilizar esta função para redimensionar a imagem.
* **EventHubTrigger** - Responder a eventos fornecidos a um Hub de Eventos do Azure. É especialmente útil em cenários de instrumentação de aplicações, experiência do utilizador ou processamento de fluxo de trabalho e Internet das Coisas (IoT).
* **Webhook genérico** - Processar os pedidos de webhook HTTP a partir de qualquer serviço que suporte webhooks.
* **GitHub webhook** - Responder a eventos que ocorrem nos seus repositórios do GitHub. Por exemplo, consulte o artigo [Criar um webhook ou uma função de API](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** - Acionar a execução do seu código através da utilização de um pedido de HTTP.
* **QueueTrigger** - Responder a mensagens à medida que chegam a uma fila de Armazenamento do Azure. Por exemplo, veja [Criar uma Função do Azure vinculada a um serviço Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** - Ligar o seu código a outros serviços do Azure ou serviços no local através da escuta de filas de mensagens. 
* **ServiceBusQueueTrigger** - Ligar o seu código a outros serviços do Azure ou serviços no local através da subscrição de tópicos. 
* **TimerTrigger** - Executar tarefas de limpeza ou outras tarefas de lote numa agenda predefinida. Por exemplo, consulte o artigo [Criar uma função de processamento de eventos](functions-create-an-event-processing-function.md).

Funções do Azure suporta *acionadores*, que são formas de iniciar a execução do seu código, e *enlaces*, que são formas para simplificar a codificação de dados de entrada e saída. Para obter uma descrição detalhada dos acionadores e enlaces fornecidos pelas Funções do Azure, consulte o artigo [Referência para programadores de acionadores e enlaces das Funções do Azure](functions-triggers-bindings.md).

## <a name="integrations"></a>ntegrações
Funções do Azure integra-se com vários serviços do Azure e de terceiros. Estes serviços podem acionar a sua função e iniciar a execução ou podem servir como entrada e saída para o seu código. As Funções do Azure são compatíveis com as seguintes integrações de serviço. 

* Azure DocumentDB
* Azure Event Hubs 
* Azure Mobile Apps (tabelas)
* Azure Notification Hubs
* Azure Service Bus (filas e tópicos)
* Armazenamento do Azure (blob, filas e tabelas) 
* GitHub (webhooks)
* No local (utilizando o Service Bus)
* Twilio (mensagens SMS)

## <a name="pricing"></a>Quanto custam as Funções?
Funções do Azure tem dois tipos de planos de preços, escolha a que melhor se adequa às suas necessidades: 

* **Plano de Consumo** - Quando a função é executada, o Azure fornece todos os recursos informáticos necessários. Não tem de se preocupar com a gestão de recursos, paga apenas o tempo de execução do seu código. 
* **Plano do App Service** - Executar as suas funções, como aplicações da Web, telemóveis e API. Se já utilizar o App Service para outras aplicações, pode executar as suas funções no mesmo plano sem custos adicionais. 

Os detalhes completos sobre os preços estão disponíveis na [Preços das Funções](https://azure.microsoft.com/pricing/details/functions/). Para obter mais informações sobre como dimensionar as suas funções, consulte o artigo [Como dimensionar Funções do Azure](functions-scale.md).

## <a name="next-steps"></a>Passos Seguintes
* [Criar a sua primeira Função do Azure](functions-create-first-azure-function.md)  
  Comece de imediato e crie a sua primeira função com o guia de introdução das Funções do Azure. 
* [Referência para programadores das Funções do Azure](functions-reference.md)  
  Fornece mais informações técnicas sobre o tempo de execução das Funções do Azure e uma referência para as funções de codificação e definição de acionadores e enlaces.
* [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.
* [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de alojamento de Consumo e como escolher o plano adequado. 
* [Saiba mais sobre o Azure App Service](../app-service/app-service-value-prop-what-is.md)  
  Funções do Azure melhora a plataforma do Azure App Service para funcionalidades essenciais como implementações, variáveis de ambiente e diagnósticos. 



---
title: "Escolha entre fluxo, as Logic Apps, funções e WebJobs | Microsoft Docs"
description: "Comparar e contraste a nuvem integração dos serviços da Microsoft e decidir que serviços deve utilizar."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: "Microsoft fluxo, fluxo, as logic apps, as funções do azure, as funções do azure webjobs, webjobs, de processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: ab0aa377f9803d74d8a7a94bdb4c7b780e3ae41d
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Escolher entre Fluxo, Aplicações Lógicas, Funções e WebJobs
Este artigo compara e contrasta os seguintes serviços em nuvem da Microsoft, que podem resolver todos os problemas de integração e automatizar processos empresariais:

* [Fluxo da Microsoft](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Funções do Azure](https://azure.microsoft.com/services/functions/)
* [WebJobs do App Service do Azure](../app-service/web-sites-create-web-jobs.md)

Todos os estes serviços são úteis quando "gluing" em conjunto sistemas diferentes. Se todos os definir entrada, ações, condições e de saída. Pode executar cada um num agendamento ou acionador. No entanto, cada serviço tem vantagens exclusivas e comparar-los a questão não é de "serviço é o melhor?" mas uma das "serviço é melhor adequados para esta situação?" Muitas vezes, uma combinação destes serviços é a melhor forma para criar rapidamente uma solução de integração dimensionável e completo.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Fluxo vs. Aplicações Lógicas
Pode discutimos Microsoft Flow e Azure Logic Apps em conjunto por estarem ambos *configuração primeiro* serviços de integração. Estes facilitam a criação de processos e fluxos de trabalho e integrar com várias aplicações SaaS e enterprise. 

* Fluxo é desenvolvido com Logic Apps
* Têm o mesmo estruturador de fluxo de trabalho
* [Conectores](../connectors/apis-list.md) trabalho num pode também funcionar no outro

Fluxo garante qualquer trabalho do office para efetuar integrações simples (por exemplo, get SMS para e-mails importantes) sem passar os programadores ou IT. Por outro lado, as Logic Apps podem ativar avançadas ou fundamentais integrações (por exemplo, processos B2B) onde são necessárias as práticas de DevOps e segurança de nível empresarial. É típica para um fluxo de trabalho do negócio a crescer num complexidade, ao longo do tempo. Em conformidade, pode começar com um fluxo em primeiro lugar, em seguida, convertê-lo para uma aplicação lógica, conforme necessário.

A tabela seguinte ajuda-o a determinar se é melhores para uma determinada integração fluxo ou Logic Apps.

|  | Fluxo | Aplicações Lógicas |
| --- | --- | --- |
| Audiência |colegas, os utilizadores empresariais |Profissionais de TI, os programadores |
| Cenários |Self-Service |Fundamentais |
| Ferramenta de estrutura |Aplicações baseadas no browser e dispositivos móveis, apenas a IU |No browser e [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Code vista](../logic-apps/logic-apps-author-definitions.md) disponíveis |
| DevOps |Ad-hoc, desenvolver na produção |origem de controlo, testar, suporte e automatização e capacidade de gestão em [gestão de recursos do Azure](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Experiência da administração |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.Azure.com](https://portal.azure.com) |
| Segurança |Práticas padrão: [soberania de dados](https://wikipedia.org/wiki/Technological_Sovereignty), [encriptação de Inativos](https://wikipedia.org/wiki/Data_at_rest#Encryption) para dados confidenciais, etc. |Garantia de segurança do Azure: [segurança do Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Centro de segurança](https://azure.microsoft.com/services/security-center/), [registos de auditoria](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)e muito mais. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>As funções vs. Trabalhos Web
Pode discutimos das funções do Azure e WebJobs de serviço de aplicações do Azure em conjunto por estarem ambos *primeiro código* integração de serviços e foi concebido para programadores. Dão-lhe executar um script ou um fragmento de código em resposta a vários eventos, tal como [novos de armazenamento de Blobs](functions-bindings-storage.md) ou [um pedido de WebHook](functions-bindings-http-webhook.md). Seguem-se os seus semelhanças: 

* Ambos são criadas numa [App Service do Azure](../app-service/app-service-web-overview.md) e desfrutar funcionalidades, tais como [controlo de origem](../app-service/app-service-continuous-deployment.md), [autenticação](../app-service/app-service-authentication-overview.md), e [monitorização](../app-service/web-sites-monitor.md).
* Ambos são centrados na programação.
* O suporte de scripts padrão e linguagens de programação.
* Têm NuGet e NPM suportar.

As funções são a evolução natural de WebJobs demora a melhor coisas sobre WebJobs e melhora nelas. As melhorias incluem: 

* [Sem servidor](https://azure.microsoft.com/overview/serverless-computing/) modelo de aplicação.
* Dev simplificada, testar e execução do código, diretamente no browser.
* Como de integração incorporada com serviços mais do Azure e os serviços de terceiros 3rd [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Pagamento por utilização, não é necessário pagar para um [plano do App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* Automática, [dimensionamento dinâmico](functions-scale.md).
* Para clientes existentes do serviço de aplicações em execução no plano de serviço aplicacional ainda possível (tirar partido dos recursos sob utilizados).
* Integração com Logic Apps.

A tabela seguinte resume as diferenças entre as funções e WebJobs:

|  | Funções | Trabalhos Web |
| --- | --- | --- |
| Dimensionamento |Dimensionamento configurationless |Dimensionar com o plano do App Service |
| Preços |Pagamento por utilização ou parte do plano do App Service |Parte do plano de serviço de aplicações |
| Tipo de execução |acionado, agendada (por acionador de temporizador) |accionadas, contínua, agendada |
| Eventos de Acionador |[Temporizador](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [Event Hubs do Azure](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [aplicações móveis do App Service do Azure](functions-bindings-mobile-apps.md), [Notification Hubs do azure](functions-bindings-notification-hubs.md), [Service Bus do Azure](functions-bindings-service-bus.md), [Storage do Azure](functions-bindings-storage-blob.md) |[Armazenamento do Azure](functions-bindings-storage-blob.md), [Service Bus do Azure](functions-bindings-service-bus.md) |
| Desenvolvimento baseadas no browser |Suportado |Não suportado |
| C# |Suportado |Suportado |
| F# |Suportado |Não suportado |
| JavaScript |Suportado |Suportado |
| Java |Suportado | Não suportado |
| Bash |Experimental |Suportado |
| Windows scripting (. cmd,. bat) |Experimental |Suportado |
| PowerShell |Experimental |Suportado |
| PHP |Experimental |Suportado |
| Python |Experimental |Suportado |
| TypeScript |Experimental |Não suportado |

Se pretende utilizar as funções ou WebJobs depende basicamente que está já a fazer com o serviço de aplicações. Se tiver uma aplicação de serviço de aplicações para o qual pretende executar fragmentos de código e pretender geri-los em conjunto no mesmo ambiente DevOps, utilize WebJobs. Nos seguintes cenários, utilize as funções.

* Pretende executar fragmentos de código para outros serviços do Azure ou aplicações de terceiros 3rd.
* Pretende gerir o seu código de integração em separado das suas aplicações de serviço de aplicações.
* Pretende chamar fragmentos de código a partir de uma aplicação lógica. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Fluxo, as Logic Apps e as funções em conjunto
Como mencionadas anteriormente, o serviço é mais adequado para si depende da situação existente. 

* Para a otimização de negócio simples, utilize o fluxo.
* Se o seu cenário de integração é demasiado avançado do fluxo de ou se precisa de capacidades de DevOps e compliances de segurança, em seguida, utilize Logic Apps.
* Se necessitar de um passo no seu cenário de integração transformação altamente personalizada ou um código especializado, em seguida, escreva uma função e acionar a função como uma ação na sua aplicação lógica.

Pode ligar a uma aplicação lógica de um fluxo. Também pode chamar uma função de uma aplicação lógica e uma aplicação lógica de uma função. A integração entre o fluxo das Logic Apps, funções e continua a melhorar o ao longo do tempo. Pode criar algo um serviço e utilizá-lo em outros serviços. Por conseguinte, qualquer investimento que tomar nestas três tecnologias é valer a pena.

## <a name="next-steps"></a>Passos seguintes
Comece com cada um dos serviços ao criar a sua primeira fluxo, aplicação lógica, aplicação de função ou trabalho Web. Clique em qualquer uma das ligações seguintes:

* [Introdução ao Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Criar a sua primeira Função do Azure](functions-create-first-azure-function.md)
* [Implementar o WebJobs com o Visual Studio](../app-service/websites-dotnet-deploy-webjobs.md)

Em alternativa, obter mais informações sobre estes serviços de integração com as seguintes ligações:

* [Tirar partido das funções do Azure e o App Service do Azure para cenários de integração por Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrações graças à simples Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [As Logic Apps Live Webcast](http://aka.ms/logicappslive)
* [Microsoft fluxo perguntas mais frequentes](https://flow.microsoft.com/documentation/frequently-asked-questions/)


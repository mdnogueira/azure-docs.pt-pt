---
title: "Exemplos e cenários comuns - Azure Logic Apps | Microsoft Docs"
description: "Saiba mais sobre as logic apps com exemplos, cenários, tutoriais e instruções"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 09/13/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 5b2b82d90dee41e80233e5f52c960be23d89ee3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Comuns cenários, exemplos, tutoriais e instruções para Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) ajuda-o a orquestrar e integrar os diferentes serviços fornecendo [100 + prontos a utilizar conectores](../connectors/apis-list.md), vão de SQL Server no local ou SAP cognitivos serviços da Microsoft. O serviço de aplicações lógicas é "sem servidor", pelo que não precisa de preocupar com escala ou instâncias. Tudo o que precisa de executar é definir o fluxo de trabalho com um acionador e as ações que executa o fluxo de trabalho. A plataforma subjacente processa escala, disponibilidade e desempenho. As Logic Apps é especialmente útil para cenários em que terá de coordenar várias ações em vários sistemas e de casos de utilização.

Para saber mais sobre a muitos padrões e capacidades que [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) suporta, seguem-se exemplos e cenários comuns.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Pontos de partida para fluxos de trabalho de aplicação de lógica populares

Cada aplicação lógica começa com um [ *acionador*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)e apenas um acionador, que inicia o fluxo de trabalho de aplicação de lógica e passa em todos os dados como parte desse acionador. Alguns conectores fornecem acionadores, que são fornecidos em destes tipos:

* *Acionadores de consulta*: verifica regularmente um ponto final de serviço para novos dados. Quando existem novos dados, o acionador cria e é executada uma nova instância de fluxo de trabalho com os dados como entrada.

* *Push acionadores*: escuta de dados de um ponto final de serviço e aguarda até que ocorre um evento específico. Quando o evento acontece, o acionador desencadeado imediatamente, criar e executar uma nova instância de fluxo de trabalho que utiliza todos os dados disponíveis como entrada.

Seguem-se alguns exemplos de Acionador populares:

* Consulta: 

  * [**Agenda - periodicidade** acionador](../connectors/connectors-native-recurrence.md) permite-lhe definir a data de início e a hora mais a periodicidade para acionando a sua aplicação lógica. 
  Por exemplo, pode selecionar os dias da semana e as horas do dia para acionar a sua aplicação lógica.

  * O acionador "Quando é recebida uma mensagem de e-mail" permite que a sua aplicação lógica, verifique a existência de novo e-mail a partir de qualquer fornecedor de correio eletrónico que é suportado pelo Logic Apps, por exemplo, [Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com](https://docs.microsoft.com/connectors/outlook/), e assim sucessivamente.

  * O [ **HTTP** acionador](../connectors/connectors-native-http.md) permite que a sua aplicação lógica, verifique um ponto final de serviço especificado ao comunicar através de HTTP.
  
* Push:

  * O [ **pedido / resposta - pedido** acionador](../connectors/connectors-native-reqres.md) permite que a sua aplicação lógica receber pedidos HTTP e respondem em tempo real para eventos de alguma forma.

  * O [ **HTTP Webhook** acionador](../connectors/connectors-native-webhook.md) subscreve um ponto final de serviço ao registar um *URL de chamada de retorno* com esse serviço. 
  Dessa forma, o serviço pode apenas notificar o acionador quando o evento especificado acontece, para que o acionador não necessita de consultar o serviço.

Depois de receber uma notificação sobre novos dados ou um evento, o acionador é acionado, cria uma nova instância de fluxo de trabalho de aplicação de lógica e executa as ações no fluxo de trabalho. Pode aceder a todos os dados de Acionador em todo o fluxo de trabalho. Por exemplo, o acionador "num tweet novo" passa o conteúdo de tweet para a aplicação de lógica executar. 

## <a name="respond-to-triggers-and-extend-actions"></a>Responder a acionadores e ações de expandir

Para sistemas e serviços que não podem ter publicado conectores, também pode expandir as logic apps.

* [Criar acionadores personalizados ou ações](../logic-apps/logic-apps-create-api-app.md)
* [Configurar as ações de execução longa para execuções de fluxo de trabalho](../logic-apps/logic-apps-create-api-app.md)
* [Responder a eventos externos e ações com webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Chamar, acionador, ou aninhar fluxos de trabalho com síncronas respostas a pedidos de HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Tutorial: Criar um dashboard com tecnologia AI social em minutos com Logic Apps e o Power BI](http://aka.ms/logicappsdemo)
* [Tutorial: Responder a webhooks de SMS do Twilio e enviar uma resposta de texto](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Fluxo de controlo, processamento de erros e capacidades de registo

As Logic apps incluem capacidades avançadas de fluxo de controlo avançadas, como as condições, comutadores, ciclos e âmbitos. Para garantir soluções resilientes, também pode implementar erro e excepção a processar nos seus fluxos de trabalho. Para notificação e registos de diagnóstico de estado de execução do fluxo de trabalho, Azure Logic Apps também fornece monitorização e alertas.

* [Processo de itens em matrizes e coleções com ciclos e lotes em logic apps](../logic-apps/logic-apps-loops-and-scopes.md)
* [Executar ações diferentes com declarações de comutador](../logic-apps/logic-apps-switch-case.md)
* [Erro de autor e excepção a processar num fluxo de trabalho](../logic-apps/logic-apps-exception-handling.md)
* [Caso de utilização: como uma empresa de cuidados de saúde utiliza a exceção de aplicação de lógica de processamento para fluxos de trabalho HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Ativar monitorização, registo e alertas para logic apps existentes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Ativar o registo de diagnóstico e de monitorização ao criar as logic apps](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Implementar e gerir as logic apps

Totalmente pode desenvolver e implementar aplicações lógicas com o Visual Studio, os Visual Studio Team Services, ou quaisquer outros controlo de origem e ferramentas de compilação automatizada. Para suportar a implementação para fluxos de trabalho e ligações dependentes num modelo de recursos, as logic apps utilizam modelos de implementação de recursos do Azure. Ferramentas do Visual Studio geram automaticamente estes modelos, que pode der entrada do controlo de origem para o controlo de versões.

* [Criar e implementar as logic apps a partir do Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Ativar monitorização, registo e alertas para logic apps existentes](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Criar um modelo de implementação automatizada](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipos de conteúdo, conversões e transformações dentro de uma execução

Pode aceder, converta e transformar vários tipos de conteúdo utilizando as muitas funções no Azure Logic Apps [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs). Por exemplo, pode converter entre uma cadeia, JSON e XML com o `@json()` e `@xml()` expressões de fluxo de trabalho. O motor de Logic Apps preserva tipos de conteúdo para suportar a transferência de conteúdo de uma forma sem perdas entre serviços.

* [Como expressões de fluxo de trabalho funcionam em aplicações lógicas](../logic-apps/logic-apps-author-definitions.md)
* [Processar tipos de conteúdo JSON não](../logic-apps/logic-apps-content-type.md), como `application/xml`, `application/octet-stream`, e`multipart/formdata`
* [Referência: Linguagem de definição de fluxo de trabalho de aplicações lógicas do Azure](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Outras capacidades e integrações

As Logic apps oferecem também a integração com vários serviços, como as funções do Azure, API Management do Azure, serviços de aplicações do Azure e HTTP pontos finais personalizados, por exemplo, REST e SOAP.

* [Criar um dashboard de redes social em tempo real com sem servidor do Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Chamar as funções do Azure a partir das logic apps](../logic-apps/logic-apps-azure-functions.md)
* [Cenário: Acionar as logic apps com as funções do Azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Blogue: Chamar SOAP pontos finais de aplicações lógicas](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Cenários ponto a ponto

* [Documento técnico: Integração ponto-a-ponto maiúsculas gestão empresarial com os serviços do Azure, como as Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="next-steps"></a>Passos seguintes

* [Definições de fluxo de trabalho de autor com a linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-author-definitions.md)
* [Processar os erros e exceções em aplicações lógicas](../logic-apps/logic-apps-exception-handling.md)
* [Submeter os comentários, perguntas, comentários ou sugestões para como podemos melhorar Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
---
title: Criar web APIs e REST APIs para o Azure Logic Apps | Microsoft Docs
description: "Criar web APIs e REST APIs para chamar as APIs, serviços ou sistemas de fluxos de trabalho de aplicação de lógica integrações de sistema"
keywords: "Web APIs, REST APIs, fluxos de trabalho, integrações de sistema"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/26/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 2a8b883975ed0c0a2a6ee9a2a7ad0c0b1e938fd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-apis-that-you-can-call-from-logic-app-workflows"></a>Criar APIs personalizadas que pode chamar a partir de fluxos de trabalho de aplicação de lógica

Embora o Azure Logic Apps oferece [100 + conectores incorporados](../connectors/apis-list.md) que pode utilizar em fluxos de trabalho de aplicação de lógica, pode querer chamar APIs, sistemas e serviços que não estão disponíveis como conectores. Pode criar os suas próprias APIs que fornecer ações e acionadores para utilizar aplicações lógicas. Aqui estão outros motivos por que razão poderá pretender criar os suas próprias APIs que pode chamar a partir de fluxos de trabalho de aplicação de lógica:

* Expanda o seu atual sistema de integração e dados de fluxos de trabalho de integração.
* Ajuda os clientes de utilizar o seu serviço para gerir as tarefas profissionais ou pessoais.
* Expanda o alcance, a capacidade de deteção e a utilização do seu serviço.

Fundamentalmente, os conectores são APIs que utilizam a REST para interfaces incorporável, web [formato de metadados do Swagger](http://swagger.io/specification/) para documentação e JSON como respetivo formato dos dados de exchange. Uma vez que os conectores são APIs REST que comunicam através de pontos finais HTTP, pode utilizar qualquer idioma, como .NET, Java ou Node.js, para a criação de conectores. Também pode alojar as suas APIs em [App Service do Azure](../app-service/app-service-web-overview.md), um plataforma-como-um-serviço (PaaS) que fornece uma das formas mais dimensionáveis, mais fácil e prática para alojar a API da oferta. 

Para obter APIs personalizadas trabalhar com as logic apps, pode fornecer a API [ *ações* ](./logic-apps-what-are-logic-apps.md#logic-app-concepts) que realizam tarefas específicas em fluxos de trabalho de aplicação de lógica. A API pode também agir como um [ *acionador* ](./logic-apps-what-are-logic-apps.md#logic-app-concepts) que inicia um fluxo de trabalho de aplicação lógica quando novos dados ou um evento cumpre uma condição especificada. Este tópico descreve os padrões comuns que pode seguir para a criação de ações e acionadores na sua API, com base no comportamento que pretende que a API para fornecer.

Pode alojar as suas APIs em [App Service do Azure](../app-service/app-service-web-overview.md), um plataforma-como-um-serviço (PaaS) que fornece altamente dimensionável e fácil que aloja a API da oferta.

> [!TIP] 
> Embora possa implementar as suas APIs como as web apps, considere implementar as suas APIs como API apps, que podem facilitar a sua tarefa quando criar, anfitrião e consumir APIs na nuvem e no local. Não tem de alterar os códigos na suas APIs – basta implementar o seu código numa aplicação API. Por exemplo, saiba como criar aplicações API criadas com estes idiomas: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [python](../app-service/app-service-web-get-started-python.md)
>
> Para obter exemplos de aplicação de API criados para as logic apps, aceda a [repositório do GitHub do Azure Logic Apps](http://github.com/logicappsio) ou [blogue](http://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Como diferem APIs personalizadas a partir dos conectores personalizados?

APIs personalizadas e [conectores personalizados](../logic-apps/custom-connector-overview.md) são APIs que utilizam a REST para interfaces incorporável, web [formato de metadados do Swagger](http://swagger.io/specification/) para documentação e JSON como respetivo formato dos dados de exchange. E porque estas APIs e conectores são APIs REST que comunicam através de pontos finais HTTP, pode utilizar qualquer idioma, como .NET, Java ou Node.js, para a criação de APIs personalizadas e conectores.

APIs personalizadas permitem-lhe chamar APIs que não são conectores e fornecer pontos finais que pode chamar com HTTP + Swagger, API Management do Azure ou serviços de aplicação. Conectores personalizados funcionam como APIs personalizadas, mas também tem estes atributos:

* Registado como recursos de conector de aplicações lógicas no Azure.
* São apresentados com ícones juntamente com conectores gerida pela Microsoft no Designer de aplicações lógicas.
* Disponível apenas para autores e utilizadores de aplicação lógica que tem o mesmo inquilino do Azure Active Directory e a subscrição do Azure na região onde as aplicações lógicas são implementadas dos conectores.

Também pode para indicar conectores registados para certificação da Microsoft. Este processo verifica se conectores registados cumprem os critérios para utilização público e disponibiliza os conectores para utilizadores na Microsoft Flow e Microsoft PowerApps.

Para obter mais informações sobre conectores personalizadas, consulte 

* [Descrição geral dos conectores personalizados](../logic-apps/custom-connector-overview.md)
* [Criar conectores personalizados a partir de APIs da Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registar conectores personalizados no Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Ferramentas úteis

Uma API personalizada funciona melhor com logic apps quando a API também tem um [documento Swagger](http://swagger.io/specification/) que descreve os parâmetros e operações da API.
Como muitas bibliotecas, [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), pode gerar automaticamente o ficheiro Swagger para si. Anotar o ficheiro Swagger para os nomes a apresentar, tipos de propriedade e assim sucessivamente, também pode utilizar [TRex](https://github.com/nihaue/TRex) para que o ficheiro Swagger funciona bem com as logic apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Padrões de ação

Para aplicações lógicas efetuar tarefas, a API personalizada deve fornecer [ *ações*](./logic-apps-what-are-logic-apps.md#logic-app-concepts). Cada operação na sua API mapeia para uma ação. Uma ação básica é um controlador que aceite pedidos de HTTP e devolve as respostas de HTTP. Por isso, por exemplo, uma aplicação lógica envia um pedido HTTP para a sua aplicação web ou aplicação API. A aplicação, em seguida, devolve uma resposta HTTP, juntamente com conteúdo que pode processar a aplicação lógica.

Para uma ação padrão, pode escrever um método de pedido HTTP na sua API e descrevem esse método num ficheiro Swagger. Em seguida, pode chamar a API diretamente com um [ação HTTP](../connectors/connectors-native-http.md) ou um [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) ação. Por predefinição, as respostas tem de ser devolvidas dentro de [limite de tempo limite do pedido](./logic-apps-limits-and-config.md). 

![Padrão de ação padrão](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Para tornar uma aplicação lógica, aguarde enquanto a API acaba de tarefas de execução mais, a sua API pode seguir o [consulta assíncrona padrão](#async-pattern) ou o [webhook assíncrona padrão](#webhook-actions) descritas neste tópico. Para uma analogia que ajuda a visualizar comportamentos diferentes destes padrões, imagine o processo para um cake personalizada de um bakery de ordenação. O padrão de consulta reflete o comportamento onde tem de chamar o bakery cada 20 minutos para verificar se o cake está pronto. O padrão de webhook reflete o comportamento onde o bakery pede-lhe para o número de telefone para que podem chamar quando o cake está pronto.

Para exemplos, visite o [repositório do GitHub de aplicações lógicas](https://github.com/logicappsio). Além disso, saiba mais sobre [medição de utilização para ações](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Executar tarefas de longa execução com o padrão de ação de consulta

Para ter a sua API efetuar tarefas que foi executada mais do que o [limite de tempo limite do pedido](./logic-apps-limits-and-config.md), pode utilizar o padrão assíncrono de consulta. Este padrão tem efetue a API de trabalho num thread separado, mas manter uma ligação ativa para o motor de Logic Apps. Dessa forma, a aplicação lógica não não o tempo limite ou continuar com o passo seguinte no fluxo de trabalho antes de trabalhar de conclusão da sua API.

Eis o padrão geral:

1. Certifique-se de que o motor sabe que a API aceitou o pedido e começou a funcionar.
2. Quando o motor efetua os pedidos subsequentes para o estado da tarefa, permitir que o motor de saber quando a API é concluída a tarefa.
3. Devolva dados relevantes para o motor para que possa continuar o fluxo de trabalho de aplicação lógica.

<a name="bakery-polling-action"></a>Agora se aplicam a analogia bakery anterior para o padrão de consulta e, imagine que tem de chamar um bakery e ordem um cake personalizado para entrega. O processo para tornar o cake demora tempo e não pretender aguardar no telemóvel, enquanto o bakery funciona no cake. O bakery confirma que a sua encomenda e tem que chamar a cada 20 minutos para o estado do cake. Após passar de 20 minutos, chame o bakery, mas indicam que a sua cake não é efetuada e que deve chamar noutra 20 minutos. Este processo de back-e-estabelecido continua até chamar e o bakery indica que a encomenda está preparada e fornece o cake. 

Por isso, vamos mapear este padrão de consulta novamente. O bakery representa a API personalizada, enquanto que o cliente cake, representa o motor de Logic Apps. Quando o motor chama a API com um pedido, a API confirma que o pedido e responde com o intervalo de tempo, quando o motor pode verificar o estado da tarefa. O motor continua a verificar o estado da tarefa até que a sua API responde que a tarefa é efetuada e devolve dados para a sua aplicação lógica, que, em seguida, continua o fluxo de trabalho. 

![Padrão de ação de consulta](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Eis os passos específicos para a API a seguir, descrito da perspetiva da API:

1. Quando a API obtém um pedido HTTP para iniciar o trabalho, imediatamente devolver um HTTP `202 ACCEPTED` resposta com o `location` cabeçalho descrito mais à frente neste passo. Esta resposta permite que o motor de Logic Apps sabe que a API recebeu o pedido, aceite o payload de pedido (dados de entrada) e, agora está a processar. 
   
   O `202 ACCEPTED` resposta deve incluir estes cabeçalhos:
   
   * *Necessário*: A `location` cabeçalho que especifica o caminho absoluto para um URL onde o motor de Logic Apps pode verificar o estado da tarefa a API

   * *Opcional*: A `retry-after` cabeçalho que especifica o número de segundos que o motor deve aguardar antes de a verificar o `location` URL para o estado da tarefa. 

     Por predefinição, o motor verifica cada 20 segundos. Para especificar um intervalo de diferentes, inclua o `retry-after` cabeçalho e o número de segundos até a consulta seguinte.

2. Depois de passa o período de tempo especificado, as Logic Apps motor inquéritos o `location` URL para verificar o estado da tarefa. A API deve efetuar estas verificações e devolver essas respostas:
   
   * Se a tarefa é feita, devolver um HTTP `200 OK` resposta, juntamente com o payload de resposta (entrada para o passo seguinte).

   * Se a tarefa ainda está a processar, devolver HTTP outro `202 ACCEPTED` resposta, mas com cabeçalhos do mesmos como resposta original.

Quando a API segue este padrão, não têm de fazer nada na definição de fluxo de trabalho da aplicação lógica para continuar a verificar o estado da tarefa. Quando o motor obtém um HTTP `202 ACCEPTED` resposta e um ID válido `location` cabeçalho, o motor respeita o padrão assíncrono e verifica a `location` cabeçalho até que a sua API devolve uma resposta não 202.

> [!TIP]
> Para um padrão assíncrono de exemplo, reveja este [exemplo de resposta do controlador assíncrona no GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Executar tarefas de longa execução com o padrão de ação do webhook

Como alternativa, pode utilizar o padrão de webhook para tarefas demoradas e processamento assíncrono. Neste padrão de tenha a aplicação de lógica colocar em pausa e aguardar um "callback" a partir da sua API a conclusão do processamento antes de continuar o fluxo de trabalho. Esta chamada de retorno é um HTTP POST que envia uma mensagem para um URL, quando ocorre um evento. 

<a name="bakery-webhook-action"></a>Agora aplicar a analogia bakery anterior para o padrão de webhook e imagine que tem de chamar um bakery e ordem um cake personalizado para entrega. O processo para tornar o cake demora tempo e não pretender aguardar no telemóvel, enquanto o bakery funciona no cake. O bakery confirma que a sua ordem, mas neste momento, pode conceder-lhes o número de telefone, para que podem chamar quando é feito o cake. Neste momento, o bakery indica quando a encomenda está pronta e entrega o cake.

Quando é mapear novamente este padrão de webhook, o bakery representa a API personalizada, enquanto que o cliente cake, representa o motor de Logic Apps. O motor chama a API com um pedido e inclui um URL de "callback".
Quando terminar a tarefa, a API utiliza o URL para notificar o motor e devolver dados para a sua aplicação lógica, que, em seguida, continua o fluxo de trabalho. 

Para este padrão, configurar dois pontos finais no seu controlador: `subscribe` e`unsubscribe`

*  `subscribe`ponto final: quando a execução atinge a ação da sua API no fluxo de trabalho, as Logic Apps motor chamadas a `subscribe` ponto final. Este passo faz com que a aplicação lógica para criar um URL de chamada de retorno que armazena a API e, em seguida, aguarde que a chamada de retorno da sua API quando o trabalho está concluído. A API, em seguida, chama-se novamente com um HTTP POST para o URL e passa a qualquer conteúdo devolvido e cabeçalhos como entrada para a aplicação lógica.

* `unsubscribe`ponto final: se a aplicação de lógica executar foi cancelada, as Logic Apps motor chamadas a `unsubscribe` ponto final. A API, em seguida, pode anular o registo o URL de chamada de retorno e parar quaisquer processos conforme necessário.

![Padrão de ação do Webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Atualmente, o Designer de aplicação lógica não suporta a deteção de webhook de pontos finais através do Swagger. Para este padrão, terá de adicionar um [ **Webhook** ação](../connectors/connectors-native-webhook.md) e especifique o URL, cabeçalhos e corpo do pedido. Consulte também [ações de fluxo de trabalho e é acionado](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Para passar o URL de chamada de retorno, pode utilizar o `@listCallbackUrl()` função de fluxo de trabalho em nenhum dos campos anteriores conforme necessário.

> [!TIP]
> Para um padrão de webhook de exemplo, reveja este [exemplo de Acionador de webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Padrões de Acionador

A API personalizada pode atuar como um [ *acionador* ](./logic-apps-what-are-logic-apps.md#logic-app-concepts) que inicia uma aplicação lógica quando novos dados ou um evento cumpre uma condição especificada. Este acionador pode verificar regularmente, ou aguardar e escutar novos dados ou eventos em que o ponto final de serviço. Se os novos dados ou um evento cumpre a condição especificada, o acionador desencadeado e inicia a aplicação lógica, que está à escuta para este acionador. Para iniciar as logic apps desta forma, pode seguir a API de [ *acionador de consulta* ](#polling-triggers) ou [ *acionador de webhook* ](#webhook-triggers) padrão. Estes padrões são semelhantes para os seus homólogos para [consulta ações](#async-pattern) e [as ações de webhook](#webhook-actions). Além disso, saiba mais sobre [medição de utilização para acionadores](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Verifique a existência de novos dados ou eventos regularmente com o padrão de Acionador de consulta

A *acionador de consulta* funciona muito semelhantes às de [consulta ação](#async-pattern) descrito anteriormente neste tópico. O motor de Logic Apps periodicamente as chamadas e verifica o ponto final de Acionador novos dados ou eventos. Se o motor de localizar novos dados ou um evento que satisfaça a condição especificada, o acionador é acionado. Em seguida, o motor cria uma instância da aplicação lógica que processa os dados como entrada. 

![Padrão de Acionador de consulta](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Contagens de cada pedido de consulta como uma execução de ação, mesmo quando não é criada nenhuma instância de aplicação lógica. Para impedir que os mesmos dados a processar várias vezes, o acionador deve limpar os dados que já foi lidos e transmitidos para a aplicação lógica.

Eis os passos específicos para um acionador de consulta, descrito da perspetiva da API:

| Foram encontrados novos dados ou eventos?  | Resposta de API | 
| ------------------------- | ------------ |
| Foi encontrado | Devolver uma HTTP `200 OK` Estado com o payload de resposta (entrada para o passo seguinte). <br/>Esta resposta cria uma instância da aplicação lógica e inicia o fluxo de trabalho. | 
| Não foi encontrado | Devolver uma HTTP `202 ACCEPTED` Estado com um `location` cabeçalho e um `retry-after` cabeçalho. <br/>Para acionadores, o `location` cabeçalho deve conter também uma `triggerState` parâmetro de consulta, que é normalmente um "timestamp". A API pode utilizar este identificador para controlar a última vez que a aplicação lógica foi acionada. | 
||| 

Por exemplo, para verificar periodicamente o serviço para os novos ficheiros, poderá criar um acionador de consulta que tenha destes comportamentos:

| Pedido inclui `triggerState`? | Resposta de API | 
| -------------------------------- | -------------| 
| Não | Devolver uma HTTP `202 ACCEPTED` Estado plus um `location` cabeçalho com `triggerState` definido com a hora atual e o `retry-after` intervalo para 15 segundos. | 
| Sim | Verifique o seu serviço para os ficheiros adicionados depois do `DateTime` para `triggerState`. | 
||| 

| Número de ficheiros foi encontrado | Resposta de API | 
| --------------------- | -------------| 
| Ficheiro único | Devolver uma HTTP `200 OK` atualizar o estado e o payload de conteúdo, `triggerState` para o `DateTime` para o ficheiro devolvido e defina `retry-after` intervalo para 15 segundos. | 
| Vários ficheiros | Devolver um ficheiro de um período de tempo e um HTTP `200 OK` Estado, atualizar `triggerState`e defina o `retry-after` intervalo de 0 segundos. </br>Estes passos permitem o motor de saber que estão disponíveis mais dados, e que o motor imediatamente deve pedir os dados de URL no `location` cabeçalho. | 
| Não existem ficheiros | Devolver uma HTTP `202 ACCEPTED` Estado, não altere `triggerState`e defina o `retry-after` intervalo para 15 segundos. | 
||| 

> [!TIP]
> Para obter um exemplo de consulta padrão de Acionador, reveja este [exemplo de controlador de Acionador de consulta no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Aguarde e escutar novos dados ou eventos com o padrão de Acionador de webhook

Um acionador de webhook é um *acionador de push* que deve aguardar e escuta novos dados ou eventos em que o ponto final de serviço. Se os novos dados ou um evento cumpre a condição especificada, o acionador desencadeado e cria uma instância de aplicação lógica, que processa os dados como entrada.
Acionadores de Webhook atuam muito semelhantes às de [as ações de webhook](#webhook-actions) anteriormente descritas neste tópico e estão configurados com `subscribe` e `unsubscribe` pontos finais. 

* `subscribe`ponto final: quando adicionar e guardar um acionador de webhook na sua aplicação lógica, as Logic Apps motor chamadas a `subscribe` ponto final. Este passo faz com que a aplicação lógica para criar um URL de chamada de retorno que armazena a sua API. Quando há novos dados ou um evento que cumpre a condição especificada, a chamadas à API novamente com um HTTP POST para o URL. O payload de conteúdo e os cabeçalhos de passar como entrada para a aplicação lógica.

* `unsubscribe`ponto final: se o acionador de webhook ou uma aplicação lógica todo for eliminada, as Logic Apps motor chamadas a `unsubscribe` ponto final. A API, em seguida, pode anular o registo o URL de chamada de retorno e parar quaisquer processos conforme necessário.

![Padrão de Acionador de Webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Atualmente, o Designer de aplicação lógica não suporta a deteção de webhook de pontos finais através do Swagger. Para este padrão, terá de adicionar um [ **Webhook** acionador](../connectors/connectors-native-webhook.md) e especifique o URL, cabeçalhos e corpo do pedido. Consulte também [HTTPWebhook acionador](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Para passar o URL de chamada de retorno, pode utilizar o `@listCallbackUrl()` função de fluxo de trabalho em nenhum dos campos anteriores conforme necessário.
>
> Para impedir que os mesmos dados a processar várias vezes, o acionador deve limpar os dados que já foi lidos e transmitidos para a aplicação lógica.

> [!TIP]
> Para um padrão de webhook de exemplo, reveja este [exemplo de controlador de Acionador de webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Chamadas seguras para as suas APIs a partir das logic apps

Depois de criar as suas APIs personalizados, configure a autenticação para as suas APIs para que pode chamá-los em segurança a partir das logic apps. Saiba [como proteger chamadas para APIs personalizadas a partir das logic apps](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Implementar e chamar as suas APIs

Depois de configurar a autenticação, configurar a implementação para as suas APIs. Saiba [como implementar e chamar APIs personalizadas a partir das logic apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publicar APIs personalizadas no Azure

Para disponibilizar as suas APIs personalizados para outros utilizadores Logic Apps no Azure, tem de adicionar a segurança e registe-os como conectores de aplicação lógica. Para obter mais informações, consulte [descrição geral de conectores personalizada](../logic-apps/custom-connector-overview.md). 

Para disponibilizar as suas APIs personalizados para todos os utilizadores em Logic Apps, Microsoft Flow e Microsoft PowerApps, deve adicionar segurança, registar as suas APIs como conectores de aplicação lógica e para indicar os conectores para o [programa de certificados do Microsoft Azure](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Obter suporte

* Para obter ajuda específica com APIs personalizadas, contacte [ customapishelp@microsoft.com ](mailto:customapishelp@microsoft.com).

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar as Logic Apps, votar em ou submeter ideias no [site de comentários do utilizador Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passos seguintes

* [Lidar com erros e exceções](../logic-apps/logic-apps-exception-handling.md)
* [Chamar, acionador, ou aninhar aplicações lógicas com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Medição de utilização para ações e é acionado](../logic-apps/logic-apps-pricing.md)

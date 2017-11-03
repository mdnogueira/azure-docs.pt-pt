---
title: Implementar e chamar web APIs e REST APIs da Azure Logic Apps | Microsoft Docs
description: "Implementar e chamar web APIs e REST APIs para o sistema, fluxos de trabalho de integração no Azure Logic Apps"
keywords: "Web APIs, REST APIs, conectores, fluxos de trabalho, integrações de sistema, autenticar"
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: 3df59ec172b037daaeed9e3eb69ffb990d70d8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-call-custom-apis-from-logic-app-workflows"></a>Implementar e chamar APIs personalizadas a partir de lógica de fluxos de trabalho da aplicação

Depois de [criar APIs personalizadas](./logic-apps-create-api-app.md) para utilização em fluxos de trabalho de aplicação de lógica, tem de implementar as suas APIs antes de poder chamá-los. Pode implementar as suas APIs como [aplicações web](../app-service/app-service-web-overview.md), mas a considerar implementar as suas APIs como [das API apps](../app-service/app-service-web-tutorial-rest-api.md), que facilitar a sua tarefa quando criar, alojar e consumir APIs na nuvem e no local. Não tem de alterar os códigos na suas APIs – basta implementar o seu código numa aplicação API. Pode alojar as suas APIs em [App Service do Azure](../app-service/app-service-web-overview.md), um plataforma-como-um-serviço (PaaS) que fornece altamente dimensionável e fácil que aloja a API da oferta.

Apesar de poder chamar qualquer API a partir de uma aplicação lógica, para uma experiência otimizada, adicionar [OpenAPI (anteriormente Swagger) metadados](http://swagger.io/specification/) que descreve a API operações e parâmetros. Este ficheiro OpenAPI ajuda a sua API integrar mais facilmente e a funcionar melhor com logic apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Implementar a sua API como uma aplicação web ou aplicação API

Antes de poder chamar a API personalizada a partir de uma aplicação lógica, implemente a sua API como uma aplicação web ou aplicação API App Service do Azure. Além disso, para tornar o seu OpenAPI ficheiro ser lido pelo Designer de aplicações lógicas, definir as propriedades de definição de API e ative [recursos de várias origens (CORS) de partilha](../app-service/app-service-web-overview.md) para a sua aplicação web ou aplicação API.

1. No [portal do Azure](https://portal.azure.com), selecione a sua aplicação web ou aplicação API.

2. No menu aplicação que abre-se, em **API**, escolha **definição da API**. Definir o **localização de definição de API** para o URL para o ficheiro de swagger.json OpenAPI.

   Normalmente, o URL for apresentada neste formato:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Ligar ao ficheiro de OpenAPI para a API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Em **API**, escolha **CORS**. Definir a política CORS para **permitido origens** para  **'*'** (permitir todos os).

   Esta definição permite pedidos a partir do Designer de aplicação lógica.

   ![Permitir pedidos a partir do Designer de aplicação lógica à sua API personalizada](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Para obter mais informações, consulte [criar uma API RESTful Node.js](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Chamar a API personalizada da lógica de fluxos de trabalho da aplicação

Depois de configurar as propriedades de definição de API e a CORS, a API personalizada acionadores e ações devem estar disponíveis para si incluir no seu fluxo de trabalho de aplicação lógica. 

*  Para ver os Web sites que tenham OpenAPI URLs, pode procurar sites sua subscrição no Designer de aplicações lógicas.

*  Para ver as ações disponíveis e entradas por apontar um documento de OpenAPI, utilize o [HTTP + Swagger ação](../connectors/connectors-native-http-swagger.md).

*  Para chamar qualquer API, incluindo as APIs que não têm ou expor um documento de OpenAPI sempre pode criar um pedido com o [ação HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do conetor personalizado](../logic-apps/custom-connector-overview.md)
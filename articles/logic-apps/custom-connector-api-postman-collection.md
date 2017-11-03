---
title: Descrevem personalizado APIs e conectores com Postman - Azure Logic Apps | Microsoft Docs
description: "Criar coleções de Postman para descrever, grupo e organizar os APIs e conectores personalizado"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Descrevem APIs personalizadas e conectores personalizados com o Postman

Para tornar a desenvolver [APIs personalizadas](../logic-apps/logic-apps-create-api-app.md) e [conectores personalizados](../logic-apps/custom-connector-overview.md) mais rápida e mais fácil, pode criar [Postman](https://www.getpostman.com/) coleções, em vez de documentos OpenAPI, para descrever o APIs e conectores. Ajuda de coleções do postman organizar e grupo relacionadas com pedidos de API. Por exemplo, pode utilizar o Postman quando criar conectores para o Azure Logic Apps, Microsoft Flow ou Microsoft PowerApps. 

Este tutorial mostra como criar um [coleção Postman](https://www.getpostman.com/docs/postman/collections/creating_collections) utilizando o [detetar API de idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) no [análise de texto de serviços do Azure cognitivos](https://azure.microsoft.com/services/cognitive-services/text-analytics/) como exemplo. Esta API identifica o idioma, sentimento e expressões chaves no texto que passa para a API.

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver familiarizado com o Postman, [instalar a aplicação do Postman](https://www.getpostman.com/apps).

* Uma subscrição do Azure. Se não tiver uma subscrição, pode começar com uma [conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, inscreva-se um [subscrição pay as you go](https://azure.microsoft.com/pricing/purchase-options/).

* Se tiver uma subscrição do Azure, inscreva-se as APIs de análise de texto, concluindo [tarefa 1 aqui](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md). 

## <a name="create-a-postman-collection"></a>Criar uma coleção de Postman

Antes de poder criar uma coleção, crie um pedido HTTP para o ponto final de API. 

### <a name="create-an-http-request-for-your-api"></a>Criar um pedido de HTTP para a API

1. Abra a aplicação de Postman para que possa criar uma [pedido HTTP](https://www.getpostman.com/docs/postman/sending_api_requests/requests) para o ponto final de API. Para obter mais informações, consulte o Postman [pedidos documentação](https://www.getpostman.com/docs/postman/sending_api_requests/requests).

   1. No **Builder** separador, selecione o método HTTP, introduza o URL de pedido para o ponto final de API e selecione um protocolo de autorização, se aplicável. 
   Quando estiver pronto, selecione **Params**.

      Para este tutorial, pode utilizar estas definições neste exemplo:

      ![Criar pedido de: "HTTP method", "URL de pedido", "Autorização"](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | Parâmetro | Valor sugerido | 
      | --------- | --------------- | 
      | **Método HTTP** | POST | 
      | **URL do pedido** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **Autorização** | "Sem autenticação" | | 
      ||| 

   2. Agora pode introduzir pares chave-valor para utilizar como parâmetros de consulta ou o caminho no URL do pedido. Postman combina estes itens em conjunto numa cadeia de consulta.
   Quando tiver terminado, escolha **cabeçalhos**.

      ![Pedido de continuação: "Parâmetros de"](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | Parâmetro | Valor sugerido | 
      | --------- | --------------- | 
      | **Parâmetros** | **Chave**: "numberOfLanguagesToDetect" </br>**Valor**: "1" | 
      ||| 

   3. Introduza pares chave-valor para o cabeçalho do pedido. 
   Para o nome de cabeçalho, introduza qualquer cadeia que pretende. Para cabeçalhos HTTP comuns, pode selecionar na lista pendente. Quando tiver terminado, escolha **corpo**. 
   
      ![Pedido de continuação: "Cabeçalhos"](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | Parâmetro | Valor | 
      | --------- | ----- | 
      | **Cabeçalhos** | **Chave**: "Ocp-Apim-Subscription-Key" </br>**Valor**: *your-subscrição-chave de API*, que se encontram na sua conta de serviços cognitivos <p>**Chave**: "Content-Type" </br> **Valor**: "application/json" | 
      ||| 

   4. Introduza o conteúdo que pretende enviar no corpo do pedido. 
   Para verificar que o pedido funciona por obter uma resposta novamente, escolha **enviar**. 
   
      ![Pedido de continuação: "Corpo"](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | Parâmetro | Valor sugerido | 
      | --------- | --------------- |    
      | **Corpo** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      O campo de resposta contém a resposta completa da API, incluindo o resultado ou Ocorreu um erro, se aplicável.

      ![Obter a resposta do pedido](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. Depois de ter verificado que funciona a pedido, guardar o seu pedido para uma coleção de Postman. 

   1. Escolha **Guardar**. 
      
      ![Escolha "Guardar"](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. Em **Guardar pedido**, forneça um **nome do pedido** e, opcionalmente, um **pedido Descrição**. 

      > [!NOTE]
      > O conector personalizado utiliza estes valores mais tarde. Por isso, certifique-se de que fornecer os mesmos valores que utilizar mais tarde para a API personalizada operação resumo e descrição.

   3. Escolha **+ criar coleção** e forneça um nome da coleção. 
   Escolha a marca de verificação, que cria uma pasta de coleção, em seguida, escolha **guardar *Postman coleção-nome da***.

      ![Guardar o pedido](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>Guardar a resposta do pedido

Após ter de guardar o pedido, pode guardar a resposta. Dessa forma, a resposta aparece como um exemplo quando carregar o pedido mais tarde.

1. Acima a janela de resposta, escolha **Guardar resposta**. 

   ![Guardar resposta](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   Conectores personalizados suportam apenas uma resposta por pedido. 
   Se guardar várias respostas por pedido, apenas a primeira é utilizada.

2. Forneça um nome para o seu exemplo e escolha **guardar exemplo**.

3. Continue a criar a coleção de Postman com quaisquer pedidos adicionais e respostas.

### <a name="export-your-postman-collection"></a>Exportar a sua coleção Postman

1. Quando tiver terminado, exporte a coleção para um ficheiro JSON.

   ![Coleção de exportação](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. Escolha o **coleção v1** exportar formato e navegue para a localização onde pretende guardar o ficheiro JSON.

   > [!NOTE]
   > Atualmente, apenas V1 funciona para os conectores personalizados.

   ![Escolha o formato de exportação: "V1 de coleção"](./media/custom-connector-api-postman-collection/09-export-format.png)
   
Agora, pode utilizar esta coleção Postman para criação personalizada APIs e conectores. Depois de exportar a coleção, pode importar o ficheiro JSON para Logic Apps, fluxo ou PowerApps.

> [!IMPORTANT]
> Se criar um conetor personalizado a partir de uma coleção de Postman, certifique-se de que remove o `Content-type` cabeçalho de ações e é acionado. O serviço de destino, por exemplo, fluxo, adiciona automaticamente este cabeçalho. Além disso, remover cabeçalhos de autenticação no `securityDefintions` secção, ações e acionadores.

## <a name="next-steps"></a>Passos seguintes

* [As Logic Apps: Registar conectores personalizados](../logic-apps/logic-apps-custom-connector-register.md)
* [Fluxo: Registar o conector](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: Registar o conector](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [FAQ sobre o conetor personalizado](../logic-apps/custom-connector-faq.md)

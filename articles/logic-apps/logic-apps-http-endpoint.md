---
title: Chamar, acionador ou aninhar fluxos de trabalho com pontos finais HTTP - Azure Logic Apps | Microsoft Docs
description: Configurar pontos finais HTTP chamar, acionador, ou aninhar fluxos de trabalho para o Azure Logic Apps
services: logic-apps
keywords: fluxos de trabalho, pontos finais de HTTP
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: dab336da4e010d0a78de9a2bdd62536d8fdd9bf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Chamar, acionador, ou aninhar fluxos de trabalho com pontos finais HTTP nas logic apps

Nativamente pode expor os pontos finais de HTTP síncronos como os acionadores nas logic apps para que possa acionar ou chamar as logic apps através de um URL. Também pode aninhar fluxos de trabalho nas suas logic apps utilizando um padrão de possível chamar EndRead pontos finais.

Para criar pontos finais de HTTP, pode adicionar estes acionadores para que as logic apps podem receber pedidos de entrada:

* [Pedido](../connectors/connectors-native-reqres.md)

* [Webhook de ligação de API](logic-apps-workflow-actions-triggers.md#api-connection-trigger)

* [Webhook de HTTP](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Apesar de utilizam os nossos exemplos de **pedido** acionador, pode utilizar qualquer um dos acionadores HTTP listados e todos os princípios de forma idêntica aplicam para outros tipos de Acionador.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Configurar um ponto final HTTP para a sua aplicação lógica

Para criar um ponto final de HTTP, adicione um acionador que pode receber pedidos recebidos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com "portal do Azure"). Aceda à sua aplicação lógica e abra o Designer de aplicação lógica.

2. Adicione um acionador que permite que a sua aplicação lógica receber pedidos recebidos. Por exemplo, adicione o **pedido** acionador à sua aplicação lógica.

3.  Em **esquema de JSON de corpo do pedido**, opcionalmente, pode introduzir um esquema JSON para o payload (dados), que espera que o acionar a receber.

    O estruturador utiliza este esquema para gerar tokens que pode utilizar a aplicação lógica para consumir, analisar e transmitir dados de Acionador através do seu fluxo de trabalho. 
    Mais informações sobre como [tokens gerados a partir de esquemas JSON](#generated-tokens).

    Neste exemplo, introduza o esquema mostrado no designer de:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Adicionar a ação de pedido][1]

    > [!TIP]
    > 
    > Pode gerar um esquema para um payload JSON de exemplo a partir de uma ferramenta como o [jsonschema.net](http://jsonschema.net/), ou no **pedido** acionador escolhendo **payload de exemplo de utilização para gerar o esquema**. 
    > Introduza o payload de exemplo e escolha **feito**.

    Por exemplo, este payload de exemplo:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    gera neste esquema:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Guarde a sua aplicação lógica. Em **POST de HTTP para este URL**, agora deve encontrar um URL de chamada de retorno gerado, tal como neste exemplo:

    ![URL de chamada de retorno gerado para o ponto final](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Este URL contém uma chave de assinatura de acesso partilhado (SAS) os parâmetros de consulta que são utilizados para autenticação. 
    Também pode obter o URL de ponto final HTTP a partir da descrição geral da aplicação lógica no portal do Azure. Em **histórico de Acionador**, selecione o acionador:

    ![Obter o URL de ponto final HTTP a partir do portal do Azure][2]

    Ou pode obter o URL ao efetuar esta chamada:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Alterar o método HTTP para o acionador

Por predefinição, o **pedido** acionador espera um pedido POST de HTTP, mas pode utilizar um método HTTP diferente. 

> [!NOTE]
> Pode especificar o tipo de apenas um método.

1. No seu **pedido** acionar, escolha **Mostrar opções avançadas**.

2. Abra o **método** lista. Para este exemplo, selecione **obter** , para que possa testar URL do ponto final de HTTP mais tarde.

    > [!NOTE]
    > Pode selecionar qualquer outro método HTTP ou especificar um método personalizado para a sua própria aplicação lógica.

    ![Alterar o método HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Aceitar parâmetros através do seu URL de ponto final HTTP

Quando pretender que o seu URL de ponto final HTTP para aceitar parâmetros, personalize o caminho relativo do acionador.

1. No seu **pedido** acionar, escolha **Mostrar opções avançadas**. 

2. Em **método**, especifique o método HTTP que pretende que o seu pedido para utilizar. Para este exemplo, selecione o **obter** método, se ainda não o fez, para que possa testar URL do ponto final de HTTP.

      > [!NOTE]
      > Quando especificar um caminho relativo para o acionador, tem de especificar também explicitamente um método HTTP para o acionador.

3. Em **caminho relativo**, especifique o caminho relativo para o parâmetro que o URL deve aceitar, por exemplo, `customers/{customerID}`.

    ![Especifique o método HTTP e o caminho relativo para o parâmetro](./media/logic-apps-http-endpoint/relativeurl.png)

4. Para utilizar o parâmetro, adicione um **resposta** ação à sua aplicação lógica. (Sob o acionador, escolha **novo passo** > **adicionar uma ação** > **resposta**) 

5. Na sua resposta **corpo**, incluem o token para o parâmetro especificado no caminho relativo do acionador.

    Por exemplo, para devolver `Hello {customerID}`, atualize a sua resposta **corpo** com `Hello {customerID token}`. 
    A lista de conteúdo dinâmica deve aparecer e mostrar a `customerID` token para o selecionar.

    ![Adicionar o parâmetro de corpo de resposta](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    O **corpo** deverá ser semelhante neste exemplo:

    ![Corpo de resposta com o parâmetro](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Guarde a sua aplicação lógica. 

    O URL de ponto final HTTP inclui agora o caminho relativo, por exemplo: 

    https & # 58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Para testar o ponto final de HTTP, copie e cole o URL atualizado outra janela do browser, mas substitua `{customerID}` com `123456`, e prima Enter.

    O browser deve mostrar este texto: 

    `Hello 123456`

<a name="generated-tokens"></a>
### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Tokens gerados a partir de esquemas JSON para a sua aplicação lógica

Quando fornecer o esquema JSON na sua **pedido** acionador, o Designer de aplicação lógica gera tokens para propriedades que esquema. Em seguida, pode utilizar esses tokens para transmitir dados através do seu fluxo de trabalho de aplicação lógica.

Neste exemplo, se adicionar o `title` e `name` propriedades para o esquema JSON, os seus tokens estão agora disponíveis para utilização em passos posteriores do fluxo de trabalho. 

Segue-se o esquema JSON concluído:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Criar fluxos de trabalho aninhados para aplicações lógicas

Pode ser aninhado fluxos de trabalho na sua aplicação lógica adicionando outras as logic apps que podem receber pedidos. Para incluir estes logic apps, adicione o **Azure Logic Apps - escolha um fluxo de trabalho Logic Apps** ação a acionar. Em seguida, pode selecionar a partir de aplicações lógicas elegível.

![Adicionar outra aplicação lógica](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Chamar ou acionar as logic apps através de pontos finais HTTP

Depois de criar o ponto final de HTTP, pode acionar a sua aplicação lógica através de um `POST` método para o URL completo. As Logic apps têm suporte incorporado para pontos finais de acesso direto.

## <a name="reference-content-from-an-incoming-request"></a>Conteúdo de referência de um pedido recebido

Se o tipo de conteúdo é `application/json`, pode referenciar propriedades do pedido a receber. Caso contrário, o conteúdo é tratado como uma única unidade binária, que pode passar para outras APIs. Para fazer referência a este conteúdo dentro do fluxo de trabalho, tem de converter esse conteúdo. Por exemplo, se passa `application/xml` conteúdo, pode utilizar `@xpath()` para extração um XPath, ou `@json()` para a conversão XML para JSON. Saiba mais sobre [trabalhar com os tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

Para obter o resultado de um pedido recebido, pode utilizar o `@triggerOutputs()` função. A saída aspeto que poderá ter neste exemplo:

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

Para o acesso a `body` propriedade especificamente, pode utilizar o `@triggerBody()` atalho. 

## <a name="respond-to-requests"></a>Responder a pedidos

Pode querer responder a pedidos de determinados que iniciar uma aplicação lógica ao devolver o conteúdo para o autor da chamada. Para construir o código de estado, o cabeçalho e o corpo da sua resposta, pode utilizar o **resposta** ação. Esta ação pode aparecer em qualquer local na sua aplicação lógica, não apenas no final do fluxo de trabalho.

> [!NOTE] 
> Se a sua aplicação lógica não inclui um **resposta**, o ponto final de HTTP responde *imediatamente* com um **aceites 202** estado. Além disso, para o pedido original ao obter a resposta, todos os passos necessários para a resposta devem ser concluído dentro do [limite de tempo limite do pedido](./logic-apps-limits-and-config.md) , exceto se chamar o fluxo de trabalho como uma aplicação lógica aninhada. Se nenhuma resposta acontece este limite, o pedido de entrada exceder o tempo limite e recebe a resposta HTTP **tempo limite de cliente 408**. Para aplicações lógicas aninhados, a aplicação de lógica principal continua a aguardar pela resposta até à conclusão, independentemente da quanto tempo é necessário.

### <a name="construct-the-response"></a>Construir a resposta

Pode incluir mais do que um cabeçalho e qualquer tipo de conteúdo no corpo da resposta. No nosso exemplo de resposta, o cabeçalho Especifica que a resposta tem o tipo de conteúdo `application/json`. e o corpo contém `title` e `name`, consoante o esquema JSON anteriormente atualizado para incluir o **pedido** acionador.

![Ação de resposta de HTTP][3]

As respostas têm estas propriedades:

| Propriedade | Descrição |
| --- | --- |
| statusCode |Especifica o código de estado HTTP para responder ao pedido de entrada. Este código pode ser qualquer código de estado válido que comece com 2xx, 4xx ou 5xx. No entanto, os códigos de estado de 3xx não são permitidos. |
| Cabeçalhos |Define qualquer número de cabeçalhos para incluir na resposta. |
| Corpo |Especifica um objeto de corpo pode ser uma cadeia, um objeto JSON ou mesmo binário conteúdo referenciado a partir de um passo anterior. |

Eis o que o esquema JSON assemelha agora o **resposta** ação:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Para ver a definição JSON concluída para a sua aplicação lógica, no Designer de aplicação lógica, escolha **Code vista**.

## <a name="q--a"></a>P&R

#### <a name="q-what-about-url-security"></a>P: E sobre segurança URL?

R: azure gera em segurança URLs da chamada de retorno de aplicação de lógica com uma assinatura de acesso partilhado (SAS). Esta assinatura atravessa como um parâmetro de consulta e tem de ser validada antes da aplicação lógica pode acionados. O Azure gera a assinatura utilizando uma combinação exclusiva de uma chave secreta por aplicação lógica, o nome do acionador e a operação que é executada. Por isso, exceto se alguém tiver acesso à chave de aplicação lógica segredo, não é possível gerar uma assinatura válida.

   > [!IMPORTANT]
   > Para produção e sistemas seguros, Desaconselhamos vivamente a sua aplicação lógica ao chamar diretamente a partir do browser porque:
   > 
   > * A chave de acesso partilhado é apresentado no URL.
   > * Não é possível gerir políticas de conteúdo seguras devido a domínios partilhados entre os clientes de aplicação lógica.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: posso configurar pontos finais de HTTP mais?

R: Sim, pontos finais de HTTP suportam a configuração mais avançada através de [ **API Management**](../api-management/api-management-key-concepts.md). Este serviço também oferece a capacidade para gerir todas as suas APIs, incluindo as logic apps consistentemente, configurar os nomes de domínio personalizado, utilize mais métodos de autenticação e muito mais, por exemplo:

* [O método de pedido de alteração](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Alterar os segmentos de URL do pedido](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Configurar os domínios de gestão de API no [portal do Azure](https://portal.azure.com/ "portal do Azure")
* Configurar a política para verificar se a autenticação básica

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>P: quais alterado quando o esquema migrado a partir da pré-visualização 1 de Dezembro de 2014

R: Eis um resumo sobre estas alterações:

| Pré-visualização de 1 de Dezembro de 2014 | 1 de Junho de 2016 |
| --- | --- |
| Clique em **serviço de escuta HTTP** aplicação API |Clique em **acionador Manual** (não existe nenhuma aplicação de API obrigatório) |
| Definição de serviço de escuta de HTTP "*envia automaticamente resposta*" |Quer incluir um **resposta** ação ou não a definição de fluxo de trabalho |
| Configurar a autenticação básica ou OAuth |através da gestão de API |
| Configurar o método de HTTP |Em **Mostrar opções avançadas**, escolha um método HTTP |
| Configurar o caminho relativo |Em **Mostrar opções avançadas**, adicione um caminho relativo |
| O corpo de entrada através de referência`@triggerOutputs().body.Content` |Referência através da`@triggerOutputs().body` |
| **Enviar uma resposta HTTP** ação sobre o serviço de escuta de HTTP |Clique em **responder a pedidos de HTTP** (não existe nenhuma aplicação de API obrigatório) |

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e ver o que os outros utilizadores do Azure Logic Apps estão a fazer, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar o Azure Logic Apps e os conectores, vote ou submeta ideais no [site de comentários dos utilizadores do Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Criar definições de aplicação lógica](./logic-apps-author-definitions.md)
* [Lidar com erros e exceções](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png

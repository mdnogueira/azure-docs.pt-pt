---
title: "Comunicar com qualquer ponto final através de HTTP - Azure Logic Apps | Microsoft Docs"
description: "Criar as logic apps que podem comunicar com qualquer ponto final através de HTTP"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: d422a07a27ffa62a673bd2d471ae4fc837251dee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-http-action"></a>Começar com a ação de HTTP

Com a ação de HTTP, pode expandir fluxos de trabalho para a sua organização e comunicar com qualquer ponto final através de HTTP.

Pode:

* Crie lógica fluxos de trabalho da aplicação que ativar (acionador) quando um site que gere fica inativo.
* Comunicar com qualquer ponto final através de HTTP para expandir os seus fluxos de trabalho noutros serviços.

Para começar a utilizar a ação de HTTP numa aplicação lógica, consulte o artigo [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>Utilize o acionador HTTP
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido uma aplicação lógica. [Saiba mais sobre acionadores](connectors-overview.md).

Eis um exemplo de sequência de como configurar o acionador HTTP no Designer de aplicação lógica.

1. Adicione o acionador HTTP na sua aplicação lógica.
2. Preencha os parâmetros para o ponto final de HTTP que pretende consultar.
3. Modificar o intervalo de periodicidade na frequência deve consultar.

   A aplicação lógica agora desencadeado com qualquer conteúdo que é devolvido durante a verificação de cada.

   ![Acionador HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Como funciona o acionador HTTP

O acionador HTTP envia uma chamada para o ponto final de HTTP num intervalo periódico. Por predefinição, os códigos de resposta HTTP são inferior a 300 faz com que uma aplicação lógica ser executada. Para especificar se a aplicação lógica deve acionados, pode editar a aplicação lógica na vista de código e adicionar uma condição que avalia após a chamada HTTP. Eis um exemplo de um acionador HTTP que é acionado quando o código de estado devolvido é maior que ou igual a `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Detalhes completos sobre os parâmetros de Acionador HTTP estão disponíveis no [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Utilize a ação de HTTP

Uma ação é uma operação que é efetuada pelo fluxo de trabalho que está definido uma aplicação lógica. 
[Saiba mais sobre as ações](connectors-overview.md).

1. Escolha **novo passo** > **adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **http** para listar as ações de HTTP.
   
    ![Selecione a ação de HTTP](./media/connectors-native-http/using-action-1.png)

4. Adicione os parâmetros necessários para a chamada HTTP.
   
    ![Concluir a ação de HTTP](./media/connectors-native-http/using-action-2.png)

5. Na barra de ferramentas designer, clique em **guardar**. A aplicação lógica é guardada e publicada (ativada) ao mesmo tempo.

## <a name="http-trigger"></a>Acionador HTTP
Seguem-se os detalhes para o acionador que suporte este conector. O conector HTTP tem um acionador.

| Acionador | Descrição |
| --- | --- |
| HTTP |Faz uma chamada HTTP e devolve o conteúdo da resposta. |

## <a name="http-action"></a>Ação de HTTP
Seguem-se os detalhes para a ação que este conector suporta. O conector HTTP tem uma ação possíveis.

| Ação | Descrição |
| --- | --- |
| HTTP |Faz uma chamada HTTP e devolve o conteúdo da resposta. |

## <a name="http-details"></a>Detalhes HTTP
As tabelas seguintes descrevem os campos de entrada necessários e opcionais para a ação e os detalhes de resultado correspondente que estão associados utilizando a ação.

#### <a name="http-request"></a>Pedido de HTTP
Seguem-se os campos de entrada para a ação, o que faz um pedido de saída de HTTP.
A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Método * |Método |O verbo HTTP a utilizar |
| URI * |URI |O URI do pedido de HTTP |
| Cabeçalhos |Cabeçalhos |Um objeto JSON de cabeçalhos de HTTP para incluir |
| Corpo |Corpo |O corpo do pedido HTTP |
| Autenticação |Autenticação |Os detalhes no [autenticação](#authentication) secção |

<br>

#### <a name="output-details"></a>Detalhes de saída
Seguem-se detalhes de saída para a resposta HTTP.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Cabeçalhos |objeto |Cabeçalhos de resposta |
| Corpo |objeto |Objeto de resposta |
| Código de estado |Int |Código de estado HTTP |

## <a name="authentication"></a>Autenticação
A funcionalidade de Logic Apps permite-lhe utilizar diferentes tipos de autenticação nos pontos finais HTTP. Pode utilizar esta autenticação com o **HTTP**,  **[HTTP + Swagger](connectors-native-http-swagger.md)**, e  **[HTTP Webhook](connectors-native-webhook.md)**  conectores. Os seguintes tipos de autenticação são configuráveis:

* [Autenticação básica](#basic-authentication)
* [Autenticação de certificado de cliente](#client-certificate-authentication)
* [Autenticação do Azure Active Directory (Azure AD) OAuth](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Autenticação básica

É necessário o seguinte objeto de autenticação para a autenticação básica.
A * significa que é um campo obrigatório.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Tipo * |tipo |Tipo de autenticação (tem de ser `Basic` para a autenticação básica) |
| Nome de utilizador * |o nome de utilizador |Nome de utilizador a autenticar |
| Palavra-passe * |palavra-passe |Palavra-passe para autenticar |

> [!TIP]
> Se pretender utilizar uma palavra-passe não pode ser obtida a partir da definição, utilize um `securestring` parâmetro e o `@parameters()`  
>  [função da definição de fluxo de trabalho](http://aka.ms/logicappdocs).

Por exemplo:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Autenticação de certificados de cliente

É necessário o seguinte objeto de autenticação para autenticação de certificados de cliente. A * significa que é um campo obrigatório.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Tipo * |tipo |O tipo de autenticação (tem de ser `ClientCertificate` para certificados de cliente SSL) |
| PFX * |PFX |O conteúdo com codificação Base64 do ficheiro Personal Information (Exchange PFX) |
| Palavra-passe * |palavra-passe |A palavra-passe para aceder ao ficheiro PFX |

> [!TIP]
> Para utilizar um parâmetro que não seja legível na definição do depois de guardar a aplicação lógica, pode utilizar um `securestring` parâmetro e o `@parameters()`  
>  [função da definição de fluxo de trabalho](http://aka.ms/logicappdocs).

Por exemplo:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Autenticação do Azure AD OAuth
É necessário o seguinte objeto de autenticação para a autenticação do Azure AD OAuth. A * significa que é um campo obrigatório.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Tipo * |tipo |O tipo de autenticação (tem de ser `ActiveDirectoryOAuth` para o Azure AD OAuth) |
| Inquilino * |Inquilino |O identificador de inquilino para o inquilino do Azure AD |
| Público-alvo * |público-alvo |O recurso que está a pedir autorização para utilizar. Por exemplo: `https://management.core.windows.net/` |
| Cliente ID * |ID de cliente |O identificador de cliente para a aplicação do Azure AD |
| Segredo * |segredo |O segredo do cliente que está a solicitar o token |

> [!TIP]
> Pode utilizar um `securestring` parâmetro e o `@parameters()` [função da definição de fluxo de trabalho](http://aka.ms/logicappdocs) para utilizar um parâmetro que não seja legível na definição do depois de guardar.
> 
> 

Por exemplo:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Passos seguintes
Agora, experimente a plataforma e [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Pode explorar os outros conectores disponíveis em Logic Apps observando nosso [lista APIs](apis-list.md).


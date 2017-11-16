---
title: "Trabalhar com os proxies de funções do Azure | Microsoft Docs"
description: "Descrição geral de como utilizar os Proxies de funções do Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/11/2017
ms.author: alkarche
ms.openlocfilehash: 24bc439b6167d335a0862aa93debb9efe5aeae48
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="work-with-azure-functions-proxies"></a>Trabalhar com os Proxies de funções do Azure

Este artigo explica como configurar e trabalhar com os Proxies de funções do Azure. Com esta funcionalidade, pode especificar pontos finais na sua aplicação de função que são implementados por outro recurso. Pode utilizar estes proxies para quebrar uma API de grandes dimensões em várias aplicações de função (como uma arquitetura de microsserviço), enquanto ainda apresentar uma único superfície de API para os clientes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Funções padrão faturação aplica-se para execuções de proxy. Para obter mais informações, consulte [preços de funções do Azure](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Criar um proxy

Esta secção mostra como criar um proxy no portal de funções.

1. Abra o [portal do Azure]e, em seguida, aceda a sua aplicação de função.
2. No painel esquerdo, selecione **novo proxy**.
3. Forneça um nome para o proxy.
4. Configurar o ponto final que é exposto nesta aplicação de função especificando o **modelo de rota** e **métodos de HTTP**. Estes parâmetros se comportam de acordo com as regras para [HTTP acionadores].
5. Definir o **URL back-end** para outro ponto final. Este ponto final pode ser uma função na outra aplicação de função, ou pode ser qualquer outra API. O valor não tem de ser estáticos e podem fazer referência a [definições da aplicação] e [parâmetros do pedido de cliente original].
6. Clique em **Criar**.

O proxy agora existe como um novo ponto final na sua aplicação de função. A partir de uma perspetiva do cliente, é equivalente a um HttpTrigger nas funções do Azure. Pode experimentar o seu novo proxy ao copiar o URL de Proxy e testá-lo com o cliente HTTP favorito.

## <a name="modify-requests-responses"></a>Modificar pedidos e respostas

Com os Proxies de funções do Azure, pode modificar pedidos e respostas de back-end. Estes transformações podem utilizar variáveis, tal como definido no [utilizar variáveis].

### <a name="modify-backend-request"></a>Modificar o pedido de back-end

Por predefinição, o pedido de back-end é inicializado como uma cópia do pedido original. Além de definir o URL de back-end, pode efetuar alterações para o método HTTP, cabeçalhos e os parâmetros de cadeia de consulta. Os valores modificados podem referenciar [definições da aplicação] e [parâmetros do pedido de cliente original].

Atualmente, não há nenhum experiência do portal para modificar pedidos de back-end. Para saber como aplicar esta capacidade de proxies.json, consulte [definir um objeto de requestOverrides].

### <a name="modify-response"></a>Modificar a resposta

Por predefinição, a resposta do cliente é inicializada como uma cópia da resposta de back-end. Pode efetuar alterações ao código de estado de resposta, frase de razão, cabeçalhos e corpo. Os valores modificados podem referenciar [definições da aplicação], [parâmetros do pedido de cliente original], e [parâmetros da resposta de back-end].

Atualmente, não há nenhum experiência do portal para modificar as respostas. Para saber como aplicar esta capacidade de proxies.json, consulte [definir um objeto de responseOverrides].

## <a name="using-variables"></a>Utilizar variáveis

A configuração para um proxy não precisa de ser estático. Pode condição-a para utilizar variáveis do pedido original, a resposta de back-end ou as definições da aplicação.

### <a name="request-parameters"></a>Parâmetros do pedido de referência

Pode utilizar os parâmetros do pedido como entradas para a propriedade de URL de back-end ou como parte de modificar os pedidos e respostas. Podem estar vinculados alguns parâmetros do modelo de rota que é especificado na configuração do proxy de base e outros utilizadores podem ter a partir das propriedades do pedido a receber.

#### <a name="route-template-parameters"></a>Parâmetros do modelo de rota
Os parâmetros que são utilizados no modelo de rota se disponível para ser referenciado por nome. Os nomes dos parâmetros estão incluídos no chavetas ({}).

Por exemplo, se um proxy, tem um modelo de rota, tal como `/pets/{petId}`, o URL de back-end pode incluir o valor de `{petId}`, como no `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se o modelo de rota termina num caráter universal, tais como `/api/{*restOfPath}`, o valor `{restOfPath}` é uma representação de cadeia dos restantes segmentos de caminho do pedido a receber.

#### <a name="additional-request-parameters"></a>Parâmetros do pedido adicionais
Para além dos parâmetros do modelo de rota, os seguintes valores podem ser utilizados valores de configuração:

* **{request.method}** : Método de HTTP o que é utilizado no pedido original.
* **{request.headers. \<HeaderName\>}**: um cabeçalho que pode ser lidos no pedido original. Substitua  *\<HeaderName\>*  com o nome de cabeçalho que pretende ler. Se o cabeçalho não está incluído no pedido, o valor será a cadeia vazia.
* **{request.querystring. \<ParameterName\>}**: um parâmetro de cadeia de consulta que pode ser lidos no pedido original. Substitua  *\<ParameterName\>*  com o nome do parâmetro que pretende ler. Se o parâmetro não está incluído no pedido, o valor será a cadeia vazia.

### <a name="response-parameters"></a>Parâmetros de resposta de back-end de referência

Parâmetros de resposta podem ser utilizados como parte de modificar a resposta ao cliente. Os seguintes valores podem ser utilizados valores de configuração:

* **{backend.response.statusCode}** : Código de estado de HTTP o que é devolvido na resposta back-end.
* **{backend.response.statusReason}** : Frase de razão de HTTP o que é devolvido na resposta back-end.
* **{backend.response.headers. \<HeaderName\>}**: um cabeçalho que pode ser lidos na resposta de back-end. Substitua  *\<HeaderName\>*  com o nome de cabeçalho que pretende ler. Se o cabeçalho não está incluído no pedido, o valor será a cadeia vazia.

### <a name="use-appsettings"></a>Definições da aplicação de referência

Também pode referenciar [definições da aplicação definidas para a aplicação de função](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) por envolvente o nome de definição de percentagem sinais (%).

Por exemplo, um URL de back-end de *https://%ORDER_PROCESSING_HOST%/api/orders* teria aos "% ORDER_PROCESSING_HOST %" substituída pelo valor da definição de ORDER_PROCESSING_HOST.

> [!TIP] 
> Utilize as definições da aplicação para os anfitriões de back-end, se tiver múltiplas implementações ou ambientes de teste. Dessa forma, pode certificar-se que estamos a falar sempre para o direito back-end nesse ambiente.

## <a name="advanced-configuration"></a>Configuração avançada

Proxies que configurar são armazenadas num ficheiro proxies.json, que está localizado na raiz de um diretório de aplicação de função. Pode editar este ficheiro e implementá-lo como parte da sua aplicação quando utilizar qualquer um dos manualmente o [métodos de implementação](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) que suporta as funções. A funcionalidade tem de ser [ativada](#enable) para o ficheiro a ser processado. 

> [!TIP] 
> Se não configurou um dos métodos de implementação, pode também funcionar com o ficheiro de proxies.json no portal. Aceda à sua aplicação de função, selecione **funcionalidades da plataforma**e, em seguida, selecione **Editor do serviço de aplicações**. Ao fazê-lo, pode ver a estrutura de ficheiro completo da sua aplicação de função e, em seguida, efetuar alterações.

Proxies.JSON é definido por um objeto de proxies, que é composto por proxies com nome e as respetivas definições. Opcionalmente, se o seu editor de o suportar, pode referenciar um [esquema JSON](http://json.schemastore.org/proxies) para conclusão de código. Um exemplo de ficheiro pode ter o seguinte aspeto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Cada proxy tem um nome amigável, tais como *proxy1* no exemplo anterior. O objeto de definição de proxy correspondente é definido pelas seguintes propriedades:

* **matchCondition**: necessário – um objeto que define os pedidos de acionam a execução deste proxy. Contém duas propriedades que são partilhadas com [HTTP acionadores]:
    * _métodos_: uma matriz dos métodos de HTTP que o proxy responde a. Se não for especificado, o proxy de responde a todos os métodos HTTP na rota.
    * _rota_: necessário – define o modelo de rota, controlar qual pedido URLs o proxy responde a. Ao contrário no acionadores HTTP, não há nenhum valor predefinido.
* **backendUri**: O URL do recurso de back-end para o qual o pedido deve ser efetuado. Este valor pode referenciar as definições da aplicação e os parâmetros do pedido de cliente original. Se esta propriedade não for incluída, as funções do Azure responde com uma HTTP 200 OK.
* **requestOverrides**: um objeto que defina transformações ao pedido de back-end. Consulte [definir um objeto de requestOverrides].
* **responseOverrides**: um objeto que defina transformações para a resposta do cliente. Consulte [definir um objeto de responseOverrides].

> [!NOTE] 
> A propriedade de rota Proxies de funções do Azure não honrar a propriedade de routePrefix da configuração de anfitrião de funções. Se pretender incluir um prefixo como /api, tem de ser incluído na propriedade rota.

### <a name="requestOverrides"></a>Definir um objeto de requestOverrides

O objeto de requestOverrides define as alterações efetuadas ao pedido quando o recurso de back-end é chamado. O objeto é definido pelas seguintes propriedades:

* **backend.Request.Method**: método de HTTP o que é utilizado para chamar o back-end.
* **backend.Request.QueryString. \<ParameterName\>**: um parâmetro de cadeia de consulta que pode ser definido para a chamada para o back-end. Substitua  *\<ParameterName\>*  com o nome do parâmetro que pretende configurar. Se a cadeia vazia, o parâmetro não está incluído no pedido de back-end.
* **backend.Request.Headers. \<HeaderName\>**: um cabeçalho que pode ser definido para a chamada para o back-end. Substitua  *\<HeaderName\>*  com o nome de cabeçalho que pretende configurar. Se fornecer a cadeia vazia, o cabeçalho não está incluído no pedido de back-end.

Valores podem referenciar as definições da aplicação e os parâmetros do pedido de cliente original.

Um exemplo de configuração pode ter o seguinte aspeto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>Definir um objeto de responseOverrides

O objeto de requestOverrides define as alterações efetuadas à resposta que é transmitida para o cliente. O objeto é definido pelas seguintes propriedades:

* **response.statusCode**: código de estado de HTTP a deve ser devolvida ao cliente.
* **response.statusReason**: frase de razão do HTTP a devolver ao cliente.
* **Response.body**: A representação de cadeia do corpo deve ser devolvida ao cliente.
* **Response.Headers. \<HeaderName\>**: um cabeçalho que pode ser definido para a resposta ao cliente. Substitua  *\<HeaderName\>*  com o nome de cabeçalho que pretende configurar. Se fornecer a cadeia vazia, o cabeçalho não está incluído na resposta.

Valores podem referenciar as definições da aplicação, parâmetros do pedido de cliente original e os parâmetros da resposta de back-end.

Um exemplo de configuração pode ter o seguinte aspeto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> Neste exemplo, o corpo estiver a ser definido diretamente, por isso, não `backendUri` propriedade é necessária. O exemplo mostra como pode utilizar os Proxies de funções do Azure para mocking APIs.

## <a name="enable"></a>Ativar as funções do Azure Proxies

Proxies agora estão ativadas por predefinição! Se estava a utilizar uma versão antiga da pré-visualização de proxies e desativada proxies, terá de ativar manualmente proxies uma vez por ordem para proxies executar.

1. Abra o [portal do Azure]e, em seguida, aceda a sua aplicação de função.
2. Selecione **as definições de aplicação de função**.
3. Comutador **ativar Proxies de funções do Azure (pré-visualização)** para **no**.

Também pode regressar aqui para atualizar o tempo de execução do proxy à novas funcionalidades ficam disponíveis.

[portal do Azure]: https://portal.azure.com
[HTTP acionadores]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[definir um objeto de requestOverrides]: #requestOverrides
[definir um objeto de responseOverrides]: #responseOverrides
[definições da aplicação]: #use-appsettings
[utilizar variáveis]: #using-variables
[parâmetros do pedido de cliente original]: #request-parameters
[parâmetros da resposta de back-end]: #response-parameters

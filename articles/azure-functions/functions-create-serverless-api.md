---
title: "Criar uma API sem servidor através das funções do Azure | Microsoft Docs"
description: "Como criar uma API sem servidor através das funções do Azure"
services: functions
author: mattchenderson
manager: cfowler
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7c3933210c01c81077b594abb8c3183d6e3c58a0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Criar uma API sem servidor através das funções do Azure

Neste tutorial, ficará a saber como as funções do Azure permite-lhe criar APIs altamente dimensionáveis. As funções do Azure vem com uma coleção de incorporada HTTP acionadores e enlaces, o que tornam mais fácil criar um ponto final numa variedade de idiomas, incluindo o Node.JS, c# e muito mais. Neste tutorial, irá personalizar um acionador HTTP para processar ações específicas na sua conceção de API. Também irá preparar para a crescer a sua API, como o integrar com Proxies de funções do Azure e como configurar mock APIs. Tudo isto é conseguido sobre o ambiente de computação sem servidor de funções, pelo que não precisa de preocupar com dimensionamento recursos - apenas poder concentrar na sua lógica de API.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

A função resultante será utilizada para o resto deste tutorial.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o portal do Azure. Para tal, inicie sessão na [https://portal.azure.com](https://portal.azure.com) com a sua conta do Azure.

## <a name="customize-your-http-function"></a>Personalizar a sua função HTTP

Por predefinição, a sua função de acionada por HTTP está configurada para aceitar qualquer método HTTP. Também é um URL predefinido do formulário `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Se seguiu o guia de introdução, em seguida, `<funcname>` provavelmente aspeto semelhante ao seguinte "HttpTriggerJS1". Nesta secção, irá modificar a função responda apenas a pedidos GET contra `/api/hello` encaminhar em vez disso. 

1. Navegue para a função no portal do Azure. Selecione **integrar** no painel de navegação esquerdo.

    ![Personalizar uma função HTTP](./media/functions-create-serverless-api/customizing-http.png)

1. Utilize as definições de Acionador HTTP especificado na tabela.

    | Campo | Valor da amostra | Descrição |
    |---|---|---|
    | Métodos de HTTP permitidos | Métodos selecionados | Determina os métodos HTTP podem ser utilizados para invocar esta função |
    | Métodos de HTTP selecionados | INTRODUÇÃO | Permite que apenas selecionados métodos de HTTP ser utilizado para invocar esta função |
    | Modelo de rota | /Hello | Determina que rota é utilizada para invocar esta função |
    | Nível de autorização | Anónima | Opcional: Faz com que a função acessível sem uma chave de API |

    > [!NOTE] 
    > Tenha em atenção que não incluía o `/api` base prefixo de caminho no modelo de rota, como isto é processado por uma definição global.

1. Clique em **Guardar**.

Pode saber mais sobre como personalizar funções HTTP no [enlaces HTTP de funções do Azure e webhook](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>A API de teste

Em seguida, teste a sua função para vê-lo a trabalhar com a superfície de API de novo.
1. Navegue de volta para a página de desenvolvimento ao clicar no nome da função na navegação à esquerda.
1. Clique em **obter URL de função** e copie o URL. Deverá ver que utiliza o `/api/hello` encaminhar agora.
1. Copie o URL para um novo separador do browser ou o cliente REST preferencial. Browsers utilizará GET por predefinição.
1. Adicionar parâmetros para a cadeia de consulta no seu URL, por exemplo`/api/hello/?name=John`
1. Atingiu o introduza para confirmar que está a funcionar. Deverá ver a resposta "*Hello João*"
1. Também pode tentar chamar o ponto final com outro método HTTP para confirmar que a função não está a ser executada. Para tal, terá de utilizar um cliente REST, tais como cURL, Postman ou Fiddler.

## <a name="proxies-overview"></a>Descrição geral de proxies

Na secção seguinte, irá superfície da API através de um proxy. Proxies de funções do Azure permite-lhe reencaminhar pedidos para outros recursos. Definir um ponto final HTTP, tal como com o acionador de HTTP, mas em vez de escrever código para executar quando esse ponto final é chamado, forneça um URL para a implementação remota. Isto permite-lhe compor várias origens de API para uma único superfície de API que é mais fácil para os clientes consumir. Isto é particularmente útil se pretender criar a sua API como micro-serviços.

Um ponto proxy de pode a qualquer recurso HTTP, tal como:
- Funções do Azure 
- API apps no [App Service do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- Os contentores de docker no [do serviço de aplicações no Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Quaisquer outro API alojada

Para saber mais sobre proxies, consulte o artigo [trabalhar com os Proxies de funções do Azure].

## <a name="create-your-first-proxy"></a>Criar o primeiro proxy

Nesta secção, irá criar um novo proxy que funciona como front-end à sua API geral. 

### <a name="setting-up-the-frontend-environment"></a>Configurar o ambiente de front-end

Repita os passos para [criar uma aplicação de função](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) para criar uma nova aplicação de função que vai criar o proxy. URL da aplicação nova irá servir como o front-end para a nossa API e a aplicação de função que foram anteriormente editar irá servir como um back-end.

1. Navegue para a nova aplicação de função de front-end no portal.
1. Selecione **funcionalidades da plataforma** e escolha **definições da aplicação**.
1. Desloque para baixo até **definições da aplicação** onde os pares chave/valor são armazenados e crie uma nova definição com a chave "HELLO_HOST". Defina o respetivo valor para o anfitrião da sua aplicação de função de back-end, tais como `<YourBackendApp>.azurewebsites.net`. Esta é a parte do URL que copiou anteriormente ao testar a sua função HTTP. Irá referenciar esta definição na configuração mais tarde.

    > [!NOTE] 
    > As definições de aplicação são recomendadas para a configuração do anfitrião evitar uma dependência de ambiente hard-coded para o proxy. Utilizar as definições de aplicação significa que pode mover a configuração de proxy entre ambientes e serão aplicadas as definições de aplicação específico do ambiente.

1. Clique em **Guardar**.

### <a name="creating-a-proxy-on-the-frontend"></a>Criar um proxy no front-end

1. Navegue de volta para a sua aplicação de função de front-end no portal.
1. No painel de navegação esquerdo, clique no sinal '+' junto a "Proxies".
    ![Criar um proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Utilize definições de proxy especificado na tabela. 

    | Campo | Valor da amostra | Descrição |
    |---|---|---|
    | Nome | HelloProxy | Um nome amigável utilizado apenas para gestão |
    | Modelo de rota | Olá/api / | Determina a rota de que é utilizada para invocar esta proxy |
    | URL de back-end | https://%HELLO_HOST%/API/Hello | Especifica o ponto final ao qual o pedido deve ser efetuado |
    
1. Tenha em atenção que Proxies não fornece o `/api` prefixo do caminho de base e este deve ser incluído no modelo de rota.
1. O `%HELLO_HOST%` sintaxe fará referência a definição de aplicação que criou anteriormente. O URL resolvido irá apontar para a sua função original.
1. Clique em **Criar**.
1. Pode experimentar o seu novo proxy ao copiar o URL de Proxy e testá-lo no browser ou com o cliente HTTP favorito.
    1. Para uma utilização de função anónimo:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. Para uma função com utilização de autorização:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Criar uma API mock

Em seguida, irá utilizar um proxy para criar uma API mock para a sua solução. Isto permite que o desenvolvimento de cliente para curso, sem necessidade de back-end totalmente implementado. Mais tarde no desenvolvimento, pode criar uma nova aplicação de função que suporte esta lógica e redirecionar o proxy para a mesma.

Para criar esta API mock, vamos criar um novo proxy, este tempo, utilizando o [Editor do serviço de aplicações](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Para começar a utilizar, navegue até à sua aplicação de função no portal. Selecione **funcionalidades da plataforma** e, em **ferramentas de desenvolvimento** localizar **Editor do serviço de aplicações**. Clicar em Isto irá abrir o Editor de serviço de aplicações num novo separador.

Selecione `proxies.json` no painel de navegação esquerdo. Este é o ficheiro que armazena a configuração para todos os seus proxies. Se utilizar um do [funciona métodos de implementação](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), este é o ficheiro vai manter o controlo de origem. Para obter mais informações sobre este ficheiro, consulte o artigo [Proxies advanced configuration](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Se tiver seguido até ao momento, o proxies.json deve o seguinte aspeto:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Em seguida irá adicionar a API mock. Substitua o ficheiro proxies.json com o seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Esta ação adiciona um novo proxy, "GetUserByName", sem a propriedade backendUri. Em vez de chamar outro recurso, modifica a resposta de predefinição do Proxies utilizando uma substituição de resposta. Substituições de pedido e resposta também podem ser utilizadas em conjunto com um URL de back-end. Isto é particularmente útil quando a funcionalidade de proxy para um sistema legado, onde poderá ter de modificar cabeçalhos, consultar parâmetros, etc. Para saber mais sobre o pedido e resposta substituições, consulte o artigo [modificar pedidos e respostas no Proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Testar a sua API mock ao chamar o `<YourProxyApp>.azurewebsites.net/api/users/{username}` ponto final utilizando um browser ou o cliente REST favorito. Não se esqueça de substituir _{username}_ com um valor de cadeia representando um nome de utilizador.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar e personalizar uma API das funções do Azure. Também aprendeu como colocar várias APIs, incluindo mocks, em conjunto como uma superfície de API unificada. Pode utilizar estes técnicas para criar os APIs de qualquer complexidade, todos os ao ser executado no modelo de computação sem servidor forneceu as funções do Azure.

As seguintes referências podem ser útil como desenvolver ainda mais a API:

- [Enlaces de funções de HTTP e webhook do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [trabalhar com os Proxies de funções do Azure]
- [Documentar uma API de funções do Azure (pré-visualização)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[trabalhar com os Proxies de funções do Azure]: https://docs.microsoft.com/azure/azure-functions/functions-proxies

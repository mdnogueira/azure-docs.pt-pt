---
title: "Utilizar o serviço de API Management para gerar pedidos de HTTP"
description: "Saiba como utilizar políticas de pedido e resposta na API Management chamar serviços externos a partir da sua API"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 6b7f1268ea4893307713931e7288f5d38c5ee080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Utilizando os serviços externos do serviço de API Management do Azure
As políticas disponíveis no serviço de API Management do Azure o podem fazer uma vasta gama de trabalho útil com base puramente no pedido a receber, as respostas a enviar e informações básicas de configuração. No entanto, a ser capazes de interagir com os serviços externos da gestão de API políticas abre-se muitas oportunidades mais.

Podemos ter visto anteriormente como podemos pode interagir com o [serviço de Hub de eventos do Azure para o registo, monitorização e análise](api-management-log-to-eventhub-sample.md). Neste artigo, vamos demonstrar políticas que lhe permitem interagir com quaisquer HTTP externo com base em serviço. Estas políticas podem ser utilizadas para acionar eventos remotos ou para obter informações que serão utilizadas para manipular a pedido e resposta de alguma forma original.

## <a name="send-one-way-request"></a>Enviar-uma forma de pedido
Possivelmente a interação externa mais simples é o estilo fire e esquecido de pedido que permite que um serviço externo ser notificado de algum tipo de eventos importantes. Podemos utilizar a política de controlo de fluxo `choose` para detetar qualquer tipo de condição que estamos interessados em e, em seguida, se a condição for satisfeita, podemos efetuar um externo através de pedidos HTTP a [forma-pedido de envio-um](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) política. Isto pode dever um pedido para um sistema de mensagens como Hipchat ou Slack ou um API de correio como SendGrid ou MailChimp ou para incidentes de suporte críticos como PagerDuty. Todos estes sistemas de mensagens tem APIs HTTP simples que vamos facilmente pode invocar.

### <a name="alerting-with-slack"></a>Alertas com Slack
O exemplo seguinte demonstra como enviar uma mensagem para uma sala de chat Slack se o código de estado de resposta HTTP é maior que ou igual a 500. Um erro de 500 intervalo indica um problema com o nosso back-end da API que o cliente da nossa API não é possível resolver si próprios. Normalmente, requer algum tipo de intervenção no nosso parte.  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

Slack tem a noção de hooks de entrada web. Quando configurar um hook de entrada web, Slack gera um URL especial que lhe permite efetuar um pedido POST simple e introduzir uma mensagem para o canal Slack. O corpo JSON que criamos baseia-se no formato definido pelo Slack.

![Slack Web Hook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>É fire e se esqueça aconselhável suficientemente?
Existem determinadas fala quando utilizar um estilo fire e esquecido de pedido. Se por alguma razão, falha o pedido, em seguida, a falha não será reportada. Nesta situação específica, a complexidade de ter uma falha secundária, sistema e o custo de desempenho adicionais de aguardar a resposta de relatórios não é garantida. Para cenários em que é essencial para verificar a resposta a [pedido de envio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) política é uma opção melhor.

## <a name="send-request"></a>Pedido de envio
O `send-request` permite que política utilizar um serviço externo para realizar funções de processamento complexa e devolver dados para a gestão de API do serviço que pode ser utilizadas para processamento adicional da política.

### <a name="authorizing-reference-tokens"></a>Autorizar os tokens de referência
Uma função principais da API Management está a proteger os recursos de back-end. Se o servidor de autorização utilizado pela sua API cria [os tokens JWT](http://jwt.io/) como parte do seu fluxo de OAuth2, como [do Azure Active Directory](../active-directory/active-directory-aadconnect.md) , em seguida, pode utilizar o `validate-jwt` política para verificar a validade do token. No entanto, alguns servidores de autorização criar são conhecidas [referenciar tokens](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) que não é possível verificar sem efetuar uma chamada para o servidor de autorização.

### <a name="standardized-introspection"></a>Introspection normalizado
No passado não foi efectuada nenhuma forma normalizada de verificar um token de referência com um servidor de autorização. No entanto padrão recentemente proposto [RFC 7662](https://tools.ietf.org/html/rfc7662) publicou a IETF que define a forma como um servidor de recurso pode verificar a validade de um token.

### <a name="extracting-the-token"></a>Extrair o token
O primeiro passo é ao extrair o token do cabeçalho de autorização. O valor do cabeçalho deve ser formatado com o `Bearer` esquema de autorização, um único espaço e, em seguida, a autorização token como por [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Lamentamos, mas há casos em que o esquema de autorização for omitido. Para durante a análise de uma conta para isto, iremos dividir o valor do cabeçalho num espaço e selecione a cadeia de última da matriz devolvido de cadeias. Isto fornece uma solução para cabeçalhos de autorização incorretamente formatado.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Efetuar o pedido de validação
Assim que tivermos o token de autorização, podemos efetuar o pedido para validar o token. RFC 7662 chama introspection este processo e requer que lhe `POST` um formulário HTML para o recurso de introspection. O formulário HTML tem de conter, pelo menos, um par chave/valor com a chave `token`. Este pedido para o servidor de autorização também têm de ser autenticado para se certificar de que os clientes maliciosos não é possível ir trawling para tokens válidos.

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>A verificar a resposta
O `response-variable-name` atributo é utilizado para dar acesso a resposta devolvida. O nome definido nesta propriedade pode ser utilizado como uma chave para o `context.Variables` dicionário para aceder a `IResponse` objeto.

O objeto de resposta é possível obter o corpo e RFC 7622 informa-nos que a resposta tem de ser um objeto JSON e tem de conter, pelo menos, uma propriedade denominada `active` que é um valor booleano. Quando `active` for VERDADEIRO, o token é considerado válido.

### <a name="reporting-failure"></a>Falha de criação de relatórios
Utilizamos uma `<choose>` política para detetar se o token é inválido e, se assim for, devolver uma resposta 401.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

Como por [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) que descreve como `bearer` tokens devem ser utilizados, que também enviamos um `WWW-Authenticate` cabeçalho com a resposta 401. O WWW-Authenticate destina-se ao instruir o cliente sobre como construir um pedido corretamente autorizado. Devido à grande variedade de abordagens possíveis com a arquitetura de OAuth2, é difícil comunicar todas as informações necessárias. Felizmente, existem esforços em curso para o ajudar [clientes detetar como autorizar corretamente os pedidos para um servidor de recurso](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solução final
Passar todas as peças em conjunto, obtemos a seguinte política:

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

Esta é apenas um dos muitos exemplos de como o `send-request` política pode ser utilizada para integrar os serviços de externos útil para o processo de pedidos e respostas de fluir através do serviço de API Management.

## <a name="response-composition"></a>Composição de resposta
O `send-request` política pode ser utilizada para otimização um pedido principal para um sistema de back-end, à medida que vimos no exemplo anterior, ou pode ser utilizado como uma substituição completa para a chamada de back-end. Com esta técnica pode facilmente criamos compostos recursos que são agregados a partir de vários sistemas diferentes.

### <a name="building-a-dashboard"></a>Criar um dashboard
Por vezes, pretende poder para expor as informações que existe em vários sistemas de back-end, por exemplo, para um dashboard de unidade. Os KPIs provenientes de todas as diferentes back-ends, mas que prefira não fornecer acesso direto aos mesmos e seria nice se foi possível obter as informações num único pedido. Talvez algumas das informações de back-end tem de alguns segmentação e repartição e limpeza um pequeno primeiro! A capacidade para colocar em cache esse recurso composto seria útil reduzir a carga de back-end como sabe que os utilizadores têm um hábito de hammering a tecla F5 para ver se as métricas underperforming podem ser alterado.    

### <a name="faking-the-resource"></a>Faking o recurso
É o primeiro passo para criar o nosso recursos de dashboard para configurar uma operação de novo no portal do publicador da API Management. Esta será uma operação de marcador de posição utilizada para configurar a nossa política de composição para criar o nosso recursos dinâmicos.

![Operação de dashboard](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Efetuar os pedidos
Uma vez a `dashboard` operação ter sido criada, pode configurar uma política especificamente para essa operação. 

![Operação de dashboard](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

O primeiro passo é extrair os parâmetros de consulta do pedido de entrada, para que possa reencaminhar ao nosso back-end. Neste exemplo nosso dashboard está a ser mostrada informações com base no durante um período de tempo de um, por conseguinte, tem um `fromDate` e `toDate` parâmetro. Podemos utilizar o `set-variable` política para extrair as informações do URL do pedido.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Assim que tivermos estas informações podemos efetuar pedidos para todos os sistemas de back-end. Cada pedido constrói um novo URL com as informações de parâmetro e chama o respetivo servidor e armazena a resposta uma variável de contexto.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Estes pedidos são executados na sequência, que não é ideal. Uma versão futura irá ser introduzir uma nova política chamada `wait` que ativará todos estes pedidos para executar em paralelo.

### <a name="responding"></a>Responder
Para construir a resposta composta podemos utilizar o [retorno resposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) política. O `set-body` elemento pode utilizar uma expressão para construir um novo `JObject` com todos os componentes representações incorporadas como propriedades.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

A política concluída procura da seguinte forma:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

A configuração do marcador de posição operação que pode configurar o recurso de dashboard à colocadas em cache durante, pelo menos, uma hora porque compreendemos a natureza dos dados significa que mesmo se for uma hora desatualizada, irá ainda ser suficientemente eficiente para transmitir importantes informações para os utilizadores.

## <a name="summary"></a>Resumo
Serviço de API Management do Azure fornece as políticas flexíveis que podem ser aplicadas seletivamente a tráfego HTTP e permite a composição dos serviços de back-end. Se pretender melhorar o gateway de API com as funções, a verificação, capacidades de validação de alerta ou criar novos recursos compostos com base nos vários serviços de back-end, o `send-request` e políticas relacionadas abrir um mundo de possibilidades.

## <a name="watch-a-video-overview-of-these-policies"></a>Veja uma descrição geral do vídeo destas políticas
Para mais informações sobre o [forma-pedido de envio-um](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [pedido de envio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest), e [retorno resposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) abordadas neste artigo, as políticas, veja o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Send-Request-and-Return-Response-Policies/player]
> 
> 


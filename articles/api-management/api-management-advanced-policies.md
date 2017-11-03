---
title: "Gestão de API do Azure políticas avançadas | Microsoft Docs"
description: "Saiba mais sobre as políticas avançadas disponíveis para utilização na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 8a13348b-7856-428f-8e35-9e4273d94323
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: e5a658e0d20d42911870f2522f6c1bab7529ea11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-advanced-policies"></a>API de gestão avançada de políticas
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para obter informações sobre adicionar e configurar as políticas, consulte [políticas na API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AdvancedPolicies"></a>Políticas Avançadas  
  
-   [Controlar o fluxo](api-management-advanced-policies.md#choose) - condicionalmente aplica-se as declarações de política baseadas nos resultados da avaliação de Boolean [expressões](api-management-policy-expressions.md).  
  
-   [Reencaminhar pedido](#ForwardRequest) -reencaminha o pedido para o serviço de back-end.

-   [Limitar a simultaneidade](#LimitConcurrency) -impede entre as políticas de execução por mais do que o número especificado de pedidos de cada vez.
  
-   [Registo para o Hub de eventos](#log-to-eventhub) -envia mensagens no formato especificado para um Hub de eventos definidos por uma entidade de registo. 

-   [Resposta de mock](#mock-response) -execução de pipeline de cancelamentos e devolve uma resposta mocked diretamente para o autor da chamada.
  
-   [Repita](#Retry) -repete as tentativas de execução de declarações de política incluídos, se e até que a condição for satisfeita. Execução irá repetir a intervalos de tempo especificado e contagem de tentativas até especificado.  
  
-   [Devolver a resposta](#ReturnResponse) -execução de pipeline de cancelamentos e devolve a resposta especificada diretamente para o autor da chamada. 
  
-   [Enviar pedido unidirecional](#SendOneWayRequest) -envia um pedido para o URL especificado sem aguardar uma resposta.  
  
-   [Enviar pedido](#SendRequest) -envia um pedido para o URL especificado.  

-   [Definir o HTTP proxy](#SetHttpProxy) -permite-lhe a pedidos de rota reencaminhado através de um proxy HTTP.  

-   [Definir o método de pedido](#SetRequestMethod) -permite-lhe alterar o método HTTP para um pedido.  
  
-   [Definir o código de estado](#SetStatus) -altera o código de estado HTTP para o valor especificado.  
  
-   [Definir variável](api-management-advanced-policies.md#set-variable) -persistir um valor com um nome na [contexto](api-management-policy-expressions.md#ContextVariables) variável para acesso posterior.  

-   [Rastreio](#Trace) -adiciona uma cadeia para o [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/) saída.  
  
-   [Aguarde](#Wait) -aguarda para colocado [pedido de envio](api-management-advanced-policies.md#SendRequest), [obter o valor da cache](api-management-caching-policies.md#GetFromCacheByKey), ou [controlar o fluxo](api-management-advanced-policies.md#choose) políticas seja concluída antes de continuar.  
  
##  <a name="choose"></a>Fluxo de controlo  
 O `choose` política aplica-se a política incluída instruções com base no resultado da avaliação de expressões, semelhantes a um comutador ou um if-, em seguida,-else construir na linguagem de programação.  
  
###  <a name="ChoosePolicyStatement"></a>Declaração de política  
  
```xml  
<choose>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <otherwise>   
        <!— one or more policy statements to be applied if none of the above conditions are true  -->  
</otherwise>   
</choose>  
```  
  
 A política de controlo de fluxo tem de conter, pelo menos, um `<when/>` elemento. O `<otherwise/>` elemento é opcional. As condições em `<when/>` elementos são avaliados por ordem de seu aspeto na política. Declarações de política, incluídas entre o primeiro `<when/>` elemento com o atributo de condição for igual a `true` serão aplicadas. As políticas, incluída entre o `<otherwise/>` elemento, se estiver presente, será aplicado se todos os do `<when/>` são os atributos de condição do elemento `false`.  
  
### <a name="examples"></a>Exemplos  
  
####  <a name="ChooseExample"></a>Exemplo  
 O exemplo seguinte demonstra um [definir variável](api-management-advanced-policies.md#set-variable) política e duas políticas de controlo de fluxo.  
  
 A política de variável de conjunto é na secção de entrada e cria um `isMobile` booleano [contexto](api-management-policy-expressions.md#ContextVariables) variável que está definido como VERDADEIRO se o `User-Agent` pedido cabeçalho contém o texto `iPad` ou `iPhone`.  
  
 A política de controlo de fluxo primeiro também na secção de entrada e condicionalmente aplica-se um dos dois [defina o parâmetro de cadeia de consulta](api-management-transformation-policies.md#SetQueryStringParameter) políticas dependendo do valor da `isMobile` variável de contexto.  
  
 A segunda política de controlo de fluxo é na secção de saída e condicionalmente aplica-se a [converter XML para JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) política quando `isMobile` está definido como `true`.  
  
```xml  
<policies>  
    <inbound>  
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
        <base />  
        <choose>  
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>true</value>  
                </set-query-parameter>  
            </when>  
            <otherwise>  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>false</value>  
                </set-query-parameter>  
            </otherwise>  
        </choose>  
    </inbound>  
    <outbound>  
        <base />  
        <choose>  
            <when condition="@(context.GetValueOrDefault<bool>("isMobile"))">  
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>  
            </when>  
        </choose>  
    </outbound>  
</policies>  
```  
  
#### <a name="example"></a>Exemplo  
 Este exemplo mostra como efetuar a filtragem de conteúdo através da remoção de elementos de dados da resposta recebida do serviço de back-end ao utilizar o `Starter` produto. Para uma demonstração de configurar e utilizar esta política, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward para 34:30. Iniciar o 31:50 ver uma descrição geral do [a escuro Sky previsão API](https://developer.forecast.io/) utilizado para esta demonstração.  
  
```xml  
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->  
<choose>  
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">  
    <set-body>@{  
        var response = context.Response.Body.As<JObject>();  
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {  
          response.Property (key).Remove ();  
        }  
        return response.ToString();  
      }  
    </set-body>  
  </when>  
</choose>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|Escolha|Elemento raiz.|Sim|  
|Quando|A condição a utilizar para o `if` ou `ifelse` partes do `choose` política. Se o `choose` política tem vários `when` secções, estes são avaliadas sequencialmente. Uma vez a `condition` de um quando o elemento é avaliada para `true`, não mais `when` condições são avaliadas.|Sim|  
|caso contrário|Contém o fragmento de política para ser utilizado se nenhuma do `when` condições avaliadas como `true`.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|  
|---------------|-----------------|--------------|  
|condição = "expressão booleana &#124; Booleano constante"|A expressão booleana ou constante para avaliada quando o que contém `when` declaração de política é avaliada.|Sim|  
  
###  <a name="ChooseUsage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  
  
##  <a name="ForwardRequest"></a>Reencaminhar pedido  
 O `forward-request` política encaminha o pedido de entrada para o serviço de back-end especificado no pedido [contexto](api-management-policy-expressions.md#ContextVariables). O URL do serviço de back-end está especificado na API [definições](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) e pode ser alterado utilizando o [definir o serviço de back-end](api-management-transformation-policies.md) política.  
  
> [!NOTE]
>  Remover este resultados da política no pedido não reencaminhado para o back-end do serviço e as políticas na secção de saída são avaliadas imediatamente após a conclusão com êxito das políticas na secção de entrada.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<forward-request timeout="time in seconds" follow-redirects="true | false"/>  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="example"></a>Exemplo  
 A política de nível de API seguinte reencaminha todos os pedidos para o serviço de back-end com um intervalo de tempo limite de 60 segundos.  
  
```xml  
<!-- api level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="60"/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exemplo  
 Utiliza esta política de nível de operação de `base` elemento herdam a política de back-end principal âmbito de nível de API.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <base/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exemplo  
 Esta política de nível de operação explicitamente reencaminha todos os pedidos para o serviço de back-end com um tempo limite de 120 e não herda a política de back-end ao nível da API do principal.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="120"/>   
        <!-- effective policy. note the absence of <base/> -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exemplo  
 Esta política de nível de operação reencaminhar pedidos para o serviço de back-end.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <!-- no forwarding to backend -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|pedido de reencaminhamento|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|tempo limite = "número inteiro"|O intervalo de tempo limite em segundos antes da chamada para o serviço de back-end irá falhar.|Não|Não há tempo limite|  
|seguir redirecionamentos = "true &#124; FALSE"|Especifica se redirecionamentos a partir do serviço de back-end são seguidos pelo gateway ou devolvidos ao autor da chamada.|Não|FALSO|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** back-end  
  
-   **Âmbitos de política:** todos os âmbitos  
  
##  <a name="LimitConcurrency"></a>Simultaneidade de limite  
 O `limit-concurrency` política impede que as políticas de incluídos em execução em mais do que o número especificado de pedidos num determinado momento. Após ter excedido o limiar, os novos pedidos de são adicionados a uma fila, até que o comprimento máximo da fila é conseguido. Após o esgotamento de filas, novos pedidos falhará imediatamente.
  
###  <a name="LimitConcurrencyStatement"></a>Declaração de política  
  
```xml  
<limit-concurrency key="expression" max-count="number" timeout="in seconds" max-queue-length="number">
        <!— nested policy statements -->  
</limit-concurrency>
``` 

### <a name="examples"></a>Exemplos  
  
####  <a name="ChooseExample"></a>Exemplo  
 O exemplo seguinte demonstra como limitar o número de pedidos reencaminhados para um back-end baseado no valor de uma variável de contexto.
 
```xml  
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3" timeout="60">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|    
|simultaneidade de limite|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|--------------|  
|key|Uma cadeia. Expressão permitido. Especifica o âmbito de concorrência. Pode ser partilhado por várias políticas.|Sim|N/D|  
|contagem máxima|Um número inteiro. Especifica um número máximo de pedidos que estão autorizados a introduzir a política.|Sim|N/D|  
|tempo limite|Um número inteiro. Expressão permitido. Especifica o número de segundos deve aguardar um pedido para introduzir um âmbito antes de falhar com "429 demasiados pedidos muitos"|Não|Infinity|  
|comprimento da fila de máx.|Um número inteiro. Expressão permitido. Especifica o comprimento máximo da fila. Os pedidos recebidos tentar introduza esta política serão terminados com "429 demasiados pedidos muitos" imediatamente quando a fila se esgota.|Não|Infinity|  
  
###  <a name="ChooseUsage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  

##  <a name="log-to-eventhub"></a>Registo para o Hub de eventos  
 O `log-to-eventhub` política envia mensagens no formato especificado para um Hub de eventos definidos por uma entidade de registo. Como o nome indica, a política é utilizada para guardar a informação selecionada de contexto de pedido ou resposta para análise online ou offline.  
  
> [!NOTE]
>  Para obter um guia passo a passo sobre como configurar um hub de eventos e os eventos de registo, consulte [como registar eventos de API Management com o Event Hubs do Azure](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">  
  Expression returning a string to be logged  
</log-to-eventhub>  
  
```  
  
### <a name="example"></a>Exemplo  
 Qualquer cadeia pode ser utilizada como o valor para ser registado no Event Hubs. Neste exemplo, a data e hora, nome do serviço de implementação, id do pedido, endereço ip e nome de operação para todas as chamadas de entrada são registadas para o hub de eventos registador registado com o `contoso-logger` id.  
  
```xml  
<policies>  
  <inbound>  
    <log-to-eventhub logger-id ='contoso-logger'>  
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )   
    </log-to-eventhub>  
  </inbound>  
  <outbound>          
  </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|registo para eventhub|Elemento raiz. O valor deste elemento é a cadeia para iniciar sessão para o hub de eventos.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|  
|---------------|-----------------|--------------|  
|id de registo|O id de registo registado com o serviço de API Management.|Sim|  
|id de partição|Especifica o índice da partição onde são enviadas as mensagens.|Opcional. Este atributo não pode ser utilizado se `partition-key` é utilizado.|  
|chave de partição|Especifica o valor utilizado para a atribuição de partição, quando são enviadas as mensagens.|Opcional. Este atributo não pode ser utilizado se `partition-id` é utilizado.|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  

##  <a name="mock-response"></a>Resposta mock  
O `mock-response`, como o nome indica, é utilizada para APIs e operações de mock. Interrompe a execução de normal pipeline e devolve uma resposta mocked para o autor da chamada. A política sempre tenta devolver respostas de fidelidade mais elevada. -Prefers exemplos conteúdo da resposta, sempre que disponível. Gera respostas de exemplo de esquemas, quando são fornecidas esquemas e exemplos não estão. Se forem encontradas nem exemplos ou esquemas, as respostas com nenhum conteúdo são devolvidas.
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<mock-response status-code="code" content-type="media type"/>  
  
```  
  
### <a name="examples"></a>Exemplos  
  
```xml  
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|resposta de mock|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|--------------|  
|código de estado|Especifica o código de estado de resposta e é utilizado para selecionar exemplo correspondente ou esquema.|Não|200|  
|tipo de conteúdo|Especifica `Content-Type` valor de cabeçalho de resposta e é utilizado para selecionar exemplo correspondente ou esquema.|Não|Nenhuma|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos

##  <a name="Retry"></a>Tente novamente  
 O `retry` política executa as respetivas políticas subordinado uma vez e, em seguida, repete a respetiva execução até que a repetição `condition` fica `false` ou repita `count` é esgotados.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
  
<retry  
    condition="boolean expression or literal"  
    count="number of retry attempts"  
    interval="retry interval in seconds"  
    max-interval="maximum retry interval in seconds"  
    delta="retry interval delta in seconds"  
    first-fast-retry="boolean expression or literal">  
        <!-- One or more child policies. No restrictions -->  
</retry>  
  
```  
  
### <a name="example"></a>Exemplo  
 O pedido de exemplo seguintes forewarding é repetida até dez vezes utilizando exponencial novamente o algoritmo. Uma vez que `first-fast-retry` é definido como FALSO, todas as tentativas de repetição estão sujeitos o algoritmo de repetição exponsntial.  
  
```xml  
  
<retry  
    condition="@(context.Response.StatusCode == 500)"  
    count="10"  
    interval="10"  
    max-interval="100"  
    delta="10"  
    first-fast-retry="false">  
        <forward-request />  
</retry>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|retry|Elemento raiz. Pode conter quaisquer outras políticas como os respetivos elementos subordinados.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|Condição|Um booleano literal ou [expressão](api-management-policy-expressions.md) especificar se as tentativas devem ser paradas (`false`) ou continuou (`true`).|Sim|N/D|  
|Contagem|Um número positivo que especifica o número máximo de tentativas a tentar.|Sim|N/D|  
|intervalo|Um número positivo em segundos, especificando o intervalo de espera entre a repetição tenta.|Sim|N/D|  
|intervalo máximo|Um número positivo no especificando o número máximo de segundos de espera intervalo entre tentativas de repetição. É utilizado para implementar um algoritmo de repetição exponencial.|Não|N/D|  
|delta|Um número positivo em segundos, especificando o incremento de intervalo de espera. É utilizado para implementar os algoritmos de repetição linear e exponencial.|Não|N/D|  
|primeiro-fast-repetir|Se definido como `true` , a primeira tentativa de repetição é executada imediatamente.|Não|`false`|  
  
> [!NOTE]
>  Quando apenas o `interval` for especificado, **fixo** são efetuadas tentativas de intervalo.  
>  Quando apenas o `interval` e `delta` estão especificados um **linear** algoritmo de repetição do intervalo for utilizado, onde o tempo de espera entre tentativas é calculado de acordo com a seguinte fórmula - `interval + (count - 1)*delta`.  
>  Quando o `interval`, `max-interval` e `delta` forem especificados, **exponencial** algoritmo de repetição de intervalo é aplicado, onde o tempo de espera entre as repetições está a crescer exponencialmente do valor `interval` para o valor `max-interval` , de acordo com a seguinte forumula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Tenha em atenção que as restrições de utilização do subordinado política serão herdadas por esta política.  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  
  
##  <a name="ReturnResponse"></a>Devolver a resposta  
 O `return-response` política interrompe a execução de pipeline e devolve uma predefinição ou resposta personalizada para o autor da chamada. Resposta de predefinição está `200 OK` com nenhum corpo. Resposta personalizada pode ser especificada através de declarações de política ou variável de contexto. Quando ambas são fornecidas, a resposta contida a variável de contexto é modificada pelas declarações de política antes de a ser devolvido para o autor da chamada.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<return-response response-variable-name="existing context variable">  
  <set-header/>  
  <set-body/>  
  <set-status/>  
</return-response>  
  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<return-response>  
   <set-status code="401" reason="Unauthorized"/>  
   <set-header name="WWW-Authenticate" exists-action="override">  
      <value>Bearer error="invalid_token"</value>  
   </set-header>  
</return-response>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|resposta de retorno|Elemento raiz.|Sim|  
|cabeçalho de conjunto|A [conjunto cabeçalho](api-management-transformation-policies.md#SetHTTPheader) declaração de política.|Não|  
|corpo de conjunto|A [conjunto corpo](api-management-transformation-policies.md#SetBody) declaração de política.|Não|  
|Definir estado|A [definir estado](api-management-advanced-policies.md#SetStatus) declaração de política.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|  
|---------------|-----------------|--------------|  
|nome da variável de resposta|O nome da variável de contexto referenciado a partir de, por exemplo, um montante [pedido de envio](api-management-advanced-policies.md#SendRequest) política e contém um `Response` objeto|Opcional.|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  
  
##  <a name="SendOneWayRequest"></a>Enviar pedido unidirecional  
 O `send-one-way-request` política envia o pedido fornecido para o URL especificado sem aguardar uma resposta.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<send-one-way-request mode="new | copy">  
  <url>...</url>  
  <method>...</method>  
  <header name="" exists-action="override | skip | append | delete">...</header>  
  <body>...</body>  
</send-one-way-request>  
  
```  
  
### <a name="example"></a>Exemplo  
 Esta política de exemplo mostra um exemplo de utilização a `send-one-way-request` política para enviar uma mensagem para uma sala de chat Slack se o código de resposta HTTP é maior que ou igual a 500. Para obter mais informações sobre este exemplo, consulte [utilizando os serviços externos do serviço de API Management do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|enviar-uma forma de pedido|Elemento raiz.|Sim|  
|URL|O URL do pedido.|Não se modo = cópia; Sim, caso contrário.|  
|Método|O método HTTP para o pedido.|Não se modo = cópia; Sim, caso contrário.|  
|cabeçalho|Cabeçalho do pedido. Utilize vários elementos de cabeçalho para vários cabeçalhos de pedido.|Não|  
|Corpo|O corpo do pedido.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|modo = "cadeia"|Determina se se trata de um novo pedido ou uma cópia do pedido atual. No modo de saída, modo = cópia não inicializou o corpo do pedido.|Não|novo|  
|nome|Especifica o nome do cabeçalho de ser definida.|Sim|N/D|  
|ação existe|Especifica a ação a tomar quando o cabeçalho já está especificado. Este atributo tem de ter um dos seguintes valores.<br /><br /> -Ignorar - substitui o valor do cabeçalho existente.<br />-Ignorar - não substitui o valor de cabeçalho existente.<br />-Acrescentar - acrescenta o valor para o valor de cabeçalho existente.<br />-delete - remove o cabeçalho do pedido.<br /><br /> Quando definido como `override` inscrever várias entradas com o mesmo nome resulta no cabeçalho de que está a ser definido de acordo com todas as entradas (que serão apresentadas várias vezes); apenas os valores listados serão definidos no resultado.|Não|substituir|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  
  
##  <a name="SendRequest"></a>Enviar o pedido  
 O `send-request` política envia o pedido de fornecido para o URL especificado, a aguardar não há mais do que o valor de tempo limite do conjunto.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error  
="false|true">  
  <set-url>...</set-url>  
  <set-method>...</set-method>  
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>  
  <set-body>...</set-body>  
</send-request>  
  
```  
  
### <a name="example"></a>Exemplo  
 Este exemplo mostra uma forma de verificar a referência a uma token com um servidor de autorização. Para obter mais informações sobre este exemplo, consulte [utilizando os serviços externos do serviço de API Management do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|pedido de envio|Elemento raiz.|Sim|  
|URL|O URL do pedido.|Não se modo = cópia; Sim, caso contrário.|  
|Método|O método HTTP para o pedido.|Não se modo = cópia; Sim, caso contrário.|  
|cabeçalho|Cabeçalho do pedido. Utilize vários elementos de cabeçalho para vários cabeçalhos de pedido.|Não|  
|Corpo|O corpo do pedido.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|modo = "cadeia"|Determina se se trata de um novo pedido ou uma cópia do pedido atual. No modo de saída, modo = cópia não inicializou o corpo do pedido.|Não|novo|  
|nome da variável de resposta = "cadeia"|Se não estiver presente, `context.Response` é utilizado.|Não|N/D|  
|tempo limite = "número inteiro"|O intervalo de tempo limite em segundos antes da chamada para o URL de falha.|Não|60|  
|Ignorar-erro|Se VERDADEIRO e os resultados de pedido num erro:<br /><br /> -Se foi especificado o nome de variável de resposta irá conter um valor nulo.<br />-Se não foi especificado o nome da variável de resposta, contexto. Não será possível atualizar o pedido.|Não|FALSO|  
|nome|Especifica o nome do cabeçalho de ser definida.|Sim|N/D|  
|ação existe|Especifica a ação a tomar quando o cabeçalho já está especificado. Este atributo tem de ter um dos seguintes valores.<br /><br /> -Ignorar - substitui o valor do cabeçalho existente.<br />-Ignorar - não substitui o valor de cabeçalho existente.<br />-Acrescentar - acrescenta o valor para o valor de cabeçalho existente.<br />-delete - remove o cabeçalho do pedido.<br /><br /> Quando definido como `override` inscrever várias entradas com o mesmo nome resulta no cabeçalho de que está a ser definido de acordo com todas as entradas (que serão apresentadas várias vezes); apenas os valores listados serão definidos no resultado.|Não|substituir|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  
  
##  <a name="SetHttpProxy"></a>Proxy HTTP de conjunto  
 O `proxy` política permite-lhe pedidos de rota reencaminhados para o back-ends através de um proxy HTTP. Apenas o HTTP (não HTTPS) é suportado entre o gateway e o proxy. Básico e apenas a autenticação NTLM.
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<proxy url="http://hostname-or-ip:port" username="username" password="password" />  
  
```  
  
### <a name="example"></a>Exemplo  
Tenha em atenção a utilização de [propriedades](api-management-howto-properties.md) como valores de nome de utilizador e palavra-passe para evitar a armazenar informações confidenciais no documento de política.  
  
```xml  
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|Proxy|Elemento raiz|Sim|  

### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|URL = "cadeia"|URL de proxy no formato http://host:port.|Sim|N/D|  
|nome de utilizador = "cadeia"|Nome de utilizador a ser utilizado para a autenticação com o proxy.|Não|N/D|  
|palavra-passe = "cadeia"|Palavra-passe a ser utilizado para a autenticação com o proxy.|Não|N/D|  

### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** todos os âmbitos  

##  <a name="SetRequestMethod"></a>Método de pedido de conjunto  
 O `set-method` política permite-lhe alterar o método de pedido HTTP para um pedido.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<set-method>METHOD</set-method>  
  
```  
  
### <a name="example"></a>Exemplo  
 Esta política de exemplo que utiliza o `set-method` política mostra um exemplo de uma mensagem a enviar para uma sala de chat Slack se o código de resposta HTTP é maior que ou igual a 500. Para obter mais informações sobre este exemplo, consulte [utilizando os serviços externos do serviço de API Management do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|método de conjunto|Elemento raiz. O valor do elemento Especifica o método HTTP.|Sim|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  
  
##  <a name="SetStatus"></a>Código de estado de conjunto  
 O `set-status` política define o código de estado HTTP para o valor especificado.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<set-status code="" reason=""/>  
  
```  
  
### <a name="example"></a>Exemplo  
 Este exemplo mostra como devolver uma resposta 401 se o token de autorização é inválido. Para obter mais informações, consulte [utilizando os serviços externos do serviço de API Management do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)  
  
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
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|Definir estado|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|código = "número inteiro"|O código de estado HTTP a devolver.|Sim|N/D|  
|razão = "cadeia"|Uma descrição do motivo para devolver o código de estado.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  

##  <a name="set-variable"></a>Definir variável  
 O `set-variable` política declara um [contexto](api-management-policy-expressions.md#ContextVariables) variável e atribui-um valor especificado através de um [expressão](api-management-policy-expressions.md) ou um literal de cadeia. Se a expressão contém um literal será convertido numa cadeia e o tipo do valor será `System.String`.  
  
###  <a name="set-variablePolicyStatement"></a>Declaração de política  
  
```xml  
<set-variable name="variable name" value="Expression | String literal" />  
```  
  
###  <a name="set-variableExample"></a>Exemplo  
 O exemplo seguinte demonstra uma política de variável definido na secção de entrada. Esta política de variável de conjunto cria um `isMobile` booleano [contexto](api-management-policy-expressions.md#ContextVariables) variável que está definido como VERDADEIRO se o `User-Agent` pedido cabeçalho contém o texto `iPad` ou `iPhone`.  
  
```xml  
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|Definir variável|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|  
|---------------|-----------------|--------------|  
|nome|O nome da variável.|Sim|  
|valor|O valor da variável. Isto pode ser uma expressão ou um valor literal.|Sim|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  
  
###  <a name="set-variableAllowedTypes"></a>Tipos permitidos  
 As expressões utilizadas no `set-variable` política tem de devolver um dos seguintes tipos de básicos.  
  
-   Boolean  
  
-   System.SByte  
  
-   System.Byte  
  
-   System.UInt16  
  
-   System.UInt32  
  
-   UInt64  
  
-   System.Int16  
  
-   System. Int32  
  
-   System. Int64  
  
-   System  
  
-   System.Single  
  
-   System.Double  
  
-   GUID  
  
-   String  
  
-   Char  
  
-   DateTime  
  
-   System.TimeSpan  
  
-   System.Byte?  
  
-   System.UInt16?  
  
-   System.UInt32?  
  
-   UInt64?  
  
-   System.Int16?  
  
-   System. Int32?  
  
-   System. Int64?  
  
-   System?  
  
-   System.Single?  
  
-   System.Double?  
  
-   GUID?  
  
-   String?  
  
-   Char?  
  
-   DateTime?  

##  <a name="Trace"></a>Rastreio  
 O `trace` política adiciona uma cadeia para o [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/) saída. A política será executado apenas quando o rastreio é acionado, ou seja, `Ocp-Apim-Trace` cabeçalho do pedido está presente e definido para `true` e `Ocp-Apim-Subscription-Key` cabeçalho do pedido está presente e contém uma chave válida associada à conta de administrador.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
  
<trace source="arbitrary string literal"/>  
    <!-- string expression or literal -->  
</trace>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|Rastreio|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|origem|Literal de cadeia significativo para o Visualizador de rastreio e especificar a origem da mensagem.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** todos os âmbitos  
  
##  <a name="Wait"></a>Aguarde  
 O `wait` política executa as respetivas políticas subordinado imediata em paralelo e aguarda todas ou uma das respetivas políticas de subordinados imediatos para concluir antes da conclusão. A política de espera pode ter como respetivas políticas subordinados imediatos [pedido de envio](api-management-advanced-policies.md#SendRequest), [obter o valor da cache](api-management-caching-policies.md#GetFromCacheByKey), e [controlar o fluxo](api-management-advanced-policies.md#choose) políticas.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<wait for="all|any">  
  <!--Wait policy can contain send-request, cache-lookup-value,   
        and choose policies as child elements -->  
</wait>  
  
```  
  
### <a name="example"></a>Exemplo  
 No exemplo seguinte existem duas `choose` políticas como políticas subordinado imediata do `wait` política. Cada um destes `choose` políticas executa em paralelo. Cada `choose` política tenta obter um valor em cache. Se existir uma falha de acerto na cache, um serviço de back-end é chamado para fornecer o valor. Neste exemplo de `wait` política não for concluída até que todas as respetivas políticas subordinados imediatos concluir, porque o `for` atributo está definido como `all`.   Neste exemplo as variáveis de contexto (`execute-branch-one`, `value-one`, `execute-branch-two`, e `value-two`) declarados exterior ao âmbito desta política de exemplo.  
  
```xml  
<wait for="all">  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-one="])">  
      <cache-lookup-value key="key-one" variable-name="value-one" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-one="))">  
          <send-request mode="new" response-variable-name="value-one">  
            <set-url>https://backend-one</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-two="])">  
      <cache-lookup-value key="key-two" variable-name="value-two" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-two="))">  
          <send-request mode="new" response-variable-name="value-two">  
            <set-url>https://backend-two</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
</wait>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|Aguarde|Elemento raiz. Pode conter como elementos subordinados só `send-request`, `cache-lookup-value`, e `choose` políticas.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|Para|Determina se o `wait` aguarda política para todas as políticas de subordinados imediatos seja concluído ou apenas um. Valores permitidos são:<br /><br /> -   `all`-aguardar para todas as políticas de subordinados imediatos concluir<br />-qualquer - Aguarde qualquer política de subordinados imediatos para concluir. Depois de concluída a política de imediato subordinados primeiro, o `wait` conclusão da política e a execução de outras políticas de subordinados imediatos é terminada.|Não|Todos os|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end  
  
-   **Âmbitos de política:**todos os âmbitos  
  
## <a name="next-steps"></a>Passos seguintes
Para obter mais informações para trabalhar com as políticas, consulte:
-   [Políticas de gestão de API](api-management-howto-policies.md) 
-   [Expressões de política](api-management-policy-expressions.md)

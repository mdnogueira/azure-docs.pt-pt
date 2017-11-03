---
title: "Políticas de transformação de API Management do Azure | Microsoft Docs"
description: "Saiba mais sobre as políticas de transformação disponíveis para utilização na API Management do Azure."
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 7406a8ce-5f9c-4fae-9b0f-e574befb2ee9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c2bed904b82c569b28a6e00d0cc9b49107c148dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-transformation-policies"></a>Políticas de transformação de API Management
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para obter informações sobre adicionar e configurar as políticas, consulte [políticas na API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="TransformationPolicies"></a>Políticas de transformação  
  
-   [Converter o JSON em XML](api-management-transformation-policies.md#ConvertJSONtoXML) - converte ou um pedido de corpo de resposta JSON em XML.  
  
-   [Converter XML para JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - converte ou um pedido de corpo de resposta a partir de XML para JSON.  
  
-   [Localizar e substituir a cadeia no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) - localiza uma subcadeia de pedido ou resposta e substitui-lo com uma subcadeia diferentes.  
  
-   [Mascarar URLs no conteúdo](api-management-transformation-policies.md#MaskURLSContent) -ligações reescreve (máscaras) na resposta body para que possam apontam para a ligação equivalente através do gateway.  
  
-   [Definir o serviço de back-end](api-management-transformation-policies.md#SetBackendService) -o serviço de back-end para um pedido recebido é alterado.  
  
-   [Definir o corpo](api-management-transformation-policies.md#SetBody) -define o corpo da mensagem para pedidos de enviados e receção.  
  
-   [Cabeçalho de HTTP de conjunto](api-management-transformation-policies.md#SetHTTPheader) - atribui um valor para uma resposta existente e/ou o cabeçalho do pedido ou adiciona um cabeçalho de resposta e/ou pedido de novo.  
  
-   [Defina o parâmetro de cadeia de consulta](api-management-transformation-policies.md#SetQueryStringParameter) - adiciona, substitui o valor de ou elimina o parâmetro de cadeia de consulta do pedido.  
  
-   [URL de regravação](api-management-transformation-policies.md#RewriteURL) -converte um URL de pedido do respetivo formulário público para o formato esperado pelo serviço web.  
  
-   [Transformação XML utilizando um XSLT](api-management-transformation-policies.md#XSLTransform) -aplica uma transformação XSL em XML no corpo do pedido ou resposta.  
  
##  <a name="ConvertJSONtoXML"></a>Converter o JSON em XML  
 O `json-to-xml` política converte um corpo do pedido ou resposta JSON em XML.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false"/>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
    </inbound>  
    <outbound>  
        <base />  
        <json-to-xml apply="always" consider-accept-header="false" />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|JSON para xml|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|aplicar|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> aplica - sempre - sempre a conversão.<br />converter a - json de tipo de conteúdo - apenas se o cabeçalho de resposta Content-Type indica a presença de JSON.|Sim|N/D|  
|Considere-aceitar-cabeçalho|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> aplica - verdadeiro - conversão se for pedido JSON no pedido. o cabeçalho Accept.<br />-false - sempre aplicam-se a conversão.|Não|VERDADEIRO|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, no caso de erro  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="ConvertXMLtoJSON"></a>Converter XML para JSON  
 O `xml-to-json` política converte um corpo do pedido ou resposta de XML para JSON. Esta política pode ser utilizada para modernize APIs com base nos serviços web de back-end só de XML.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
    </inbound>  
    <outbound>  
        <base />  
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|XML para json|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|tipo|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> javascript-compatível com - convertido JSON tem um formato amigável aos programadores de JavaScript.<br />-direta - JSON convertido reflete a estrutura do documento XML original.|Sim|N/D|  
|aplicar|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> Converta - sempre - sempre.<br />converter o-conteúdo tipo-xml - apenas se o cabeçalho de resposta Content-Type indique a presença de XML.|Sim|N/D|  
|Considere-aceitar-cabeçalho|O atributo tem de ser definido para um dos seguintes valores.<br /><br /> aplica - verdadeiro - conversão se for pedido o XML no pedido. o cabeçalho Accept.<br />-false - sempre aplicam-se a conversão.|Não|VERDADEIRO|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, no caso de erro  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="Findandreplacestringinbody"></a>Localizar e substituir a cadeia no corpo  
 O `find-and-replace` política localiza uma subcadeia de pedido ou resposta e substitui-lo com uma subcadeia diferentes.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<find-and-replace from="what to replace" to="replacement" />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<find-and-replace from="notebook" to="laptop" />  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|localizar e substituir|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|Do|A cadeia a procurar.|Sim|N/D|  
|para|A cadeia de substituição. Especifique uma zero cadeia de substituição de comprimento para remover a cadeia de procura.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="MaskURLSContent"></a>URLs de máscara no conteúdo  
 O `redirect-content-urls` política reescreve ligações (máscaras) no corpo da resposta, para que possam apontam para a ligação equivalente através do gateway. Utilize a secção de saída para escrever novamente as ligações de corpo de resposta para torná-los a apontar para o gateway. Utilize a secção de entrada para um efeito oposto.  
  
> [!NOTE]
>  Esta política não altera os valores de cabeçalho, tais como `Location` cabeçalhos. Para alterar os valores de cabeçalho, utilize o [conjunto cabeçalho](api-management-transformation-policies.md#SetHTTPheader) política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<redirect-content-urls />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<redirect-content-urls />  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|urls de redirecionamento conteúdo|Elemento raiz.|Sim|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="SetBackendService"></a>Serviço de back-end de conjunto  
 Utilize o `set-backend-service` política para redirecionar um pedido de entrada para um back-end diferente daquela especificado nas definições de API para essa operação. Esta política altera o URL de base de serviço de back-end do pedido a receber para especificada na política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<set-backend-service base-url="base URL of the backend service" />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <choose>  
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">  
                <set-backend-service base-url="http://contoso.com/api/8.2/" />  
            </when>  
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">  
                <set-backend-service base-url="http://contoso.com/api/9.1/" />  
            </when>  
        </choose>  
        <base />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
Neste exemplo a política de serviço do conjunto back-end encaminha os pedidos com base no valor de versão transmitido na cadeia de consulta para um serviço de back-end diferentes que um especificado na API.
  
Inicialmente, o URL base do serviço de back-end é derivado das definições de API. Por isso, o URL do pedido `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` fica `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` onde `http://contoso.com/api/10.4/` é o URL do serviço de back-end especificado nas definições de API.  
  
Quando o [< escolha\> ](api-management-advanced-policies.md#choose) declaração de política é aplicada o URL base do serviço de back-end pode alterar o novamente ao `http://contoso.com/api/8.2` ou `http://contoso.com/api/9.1`, consoante o valor do parâmetro de consulta do pedido de versão. Por exemplo, se o valor for `"2013-15"` o pedido final URL fica `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.  
  
Se ainda mais transformação do pedido for pretendido, outros [políticas de transformação](api-management-transformation-policies.md#TransformationPolicies) pode ser utilizado. Por exemplo, para remover o parâmetro de consulta de versão, agora que o pedido está a ser encaminhado para um versão específica back-end, o [defina o parâmetro de cadeia de consulta](api-management-transformation-policies.md#SetQueryStringParameter) política pode ser utilizada para remover o atributo de versão agora redundante.  
  
### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" /> 
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
Neste exemplo a política encaminha o pedido para um serviço fabric back-end, utilizando a cadeia de consulta de userId como a chave de partição e utilizar a réplica primária da partição.  

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|serviço de back-end de conjunto|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|url de base|Novo back-end base URL do serviço.|Não|N/D|  
|id de back-end|Identificador do back-end para encaminhar para.|Não|N/D|  
|chave de partição SF|Apenas aplicável quando o back-end é um serviço do Service Fabric e for especificado utilizando o 'id de back-end'. Utilizado para resolver uma partição específica do serviço de resolução do nome.|Não|N/D|  
|tipo de réplica SF|Apenas aplicável quando o back-end é um serviço do Service Fabric e for especificado utilizando o 'id de back-end'. Controla se o pedido deve passar para a réplica primária ou secundária de uma partição. |Não|N/D|    
|condição de resolver SF|Apenas aplicável quando o back-end é um serviço do Service Fabric. Condição identificar se a chamada de back-end do Service Fabric tem de ser repetido com a resolução de novo.|Não|N/D|    
|SF-serviço--nome da instância|Apenas aplicável quando o back-end é um serviço do Service Fabric. Permite alterar as instâncias de serviço no tempo de execução. |Não|N/D|    

### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada back-end  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="SetBody"></a>Corpo de conjunto  
 Utilize o `set-body` política para definir o corpo da mensagem para pedidos de enviados e receção. Para aceder ao corpo da mensagem, pode utilizar o `context.Request.Body` propriedade ou o `context.Response.Body`, consoante se a política é na secção de entrada ou de saída.  
  
> [!IMPORTANT]
>  Tenha em atenção que, por predefinição, quando acedem a mensagem body utilizando `context.Request.Body` ou `context.Response.Body`, o corpo da mensagem original é perdido e tem de ser definido ao devolver o corpo volta na expressão. Para preservar o conteúdo do corpo, defina o `preserveContent` parâmetro `true` ao aceder a mensagem. Se `preserveContent` está definido como `true` e um corpo diferentes é devolvido pela expressão de corpo devolvido é utilizado.  
>   
>  Tenha em atenção as seguintes considerações ao utilizar o `set-body` política.  
>   
>  -   Se estiver a utilizar o `set-body` política para devolver um corpo novo ou atualizado, não precisa de definir `preserveContent` para `true` porque são explicitamente a fornecer os novos conteúdos do corpo.  
> -   Preserva o conteúdo de uma resposta no pipeline de entrada não fazer sentido porque não ainda não existe nenhuma resposta.  
> -   Preserva o conteúdo de um pedido no pipeline de saída não fazer sentido, porque o pedido já foi enviado para o back-end neste momento.  
> -   Se esta política é utilizada quando não existe nenhum corpo da mensagem, por exemplo numa entrada GET, é emitida uma exceção.  
  
 Para obter mais informações, consulte o `context.Request.Body`, `context.Response.Body`e o `IMessage` secções no [variável de contexto](api-management-policy-expressions.md#ContextVariables) tabela.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<set-body>new body value as text</set-body>  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="literal-text-example"></a>Exemplo de texto literal  
  
```xml  
<set-body>Hello world!</set-body>  
```  
  
#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exemplo ao aceder ao corpo como uma cadeia. Tenha em atenção que iremos são preservação de corpo do pedido original para que o pode aceder ao mesmo mais tarde no pipeline.
  
```xml  
<set-body>  
@{   
    string inBody = context.Request.Body.As<string>(preserveContent: true);   
    if (inBody[0] =='c') {   
        inBody[0] = 'm';   
    }   
    return inBody;   
}  
</set-body>  
```  
  
#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accesing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exemplo ao aceder ao corpo como um JObject. Tenha em atenção que uma vez que não podemos são reservar o corpo do pedido original, accesing mais tarde no pipeline irá resultar numa exceção.  
  
```xml  
<set-body>   
@{   
    JObject inBody = context.Request.Body.As<JObject>();   
    if (inBody.attribute == <tag>) {   
        inBody[0] = 'm';   
    }   
    return inBody.ToString();   
}   
</set-body>  
  
```  
  
#### <a name="filter-response-based-on-product"></a>Resposta do filtro com base no produto  
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

### <a name="using-liquid-templates-with-set-body"></a>Utilizar modelos Liquid com corpo de conjunto 
O `set-body` política pode ser configurada para utilizar o [Liquid](https://shopify.github.io/liquid/basics/introduction/) idioma templating para transfom o corpo do pedido ou numa resposta. Isto pode ser muito eficaz se precisar de reshape completamente o formato da mensagem.

> [!IMPORTANT]
> A implementação de Liquid utilizado o `set-body` política está configurada no modo c#. Isto é especialmente importante quando fazer coisas como filtragem. Por exemplo, utilizando um filtro de data requer a utilização de Pascal tem maiúsculas e minúsculas e a c# data formatação por ex.:
>
> {{body.foo.startDateTime| Data: "yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Para vincular corretamente para um corpo XML utilizando o modelo Liquid, utilize um `set-header` política para definir o tipo de conteúdo para a aplicação/xml, texto/xml (ou qualquer tipo terminando + xml); para um corpo JSON, tem de ser application/json, texto/json (ou qualquer tipo, terminando + json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Converter o JSON de SOAP através de um modelo Liquid
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="tranform-json-using-a-liquid-template"></a>JSON de Tranform através de um modelo Liquid
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|corpo de conjunto|Elemento raiz. Contém o texto de corpo ou uma expressão que devolve um corpo.|Sim|  

### <a name="properties"></a>Propriedades  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|modelo|Utilizado para alterar o modo de templating que será executada a política de corpo de conjunto. Atualmente é o único valor suportado:<br /><br />-liquid - a política de corpo de conjunto utilizará o motor de liquid templating |Não|Liquid|  

Para aceder a informações sobre o pedido e resposta, o modelo Liquid pode vincular a um objeto de contexto com as seguintes propriedades: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="SetHTTPheader"></a>Cabeçalho de HTTP de conjunto  
 O `set-header` política atribui um valor para uma resposta existente e/ou o cabeçalho do pedido ou adiciona um cabeçalho de resposta e/ou pedido de novo.  
  
 Insere uma lista de cabeçalhos HTTP numa mensagem de HTTP. Quando colocado num pipeline de entrada, esta política define os cabeçalhos HTTP para o pedido que está a ser transmitido para o serviço de destino. Quando colocado num pipeline de saída, esta política define os cabeçalhos HTTP para a resposta a ser enviados para o cliente do gateway.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<set-header name="header name" exists-action="override | skip | append | delete">  
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->  
</set-header>  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="example"></a>Exemplo  
  
```xml  
<set-header name="some header name" exists-action="override">  
    <value>20</value>   
</set-header>  
```  
  
#### <a name="forward-context-information-to-the-backend-service"></a>Informações de contexto de reencaminhar para o serviço de back-end  
 Este exemplo mostra como aplicar a política ao nível da API para fornecer informações de contexto para o serviço de back-end. Para uma demonstração de configurar e utilizar esta política, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward para 10:30. Às 12:10 há uma demonstração da chamada de uma operação no portal do programador, onde pode ver a política no trabalho.  
  
```xml  
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->  
<set-header name="x-request-context-data" exists-action="override">  
  <value>@(context.User.Id)</value>  
  <value>@(context.Deployment.Region)</value>  
</set-header>  
```  
  
 Para obter mais informações, consulte [expressões de política](api-management-policy-expressions.md) e [variável de contexto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|cabeçalho de conjunto|Elemento raiz.|Sim|  
|valor|Especifica o valor de cabeçalho de ser definida. Para adicionar vários cabeçalhos com o mesmo nome `value` elementos.|Sim|  
  
### <a name="properties"></a>Propriedades  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|ação existe|Especifica a ação a tomar quando o cabeçalho já está especificado. Este atributo tem de ter um dos seguintes valores.<br /><br /> -Ignorar - substitui o valor do cabeçalho existente.<br />-Ignorar - não substitui o valor de cabeçalho existente.<br />-Acrescentar - acrescenta o valor para o valor de cabeçalho existente.<br />-delete - remove o cabeçalho do pedido.<br /><br /> Quando definido como `override` inscrever várias entradas com o mesmo nome resulta no cabeçalho de que está a ser definido de acordo com todas as entradas (que serão apresentadas várias vezes); apenas os valores listados serão definidos no resultado.|Não|substituir|  
|nome|Especifica o nome do cabeçalho de ser definida.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="SetQueryStringParameter"></a>Parâmetro de cadeia de consulta de conjunto  
 O `set-query-parameter` política adiciona, substitui o valor, ou parâmetro de cadeia de consulta do pedido eliminações. Pode ser utilizado para transmitir parâmetros esperado pelo serviço de back-end que são opcionais de consulta ou nunca presentes no pedido.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<set-query-parameter name="param name" exists-action="override | skip | append | delete">  
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->  
</set-query-parameter>  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="example"></a>Exemplo  
  
```xml  
  
<set-query-parameter>  
  <parameter name="api-key" exists-action="skip">  
    <value>12345678901</value>  
  </parameter>  
  <!-- for multiple parameters with the same name add additional value elements -->  
</set-query-parameter>  
  
```  
  
#### <a name="forward-context-information-to-the-backend-service"></a>Informações de contexto de reencaminhar para o serviço de back-end  
 Este exemplo mostra como aplicar a política ao nível da API para fornecer informações de contexto para o serviço de back-end. Para uma demonstração de configurar e utilizar esta política, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward para 10:30. Às 12:10 há uma demonstração da chamada de uma operação no portal do programador, onde pode ver a política no trabalho.  
  
```xml  
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->  
<set-query-parameter name="x-product-name" exists-action="override">  
  <value>@(context.Product.Name)</value>  
</set-query-parameter>  
  
```  
  
 Para obter mais informações, consulte [expressões de política](api-management-policy-expressions.md) e [variável de contexto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|parâmetro de consulta de conjunto|Elemento raiz.|Sim|  
|valor|Especifica o valor do parâmetro de consulta seja definida. Para adicionar vários parâmetros de consulta com o mesmo nome `value` elementos.|Sim|  
  
### <a name="properties"></a>Propriedades  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|ação existe|Especifica a ação a tomar quando o parâmetro de consulta já foi especificado. Este atributo tem de ter um dos seguintes valores.<br /><br /> -Ignorar - substitui o valor do parâmetro existente.<br />-Ignorar - não substitui o valor de parâmetro de consulta existente.<br />-Acrescentar - acrescenta o valor para o valor de parâmetro de consulta existente.<br />-delete - remove o parâmetro de consulta no pedido.<br /><br /> Quando definido como `override` inscrever várias entradas com o mesmo nome resulta no parâmetro de consulta que está a ser definido de acordo com todas as entradas (que serão apresentadas várias vezes); apenas os valores listados serão definidos no resultado.|Não|substituir|  
|nome|Especifica o nome do parâmetro de consulta seja definida.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada back-end  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="RewriteURL"></a>URL de reescrever  
 O `rewrite-uri` política converte um URL de pedido do respetivo formulário público para o formato esperado para o serviço web, conforme mostrado no exemplo seguinte.  
  
-   URL público-`http://api.example.com/storenumber/ordernumber`  
  
-   URL do pedido-`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`  
  
 Esta política pode ser utilizada quando um URL humano e/ou compatível com o browser deve ser transformado para o formato de URL esperado pelo serviço web. Esta política apenas tem de ser aplicadas quando a exposição de um formato de URL alternativo, tal como URLs limpo, RESTful URLs, URLs amigáveis de utilizador ou URLs de SEO amigável que são puramente estruturais URLs que não contém uma cadeia de consulta e em vez disso, contém apenas o caminho do recurso (após o esquema e a autoridade). Muitas vezes, tal aesthetic, facilidade de utilização ou motor de busca fins de otimização (SEO).  
  
> [!NOTE]
>  Só é possível adicionar parâmetros de cadeia de consulta através da política. Não é possível adicionar parâmetros do modelo extra caminho no URL reescrever.  

### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>  
    <inbound>  
        <base />  
        <rewrite-uri template="/put" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
<!-- Resulting URL will be /put?c=d -->
```  
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>  
    <inbound>  
        <base />  
        <rewrite-uri template="/put" copy-unmatched-params="false" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|uri de reescrever|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|Descrição|Necessário|Predefinição|  
|---------------|-----------------|--------------|-------------|  
|modelo|O URL do serviço web real com quaisquer parâmetros de cadeia de consulta. Quando utilizar expressões, o valor todo tem de ser uma expressão.|Sim|N/D|  
|cópia parâmetros sem correspondência|Especifica se os parâmetros de consulta no pedido a receber não está presente no modelo original URL são adicionados ao URL definido pelo modelo de reescrita|Não|VERDADEIRO|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** operação de produto, API,  
  
##  <a name="XSLTransform"></a>Transformação XML utilizando um XSLT  
 O `Transform XML using an XSLT` política se aplica uma transformação XSL XML no corpo do pedido ou resposta.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<xsl-transform>  
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>  
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">  
        <xsl:output method="xml" indent="yes" />  
        <xsl:param name="User-Agent" />  
        <xsl:template match="* | @* | node()">  
            <xsl:copy>  
                <xsl:if test="self::* and not(parent::*)">  
                    <xsl:attribute name="User-Agent">  
                        <xsl:value-of select="$User-Agent" />  
                    </xsl:attribute>  
                </xsl:if>  
                <xsl:apply-templates select="* | @* | node()" />  
            </xsl:copy>  
        </xsl:template>  
    </xsl:stylesheet>  
  </xsl-transform>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
  <inbound>  
      <base />  
  </inbound>  
  <outbound>  
      <base />  
      <xsl-transform>  
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">  
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />  
            <!-- Copy all nodes directly-->  
            <xsl:template match="node()| @*|*">  
                <xsl:copy>  
                    <xsl:apply-templates select="@* | node()|*" />  
                </xsl:copy>  
            </xsl:template>  
        </xsl:stylesheet>  
    </xsl-transform>  
  </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|transformação XSL|Elemento raiz.|Sim|  
|Parâmetro|Utilizado para definir as variáveis utilizadas na transformação|Não|  
|xsl: stylesheet|Elemento de folha de estilos de raiz. Todos os elementos e atributos definidos no seguem o padrão [especificação XSLT](http://www.w3.org/TR/xslt)|Sim|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
## <a name="next-steps"></a>Passos seguintes
Para obter mais informações para trabalhar com as políticas, consulte [políticas na API Management](api-management-howto-policies.md).  

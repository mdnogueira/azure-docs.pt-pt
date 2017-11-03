---
title: "Colocação em cache as políticas de gestão de API do Azure | Microsoft Docs"
description: "Saiba mais sobre as políticas de colocação em cache disponíveis para utilização na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c7604fdb948a2f4d2adca5d6821d9ea36e96dae6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-caching-policies"></a>Políticas de gestão de API de colocação em cache
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para obter informações sobre adicionar e configurar as políticas, consulte [políticas na API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CachingPolicies"></a>Políticas de colocação em cache  
  
-   Resposta de colocação em cache de políticas  
  
    -   [Obter a partir da cache](api-management-caching-policies.md#GetFromCache) -efetuar cache procurar e devolver um válido as respostas em cache quando disponível.  
  
    -   [Arquivo de cache](api-management-caching-policies.md#StoreToCache) -coloca em cache respostas de acordo com a configuração de controlo de cache especificado.  
  
-   Valor de políticas de colocação em cache  
  
    -   [Obter o valor da cache](#GetFromCacheByKey) -obter um item em cache por chave.  
  
    -   [Armazenar o valor na cache](#StoreToCacheByKey) -armazenar um item na cache por chave.  
  
    -   [Remova o valor da cache](#RemoveCacheByKey) -remover um item na cache por chave.  
  
##  <a name="GetFromCache"></a>Obter a partir da cache  
 Utilize o `cache-lookup` política para efetuar a cache procurar e devolver uma resposta em cache válida quando disponível. Esta política pode ser aplicada em casos onde o conteúdo de resposta permanece estático durante um período de tempo. Resposta a colocação em cache reduz a largura de banda e os requisitos de processamento imposta no back-end web server e reduz latência de percetível pelos consumidores de API.  
  
> [!NOTE]
>  Esta política tem de ter um correspondente [arquivo para colocar em cache](api-management-caching-policies.md#StoreToCache) política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
  <vary-by-header>Accept</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Accept-Charset</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Authorization</vary-by-header>  
  <!-- should be present when allow-authorized-response-caching is "true"-->  
  <vary-by-header>header name</vary-by-header>  
  <!-- optional, can repeated several times -->  
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>  
  <!-- optional, can repeated several times -->  
</cache-lookup>  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true">  
            <vary-by-query-parameter>version</vary-by-query-parameter>  
        </cache-lookup>           
    </inbound>  
    <outbound>  
        <cache-store duration="seconds" />  
        <base />          
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Exemplo utilizando expressões de política  
 Este exemplo mostra como para configurar a resposta da API Management duração que corresponda a resposta a colocação em cache do serviço de back-end como a colocação em cache especificado pelo serviço de cópia `Cache-Control` diretiva. Para uma demonstração de configurar e utilizar esta política, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward para 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Para obter mais informações, consulte [expressões de política](api-management-policy-expressions.md) e [variável de contexto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|pesquisa de cache|Elemento raiz.|Sim|  
|variar pelo cabeçalho|Iniciar a colocação em cache respostas por valor do cabeçalho especificado, tais como autorização aceitar, aceitar-conjunto de carateres, aceitar codificação, idioma de aceitar, esperado, do anfitrião, If-Match.|Não|  
|variar-por--parâmetro de consulta|Inicie a colocação em cache respostas por valor dos parâmetros de consulta especificada. Introduza um único ou vários parâmetros. Utilize ponto e vírgula como separador. Se estiver especificados, todos os parâmetros de consulta são utilizados.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|permitir-privada-resposta-colocação em cache|Quando definido como `true`, permite a colocação em cache de pedidos que contêm um cabeçalho de autorização.|Não|FALSO|  
|a jusante tipo colocação em cache|Este atributo deve ser definido para um dos seguintes valores.<br /><br /> -nenhum - não é permitida a colocação em cache a jusante.<br />-privada - a jusante privada colocação em cache é permitida.<br />-públicas - privadas e partilhado a jusante a colocação em cache é permitida.|Não|Nenhum|  
|tem-revalidate|Se a jusante a colocação em cache está ativada neste atributo transforma ou desativar o `must-revalidate` diretiva de controlo de cache nas respostas de gateway.|Não|VERDADEIRO|  
|variar pelo programador|Definido como `true` para respostas de cache por chave de programador.|Não|FALSO|  
|variar-por-programador-groups|Definido como `true` para respostas de cache por função de utilizador.|Não|FALSO|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** API, operação, produto  
  
##  <a name="StoreToCache"></a>Armazene a cache  
 O `cache-store` política coloca em cache respostas de acordo com as definições de cache especificado. Esta política pode ser aplicada em casos onde o conteúdo de resposta permanece estático durante um período de tempo. Resposta a colocação em cache reduz a largura de banda e os requisitos de processamento imposta no back-end web server e reduz latência de percetível pelos consumidores de API.  
  
> [!NOTE]
>  Esta política tem de ter um correspondente [aproveitar a cache](api-management-caching-policies.md#GetFromCache) política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cache-store duration="seconds" />  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
          <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">  
                <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->  
        </cache-lookup>  
    </inbound>  
    <outbound>  
        <base />  
            <cache-store duration="3600" />  
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Exemplo utilizando expressões de política  
 Este exemplo mostra como para configurar a resposta da API Management duração que corresponda a resposta a colocação em cache do serviço de back-end como a colocação em cache especificado pelo serviço de cópia `Cache-Control` diretiva. Para uma demonstração de configurar e utilizar esta política, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward para 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Para obter mais informações, consulte [expressões de política](api-management-policy-expressions.md) e [variável de contexto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|arquivo de cache|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|Duração|Especificar o Time-to-live das entradas em cache, em segundos.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** saída  
  
-   **Âmbitos de política:** API, operação, produto  
  
##  <a name="GetFromCacheByKey"></a>Obter o valor da cache  
 Utilize o `cache-lookup-value` política para efetuar a pesquisa de cache pela chave e devolver um valor em cache. A chave pode ter um valor de cadeia arbitrária e, geralmente, é fornecida através de uma expressão de política.  
  
> [!NOTE]
>  Esta política tem de ter um correspondente [armazenar o valor na cache](#StoreToCacheByKey) política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to" />  
```  
  
### <a name="example"></a>Exemplo  
 Para obter mais informações e exemplos desta política, consulte [personalizada de colocação em cache na API Management do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|valor de pesquisa de cache|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|valor predefinido|Um valor que será atribuído para a variável, caso a pesquisa de chave de cache resultou numa falha de acerto na. Se este atributo não for especificado, `null` está atribuído.|Não|`null`|  
|key|Valor de chave de cache para utilizar a pesquisa.|Sim|N/D|  
|nome da variável|Nome do [variável de contexto](api-management-policy-expressions.md#ContextVariables) o valor looked cópias de segurança será atribuído, se a pesquisa foi concluída com êxito. Se a pesquisa resulta numa falha de acerto na, a variável será atribuída o valor da `default-value` atributo ou `null`, se o `default-value` atributo for omitido.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** global, API, operação, produto  
  
##  <a name="StoreToCacheByKey"></a>Armazenar o valor na cache  
 O `cache-store-value` efetua o armazenamento de cache pela chave. A chave pode ter um valor de cadeia arbitrária e, geralmente, é fornecida através de uma expressão de política.  
  
> [!NOTE]
>  Esta política tem de ter um correspondente [obter o valor da cache](#GetFromCacheByKey) política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" />  
```  
  
### <a name="example"></a>Exemplo  
 Para obter mais informações e exemplos desta política, consulte [personalizada de colocação em cache na API Management do Azure](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|valor de arquivo de cache|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|Duração|Valor serão colocadas em cache para o valor de duração fornecido, especificado em segundos.|Sim|N/D|  
|key|O valor de chave de cache será armazenado em.|Sim|N/D|  
|valor|O valor a ser colocados em cache.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** global, API, operação, produto  
  
###  <a name="RemoveCacheByKey"></a>Remova o valor da cache  
 O `cache-remove-value` elimina um item em cache identificado pela respetiva chave. A chave pode ter um valor de cadeia arbitrária e, geralmente, é fornecida através de uma expressão de política.  
  
#### <a name="policy-statement"></a>Declaração de política  
  
```xml  
  
<cache-remove-value key="cache key value"/>  
  
```  
  
#### <a name="example"></a>Exemplo  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|cache-remove-valor|Elemento raiz.|Sim|  
  
#### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|key|A chave do valor anteriormente em cache a serem removidos da cache.|Sim|N/D|  
  
#### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .  
  
-   **As secções de política:** entrada, saída, back-end, no caso de erro  
  
-   **Âmbitos de política:** global, API, operação, produto  
  

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações para trabalhar com as políticas, consulte [políticas na API Management](api-management-howto-policies.md).  
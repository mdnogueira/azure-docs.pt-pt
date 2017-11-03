---
title: "As políticas de restrição de acesso da API Management do Azure | Microsoft Docs"
description: "Saiba mais sobre as políticas de restrição de acesso disponíveis para utilização na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: a8bbe6c4f6919f150012163b0c7559d2986e072f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-access-restriction-policies"></a>Políticas de restrição de acesso de gestão de API
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para obter informações sobre adicionar e configurar as políticas, consulte [políticas na API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AccessRestrictionPolicies"></a>Políticas de restrição de acesso  
  
-   [Cabeçalho HTTP de verificação](api-management-access-restriction-policies.md#CheckHTTPHeader) -impõe a existência de e/ou valor de um cabeçalho de HTTP.  
  
-   [Limitar taxa de chamadas por subscrição](api-management-access-restriction-policies.md#LimitCallRate) -picos de utilização da API impede ao limitar taxa de chamadas, numa base por subscrição.  
  
-   [Limitar taxa de chamadas por chave](#LimitCallRateByKey) -picos de utilização da API impede ao limitar taxa de chamadas, numa base por chave.  
  
-   [Restringir o chamador IPs](api-management-access-restriction-policies.md#RestrictCallerIPs) -chamadas de filtros (permite/nega) provenientes de endereços IP específicos e/ou intervalos de endereços.  
  
-   [Definir quota de utilização por subscrição](api-management-access-restriction-policies.md#SetUsageQuota) -permite-lhe impor uma renováveis duração da chamada de volume e/ou de largura de banda de quota ou, numa base por subscrição.  
  
-   [Definir quota de utilização por chave](#SetUsageQuotaByKey) -permite-lhe impor uma renováveis duração da chamada de volume e/ou de largura de banda de quota ou, numa base por chave.  
  
-   [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) -impõe existência e validade de um JWT extraído de um cabeçalho de HTTP especificado ou um parâmetro de consulta especificada.  
  
##  <a name="CheckHTTPHeader"></a>Verifique o cabeçalho de HTTP  
 Utilize o `check-header` política para impor que um pedido tem um cabeçalho HTTP especificado. Opcionalmente, pode verificar se o cabeçalho tem um valor específico ou verifique a existência de um intervalo de valores permitidos. Se a verificação falhar, a política termina o processamento de pedidos e devolve o estado e o código de erro mensagem de HTTP especificada pela política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="True">  
    <value>Value1</value>  
    <value>Value2</value>  
</check-header>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">  
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>  
</check-header>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|cabeçalho de verificação|Elemento raiz.|Sim|  
|valor|Valor de cabeçalho HTTP permitido. Quando são especificados vários elementos de valor, a verificação é considerada um êxito se qualquer um dos valores de uma correspondência.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|Falha-verifique--mensagem de erro|Mensagem de erro para devolver no corpo da resposta HTTP, se o cabeçalho não existe ou tem um valor inválido. Esta mensagem tem de ter os caracteres especiais caráter de escape correto.|Sim|N/D|  
|Falha-verifique-httpcode|Código de estado de HTTP a devolver se o cabeçalho não existe ou tem um valor inválido.|Sim|N/D|  
|nome do cabeçalho|O nome do cabeçalho de HTTP para verificar.|Sim|N/D|  
|caso ignorar|Pode ser definido como True ou False. Se definido como verdadeiro maiúsculas e minúsculas é ignorado quando o valor do cabeçalho é comparado com o conjunto de valores aceitáveis.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada, saída  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="LimitCallRate"></a>Limitar taxa de chamadas por subscrição  
 O `rate-limit` política impede picos de utilização de API numa base por subscrição ao limitar taxa de chamadas para um número especificado por um período de tempo especificado. Quando esta política é acionada o autor da chamada recebe um `429 Too Many Requests` código de estado de resposta.  
  
> [!IMPORTANT]
>  Esta política pode ser utilizada apenas uma vez por documento de política.  
>   
>  [Expressões de política](api-management-policy-expressions.md) não pode ser utilizado em qualquer um dos atributos de política para esta política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<rate-limit calls="number" renewal-period="seconds">  
    <api name="name" calls="number" renewal-period="seconds">  
        <operation name="name" calls="number" renewal-period="seconds" />  
    </api>  
</rate-limit>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit calls="20" renewal-period="90" />  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|limite de conjunto|Elemento raiz.|Sim|  
|api|Adicione um ou mais destes elementos para impor um limite de taxa de chamadas no APIs dentro do produto. Produto e a API de limites são aplicados de forma independente de taxa de chamadas.|Não|  
|operação|Adicione um ou mais destes elementos para impor um limite de taxa de chamada operações dentro de uma API. Produto, API e operação chamar taxa limites são aplicados de forma independente.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|nome|O nome da API para o qual pretende aplicar o limite de taxa.|Sim|N/D|  
|chamadas|O número máximo total de chamadas permitida durante o intervalo de tempo especificado no `renewal-period`.|Sim|N/D|  
|período de renovação|O período de tempo em segundos após o qual repõe a quota.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** produto  
  
##  <a name="LimitCallRateByKey"></a>Limitar taxa de chamadas por chave  
 O `rate-limit-by-key` política impede picos de utilização de API numa base por chave ao limitar taxa de chamadas para um número especificado por um período de tempo especificado. A chave pode ter um valor de cadeia arbitrária e, geralmente, é fornecida através de uma expressão de política. Pode ser adicionada a condição de incremento opcional para especificar quais os pedidos devem ser contados para o limite. Quando esta política é acionada o autor da chamada recebe um `429 Too Many Requests` código de estado de resposta.  
  
 Para obter mais informações e exemplos desta política, consulte [limitação API Management do Azure de pedidos avançada](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Esta política pode ser utilizada apenas uma vez por documento de política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>Exemplo  
 No exemplo seguinte, o limite de taxa é codificado por endereço IP de autor da chamada.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit-by-key  calls="10"  
              renewal-period="60"  
              increment-condition="@(context.Response.StatusCode == 200)"  
              counter-key="@(context.Request.IpAddress)"/>  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|limite de conjunto|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|chamadas|O número máximo total de chamadas permitida durante o intervalo de tempo especificado no `renewal-period`.|Sim|N/D|  
|Counter-chave|A chave a utilizar para a política de limite de taxa.|Sim|N/D|  
|condição de incremento|Expressão booleana especificar se o pedido deve ser contabilizado para a quota (`true`).|Não|N/D|  
|período de renovação|O período de tempo em segundos após o qual repõe a quota.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="RestrictCallerIPs"></a>Restringir o chamador IPs  
 O `ip-filter` (permite/nega) chamadas provenientes de endereços IP específicos e/ou intervalos de endereços de filtros de política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|filtro de IP|Elemento raiz.|Sim|  
|Endereço|Especifica um único endereço IP para filtrar.|Pelo menos um `address` ou `address-range` elemento é necessário.|  
|intervalo de endereços de = "address" para = "address"|Especifica um intervalo de endereços IP no qual pretende filtrar.|Pelo menos um `address` ou `address-range` elemento é necessário.|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|intervalo de endereços de = "address" para = "address"|Um intervalo de endereços IP para permitir ou negar o acesso.|Obrigatório quando o `address-range` elemento é utilizado.|N/D|  
|ação de filtro de IP = "Permitir &#124; forbid"|Especifica se devem ser permitidas chamadas ou não para os intervalos e endereços IP especificados.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="SetUsageQuota"></a>Definir quota de utilização por subscrição  
 O `quota` política impõe uma renováveis duração da chamada de volume e/ou de largura de banda de quota ou, numa base por subscrição.  
  
> [!IMPORTANT]
>  Esta política pode ser utilizada apenas uma vez por documento de política.  
>   
>  [Expressões de política](api-management-policy-expressions.md) não pode ser utilizado em qualquer um dos atributos de política para esta política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">  
    <api name="name" calls="number" bandwidth="kilobytes">  
        <operation name="name" calls="number" bandwidth="kilobytes" />  
    </api>  
</quota>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|quota|Elemento raiz.|Sim|  
|api|Adicione um ou mais destes elementos para impor uma quota em APIs dentro do produto. Quotas de produto e API são aplicadas de forma independente.|Não|  
|operação|Adicione um ou mais destes elementos para impor uma quota em operações de dentro de uma API. Quotas de produto, API e operação são aplicadas de forma independente.|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|nome|O nome da API ou operação para o qual se aplica a quota.|Sim|N/D|  
|Largura de banda|O número total máximo de quilobytes permitidas durante o intervalo de tempo especificado no `renewal-period`.|O `calls`, `bandwidth`, ou ambos em conjunto tem de ser especificadas.|N/D|  
|chamadas|O número máximo total de chamadas permitida durante o intervalo de tempo especificado no `renewal-period`.|O `calls`, `bandwidth`, ou ambos em conjunto tem de ser especificadas.|N/D|  
|período de renovação|O período de tempo em segundos após o qual repõe a quota.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** produto  
  
##  <a name="SetUsageQuotaByKey"></a>Definir quota de utilização por chave  
 O `quota-by-key` política impõe uma renováveis duração da chamada de volume e/ou de largura de banda de quota ou, numa base por chave. A chave pode ter um valor de cadeia arbitrária e, geralmente, é fornecida através de uma expressão de política. Pode ser adicionada a condição de incremento opcional para especificar quais os pedidos devem ser contados para a quota.  
  
 Para obter mais informações e exemplos desta política, consulte [limitação API Management do Azure de pedidos avançada](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Esta política pode ser utilizada apenas uma vez por documento de política.  
>   
>  [Expressões de política](api-management-policy-expressions.md) não pode ser utilizado em qualquer um dos atributos de política para esta política.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>Exemplo  
 No exemplo seguinte, a quota é codificada por endereço IP de autor da chamada.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"  
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"  
                      counter-key="@(context.Request.IpAddress)" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|quota|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|Largura de banda|O número total máximo de quilobytes permitidas durante o intervalo de tempo especificado no `renewal-period`.|O `calls`, `bandwidth`, ou ambos em conjunto tem de ser especificadas.|N/D|  
|chamadas|O número máximo total de chamadas permitida durante o intervalo de tempo especificado no `renewal-period`.|O `calls`, `bandwidth`, ou ambos em conjunto tem de ser especificadas.|N/D|  
|Counter-chave|A chave a utilizar para a política de quota.|Sim|N/D|  
|condição de incremento|Expressão booleana especificar se o pedido deve ser contabilizado para a quota (`true`)|Não|N/D|  
|período de renovação|O período de tempo em segundos após o qual repõe a quota.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
##  <a name="ValidateJWT"></a>Validar JWT  
 O `validate-jwt` política impõe existência e validade de um JWT extraídos a uma determinada de cabeçalho de HTTP ou um parâmetro de consulta especificada.  
  
> [!IMPORTANT]
>  O `validate-jwt` política requer que o `exp` afirmação registada é inlcuded no JWT token, a menos que `require-expiration-time` atributo especificado e definido `false`.  
> O `validate-jwt` política suporta HS256 e RS256 algoritmos de assinatura. Para HS256 a chave tem de ser fornecida inline na política no formato codificado em base64. Para RS256 a chave tem de fornecer através de um ponto final de configuração de ID aberta.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>  
  <audiences>  
    <audience>audience string</audience>  
    <!-- if there are multiple possible audiences, then add additional audience elements -->  
  </audiences>  
  <issuers>  
    <issuer>issuer string</issuer>  
    <!-- if there are multiple possible issuers, then add additional issuer elements -->  
  </issuers>  
  <required-claims>  
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>  
      <!-- if there is more than one allowed values, then add additional value elements -->  
    </claim>  
    <!-- if there are multiple possible allowed values, then add additional value elements -->  
  </required-claims>  
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />  
  <zumo-master-key id="key identifier">key value</zumo-master-key>  
</validate-jwt>  
  
```  
  
### <a name="examples"></a>Exemplos  
  
#### <a name="azure-mobile-services-token-validation"></a>Validação de token de Mobile Services do Azure  
  
```xml  
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">  
    <issuers>  
        <issuer>urn:microsoft:windows-azure:zumo</issuer>  
    </issuers>  
    <audiences>  
        <audience>Facebook</audience>  
    </audiences>  
    <issuer-signing-keys>  
        <zumo-master-key id="0">insert key here</zumo-master-key>  
    </issuer-signing-keys>  
</validate-jwt>  
```  
  
#### <a name="azure-active-directory-token-validation"></a>Validação de token do Azure Active Directory  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />  
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Validação de token do Azure Active Directory B2C  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Autorizar o acesso às operações de baseadas em afirmações de token  
 Este exemplo mostra como utilizar o [validar JWT](api-management-access-restriction-policies.md#ValidateJWT) política para pré-autorizar o acesso às operações de baseadas em afirmações de token. Para uma demonstração de configurar e utilizar esta política, consulte [nuvem abrangem episódio 177: mais funcionalidades de gestão de API com Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e fast-forward a 13:50 carateres. Reencaminhe rápido para 15:00 para ver as políticas configuradas no editor de política e, em seguida, para 18:50 para uma demonstração de chamar uma operação a partir do portal do programador com e sem o token de autorização necessário.  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|Descrição|Necessário|  
|-------------|-----------------|--------------|  
|jwt validar|Elemento raiz.|Sim|  
|público|Contém uma lista de afirmações de audiência aceitável que pode estar presente no token. Se existirem vários valores de público-alvo, em seguida, cada valor é experimentada até que todos os ficam esgotados (caso em que falha de validação) ou até uma ter êxito. Tem de ser especificado pelo menos um público-alvo.|Não|  
|assinatura-chaves do emissor|Uma lista de chaves de segurança com codificação Base64 utilizadas para validar os tokens assinados. Se existirem várias chaves de segurança, em seguida, é experimentada uma cada chave até que todos os ficam esgotados (caso em que falha de validação) ou até uma ter êxito (útil para token rollover). Os elementos chaves ter opcional `id` atributo utilizado para correspondência `kid` de afirmação.|Não|  
|emissores|Uma lista de principais aceitáveis que emitiu o token. Se existirem vários valores de emissor, em seguida, cada valor é experimentada até que todos os ficam esgotados (caso em que falha de validação) ou até uma ter êxito.|Não|  
|openid-config|O elemento utilizado para especificar um compatível abrir ID configuração ponto final partir da qual a assinatura de chaves e do emissor pode ser obtido.|Não|  
|afirmações necessários|Contém uma lista de afirmações deve estar presente no token para o mesmo ser considerado válido. Quando o `match` atributo está definido como `all` cada valor de afirmação na política tem de estar presente no token para validação com êxito. Quando o `match` atributo está definido como `any` , pelo menos, uma afirmação deve estar presente no token para validação com êxito.|Não|  
|chave de mestra zumo|Chave mestra para tokens emitidos pelo Mobile Services do Azure|Não|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|desfasamento de relógio|TimeSpan. Fornece algumas pequena leeway no caso de afirmação de expiração do token está presente no token e é posterior à data atual / hora.|Não|0 segundos|  
|Falha-validação--mensagem de erro|Mensagem de erro para devolver no corpo da resposta HTTP, se o JWT não transmite a validação. Esta mensagem tem de ter os caracteres especiais caráter de escape correto.|Não|Mensagem de erro predefinida depende de problema de validação, por exemplo "JWT não está presente."|  
|Falha-validação-httpcode|Código de estado de HTTP a devolver se a JWT não aprovado na validação.|Não|401|  
|nome do cabeçalho|O nome do cabeçalho de HTTP que contém o token.|O `header-name` ou `query-paremeter-name` tem de ser especificado; mas não ambos.|N/D|  
|ID|O `id` atributo no `key` elemento permite-lhe especificar a cadeia que irá ser comparada com `kid` de afirmação no token (caso exista) localizar a chave a utilizar para a validação da assinatura adequada.|Não|N/D|  
|Correspondência|O `match` atributo no `claim` elemento Especifica se cada valor de afirmação na política tem de estar presente no token para validação com êxito. Os valores possíveis são:<br /><br /> -                          `all`-cada valor de afirmação na política tem de estar presente no token para validação com êxito.<br /><br /> -                          `any`-valor, pelo menos, uma afirmação deve estar presente no token para validação com êxito.|Não|Todos os|  
|nome da paremeter de consulta|O nome do parâmetro de consulta que contém o token.|O `header-name` ou `query-paremeter-name` tem de ser especificado; mas não ambos.|N/D|  
|exigir expiração-tempo|valor booleano. Especifica se uma afirmação de expiração é necessária no token.|Não|VERDADEIRO|
|esquema de exigir|O nome do token de esquema, por exemplo "Portador". Quando este atributo for definido, a política irá garantir que esquema especificada encontra-se no valor de cabeçalho de autorização.|Não|N/D|
|exigir-assinado-tokens|valor booleano. Especifica se um token é necessário para ser iniciada.|Não|VERDADEIRO|  
|separador|Cadeia. Especifica um separador (por exemplo, ",") para ser utilizado para extrair um conjunto de valores de uma afirmação com múltiplos valor.|Não|N/D| 
|URL|Abra o URL de ponto final de configuração de ID de onde podem obter metadados de configuração de ID aberta. Para o Azure Active Directory, utilize o seguinte URL: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` substituindo o nome do seu inquilino de diretório, por exemplo, `contoso.onmicrosoft.com`.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
## <a name="next-steps"></a>Passos seguintes
Para obter mais informações para trabalhar com as políticas, consulte [políticas na API Management](api-management-howto-policies.md).  

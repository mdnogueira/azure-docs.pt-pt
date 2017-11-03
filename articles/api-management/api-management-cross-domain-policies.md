---
title: "Gestão de API do Azure políticas entre domínios | Microsoft Docs"
description: "Saiba mais sobre as políticas de domínio cruzada disponíveis para utilização na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 05b25ffad4a91859932cd53475d82b11bf3e43e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-cross-domain-policies"></a>Políticas entre domínios da API Management
Este tópico fornece uma referência para as seguintes políticas de gestão de API. Para obter informações sobre adicionar e configurar as políticas, consulte [políticas na API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a>Entre as políticas de domínio  
  
-   [Permitir chamadas entre domínios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -faz com que a API acessível a partir de clientes baseada no browser Adobe Flash e o Microsoft Silverlight.  
  
-   [CORS](api-management-cross-domain-policies.md#CORS) -adiciona os recursos de várias origens (CORS) com o suporte para uma operação ou uma API para permitir que as chamadas entre domínios de clientes baseados no browser de partilha.  
  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) -adiciona JSON com o suporte do preenchimento (JSONP) para uma operação ou uma API para permitir que as chamadas entre domínios de clientes baseada no browser do JavaScript.  
  
##  <a name="AllowCrossDomainCalls"></a>Permitir chamadas entre domínios  
 Utilize o `cross-domain` política para tornar a API acessível a partir de clientes baseada no browser Adobe Flash e o Microsoft Silverlight.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|entre domínios|Elemento raiz. Elementos subordinados devem estar em conformidade com a [especificação de ficheiro de política entre domínios Adobe](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Sim|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** global  
  
##  <a name="CORS"></a>CORS  
 O `cors` política adiciona os recursos de várias origens (CORS) com o suporte para uma operação ou uma API para permitir que as chamadas entre domínios de clientes baseados no browser de partilha.  
  
 CORS permite um browser e um servidor interagir e determinar se deve ou não permitir pedidos de várias origens específicos (ou seja, chamadas de XMLHttpRequests efetuadas a partir de JavaScript numa página web para outros domínios). Isto permite uma maior flexibilidade ao permitir apenas pedidos do mesmo origem, mas é mais seguro do que permitir que todos os pedidos de várias origens.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<cors allow-credentials="false|true">  
    <allowed-origins>  
        <origin>origin uri</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="number of seconds">  
        <method>http verb</method>  
    </allowed-methods>  
    <allowed-headers>  
        <header>header name</header>  
    </allowed-headers>  
    <expose-headers>  
        <header>header name</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="example"></a>Exemplo  
 Este exemplo demonstra como suportar pedidos de pré-voo, como os com cabeçalhos personalizados ou métodos diferentes de GET e POST. Para suportar cabeçalhos personalizados e verbos HTTP adicionais, utilize o `allowed-methods` e `allowed-headers` secções conforme mostrado no exemplo seguinte.  
  
```xml  
<cors allow-credentials="true">  
    <allowed-origins>  
        <!-- Localhost useful for development -->  
        <origin>http://localhost:8080/</origin>  
        <origin>http://example.com/</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="300">  
        <method>GET</method>  
        <method>POST</method>  
        <method>PATCH</method>  
        <method>DELETE</method>  
    </allowed-methods>  
    <allowed-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
        <header>x-zumo-version</header>  
        <header>x-zumo-auth</header>  
        <header>content-type</header>  
        <header>accept</header>  
    </allowed-headers>  
    <expose-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|cors|Elemento raiz.|Sim|N/D|  
|origens permitidas|Contém `origin` elementos que descrevem as origens permitidas para pedidos entre domínios. `allowed-origins`pode conter qualquer um único `origin` elemento que especifica `*` para permitir que qualquer origem, ou um ou mais `origin` elementos que contêm um URI.|Sim|N/D|  
|origem|O valor pode ser `*` para permitir que todas as origens ou um URI que especifica uma única origem. O URI tem de incluir um esquema, anfitrião e porta.|Sim|Se a porta for omitida num URI, é utilizada a porta 80 para HTTP e é utilizada a porta 443 para HTTPS.|  
|métodos permitido|Este elemento é necessário se métodos diferente de obter ou POST são permitidos. Contém `method` elementos que especifique os verbos HTTP suportados.|Não|Se esta secção não estiver presente, são suportadas GET e POST.|  
|Método|Especifica um verbo de HTTP.|Pelo menos um `method` elemento é necessário se o `allowed-methods` secção está presente.|N/D|  
|cabeçalhos permitido|Este elemento contém `header` elementos especificando os nomes dos cabeçalhos que podem ser incluídos no pedido.|Não|N/D|  
|expõe cabeçalhos|Este elemento contém `header` elementos especificando os nomes dos cabeçalhos que estarão acessíveis pelo cliente.|Não|N/D|  
|cabeçalho|Especifica um nome de cabeçalho.|Pelo menos um `header` elemento não é necessária no `allowed-headers` ou `expose-headers` se a secção está presente.|N/D|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|permitir que as credenciais|O `Access-Control-Allow-Credentials` cabeçalho na resposta prévia será definida para o valor deste atributo e afeta a capacidade do cliente para submeter credenciais nos pedidos entre domínios.|Não|FALSO|  
|verificação prévia-resultado--idade máxima|O `Access-Control-Max-Age` cabeçalho na resposta prévia será definida para o valor deste atributo e afeta a capacidade do agente de utilizador de resposta de pré-voo da cache.|Não|0|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** entrada  
  
-   **Âmbitos de política:** API, operação  
  
##  <a name="JSONP"></a>JSONP  
 O `jsonp` política adiciona JSON com o suporte de preenchimento (JSONP) a uma operação ou uma API para permitir que as chamadas entre domínios de clientes baseada no browser do JavaScript. JSONP é um método utilizado em programas JavaScript para dados de pedido de um servidor num domínio diferente. JSONP ignora a limitação imposta pela maioria dos browsers onde o acesso a páginas web tem de estar no mesmo domínio.  
  
### <a name="policy-statement"></a>Declaração de política  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Exemplo  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Se chamar o método sem o parâmetro de chamada de retorno? cb = XXX devolverá JSON simples (sem um dispositivo de moldagem de chamada de função).  
  
 Se adicionar o parâmetro de chamada de retorno `?cb=XXX` irá devolver um resultado JSONP, original JSON de encapsulamento de aplicações, como os resultados à volta a função de chamada de retorno`XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Elementos  
  
|Nome|Descrição|Necessário|  
|----------|-----------------|--------------|  
|jsonp|Elemento raiz.|Sim|  
  
### <a name="attributes"></a>Atributos  
  
|Nome|Descrição|Necessário|Predefinição|  
|----------|-----------------|--------------|-------------|  
|nome de parâmetro de chamada de retorno|A chamada de função de JavaScript entre domínios prefixo com o nome de domínio completamente qualificado onde reside a função.|Sim|N/D|  
  
### <a name="usage"></a>Utilização  
 Esta política pode ser utilizada na política de seguinte [secções](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) e [âmbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **As secções de política:** saída  
  
-   **Âmbitos de política:** global, produto, API, operação  
  
## <a name="next-steps"></a>Passos seguintes
Para obter mais informações para trabalhar com as políticas, consulte [políticas na API Management](api-management-howto-policies.md).  
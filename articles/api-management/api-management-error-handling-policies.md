---
title: "Erro ao processar o nas políticas de API Management do Azure | Microsoft Docs"
description: "Saiba como responder a condições de erro que possam ocorrer durante o processamento de pedidos na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: e8837b8d62bb8caeee9460661438368c2d11697a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="error-handling-in-api-management-policies"></a>Erro ao processar o nas políticas de gestão de API
Gestão de API do Azure permite editores responder a condições de erro que possam ocorrer durante o processamento de pedidos para o proxy, fornecendo um `ProxyError` objeto. O `ProxyError` objeto é acedido através do [contexto. LastError](api-management-policy-expressions.md#ContextVariables) propriedade e pode ser utilizado pelas políticas no `on-error` secção política. Este tópico fornece uma referência para o erro capacidades de processamento na API Management do Azure.  
  
## <a name="error-handling-in-api-management"></a>Erro ao processar na API Management  
 As políticas de API Management do Azure são divididas em `inbound`, `backend`, `outbound`, e `on-error` secções conforme mostrado no exemplo seguinte.  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
 Durante o processamento de um pedido, são executados passos incorporados juntamente com quaisquer políticas que se encontrem no âmbito do pedido. Se ocorrer um erro, processamento imediatamente passar para o `on-error` secção política. O `on-error` secção política pode ser utilizada em qualquer âmbito e publicadores de API podem configurar o comportamento personalizado, tais como o registo de erros para os event hubs ou criar uma nova resposta para voltar para o autor da chamada.  
  
> [!NOTE]
>  O `on-error` secção não está presente nas políticas por predefinição. Para adicionar o `on-error` secção a uma política, navegue para a política pretendida no editor de políticas e adicioná-lo. Para obter mais informações sobre como configurar políticas, consulte [políticas na API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Se não houver nenhuma `on-error` secção, os chamadores irão receber mensagens de resposta HTTP 400 ou 500 se ocorrer uma condição de erro.  
  
### <a name="policies-allowed-in-on-error"></a>Políticas permitidas no erro  
 As seguintes políticas podem ser utilizadas no `on-error` secção política.  
  
-   [Escolha](api-management-advanced-policies.md#choose)  
  
-   [Definir variável](api-management-advanced-policies.md#set-variable)  
  
-   [localizar e substituir](api-management-transformation-policies.md#Findandreplacestringinbody)  
  
-   [resposta de retorno](api-management-advanced-policies.md#ReturnResponse)  
  
-   [cabeçalho de conjunto](api-management-transformation-policies.md#SetHTTPheader)  
  
-   [método de conjunto](api-management-advanced-policies.md#SetRequestMethod)  
  
-   [Definir estado](api-management-advanced-policies.md#SetStatus)  
  
-   [pedido de envio](api-management-advanced-policies.md#SendRequest)  
  
-   [enviar-uma forma de pedido](api-management-advanced-policies.md#SendOneWayRequest)  
  
-   [registo para eventhub](api-management-advanced-policies.md#log-to-eventhub)  
  
-   [JSON para xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
  
-   [XML para json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError  
 Quando ocorre um erro e controlo de passar para o `on-error` secção de política, o erro é armazenado no [contexto. LastError](api-management-policy-expressions.md#ContextVariables) propriedade que pode ser acedida por políticas no `on-error` secção e tem as seguintes propriedades.  
  
|Nome|Tipo|Descrição|Necessário|  
|----------|----------|-----------------|--------------|  
|Origem|Cadeia|Os nomes de elemento onde ocorreu o erro. Pode ser política ou um nome de passo de pipeline incorporada.|Sim|  
|Razão|Cadeia|Código de erro de máquina amigável, que foi utilizado no processamento de erros.|Não|  
|Mensagem|Cadeia|Descrição do erro legível por humanos.|Sim|  
|Âmbito|Cadeia|Nome do âmbito em que ocorreu o erro e pode ser uma das "global", "product", "api" ou "operação"|Não|  
|Section|Cadeia|Nome de secção onde ocorreu o erro e foi uma das "de entrada", "backend", "saída" ou "no error".|Não|  
|Caminho|Cadeia|Especifica a política aninhada, por exemplo, "Escolha [3] / quando [2]".|Não|  
|PolicyId|Cadeia|O valor da `id` atributo, se for especificado pelo cliente, na política de onde ocorreu o erro|Não|  
  
> [!NOTE]
>  Todas as políticas têm opcional `id` atributo que pode ser adicionado ao elemento raiz da política. Se este atributo estiver presente numa política quando ocorre uma condição de erro, o valor do atributo pode ser obtido utilizando o `context.LastError.PolicyId` propriedade.  
  
## <a name="predefined-errors-for-built-in-steps"></a>Erros predefinidos para passos incorporados  
 Os seguintes erros estão predefinidos para condições de erro que podem ocorrer durante a avaliação dos passos de processamento incorporada.  
  
|Origem|Condição|Razão|Mensagem|  
|------------|---------------|------------|-------------|  
|configuração|URI não corresponde a qualquer Api ou a operação|OperationNotFound|Não é possível comparar o pedido de entrada para uma operação.|  
|Autorização|Chave de subscrição não foi fornecido|SubscriptionKeyNotFound|Acesso negado devido à falta de chave de subscrição. Certifique-se de que inclui a chave de subscrição quando efetuar pedidos para esta API.|  
|Autorização|Valor de chave de subscrição é inválida|SubscriptionKeyInvalid|Acesso negado devido a chave de subscrição inválido. Certifique-se fornecer uma chave válida para uma subscrição ativa.|  
  
## <a name="predefined-errors-for-policies"></a>Erros predefinidos para as políticas  
 Os seguintes erros estão predefinidos para condições de erro que podem ocorrer durante a avaliação da política.  
  
|Origem|Condição|Razão|Mensagem|  
|------------|---------------|------------|-------------|  
|limite de taxa|Excedida o limite de velocidade|RateLimitExceeded|O limite de velocidade for excedido|  
|quota|Quota foi excedida|QuotaExceeded|Volume de chamadas fora da quota. Quota será replenished no xx:xx:xx. - ou - de quota de largura de banda. Quota será replenished no xx:xx:xx.|  
|jsonp|O valor de parâmetro de chamada de retorno é inválido (contém carateres errados)|CallbackParameterInvalid|Valor do parâmetro de chamada de retorno {nome de parâmetro de chamada de retorno} não é um identificador de JavaScript válido.|  
|filtro de IP|Falha ao analisar o IP de autor da chamada de pedido|FailedToParseCallerIP|Falha ao estabelecer o endereço IP para o chamador. Acesso negado.|  
|filtro de IP|Autor da chamada IP não está na lista de permitidos|CallerIpNotAllowed|Endereço IP do autor da chamada {ip address} não é permitido. Acesso negado.|  
|filtro de IP|Autor da chamada IP está na lista bloqueada|CallerIpBlocked|Endereço IP do emissor está bloqueado. Acesso negado.|  
|cabeçalho de verificação|Ou valor de cabeçalho necessário não está em falta|HeaderNotFound|Cabeçalho {nome do cabeçalho} não foi encontrado no pedido. Acesso negado.|  
|cabeçalho de verificação|Ou valor de cabeçalho necessário não está em falta|HeaderValueNotAllowed|Valor de cabeçalho {nome do cabeçalho} de {valor do cabeçalho} não é permitido. Acesso negado.|  
|jwt validar|Jwt token está em falta no pedido|TokenNotFound|JWT não encontrado no pedido. Acesso negado.|  
|jwt validar|Falha na validação de assinatura|TokenSignatureInvalid|< Mensagem da biblioteca de jwt\>. Acesso negado.|  
|jwt validar|Audiência inválida|TokenAudienceNotAllowed|< Mensagem da biblioteca de jwt\>. Acesso negado.|  
|jwt validar|Emissor inválido|TokenIssuerNotAllowed|< Mensagem da biblioteca de jwt\>. Acesso negado.|  
|jwt validar|Token expirou|TokenExpired|< Mensagem da biblioteca de jwt\>. Acesso negado.|  
|jwt validar|Chave de assinatura não foi resolvido pelo id|TokenSignatureKeyNotFound|< Mensagem da biblioteca de jwt\>. Acesso negado.|  
|jwt validar|Afirmações necessárias estão em falta no token|TokenClaimNotFound|JWT token está em falta as seguintes afirmações: < c1\>, < c2\>,... Acesso negado.|  
|jwt validar|Erro de correspondência de valores de afirmação|TokenClaimValueNotAllowed|Valor de afirmação {nome da afirmação} da {valor de afirmação} não é permitida. Acesso negado.|  
|jwt validar|Outras falhas de validação|JwtInvalid|< Mensagem da biblioteca de jwt\>|

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações para trabalhar com as políticas, consulte [políticas na API Management](api-management-howto-policies.md).  
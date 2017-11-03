---
title: "Políticas de API Management do Azure | Microsoft Docs"
description: "Saiba mais sobre as políticas disponíveis para utilização na API Management do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 1cc460cb-8e67-41aa-bc76-bbafc1892798
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 89d76c5d00f8a438cfec7fd1568d4735f0a65327
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-policies"></a>Políticas de API Management
Esta secção fornece uma referência para as seguintes políticas de gestão de API. Para obter informações sobre adicionar e configurar as políticas, consulte [políticas na API Management](api-management-howto-policies.md).  
  
 As políticas são uma funcionalidade poderosa do sistema que permite ao publicador alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou resposta de uma API. As instruções populares incluem a conversão do formato de XML para JSON e limitação para restringir a quantidade de chamadas recebidas de um programador de taxa de chamadas. Muitas mais políticas estão disponíveis a box.  
  
 As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo](api-management-advanced-policies.md#choose) e [Definir variável](api-management-advanced-policies.md#set-variable), baseiam-se em expressões de política. Para obter mais informações, consulte [políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies) e [expressões de política](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a>Políticas  
  
-   [Políticas de restrição de acesso](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
  
    -   [Cabeçalho HTTP de verificação](api-management-access-restriction-policies.md#CheckHTTPHeader) -impõe a existência de e/ou valor de um cabeçalho de HTTP.  
  
    -   [Limitar taxa de chamadas por subscrição](api-management-access-restriction-policies.md#LimitCallRate) -picos de utilização da API impede ao limitar taxa de chamadas, numa base por subscrição.  
  
    -   [Limitar taxa de chamadas por chave](api-management-access-restriction-policies.md#LimitCallRateByKey) -picos de utilização da API impede ao limitar taxa de chamadas, numa base por chave.  
  
    -   [Restringir o chamador IPs](api-management-access-restriction-policies.md#RestrictCallerIPs) -chamadas de filtros (permite/nega) provenientes de endereços IP específicos e/ou intervalos de endereços.  
  
    -   [Definir quota de utilização por subscrição](api-management-access-restriction-policies.md#SetUsageQuota) -permite-lhe impor uma renováveis duração da chamada de volume e/ou de largura de banda de quota ou, numa base por subscrição.  
  
    -   [Definir quota de utilização por chave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) -permite-lhe impor uma renováveis duração da chamada de volume e/ou de largura de banda de quota ou, numa base por chave.  
  
    -   [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) -impõe existência e validade de um JWT extraído de um cabeçalho de HTTP especificado ou um parâmetro de consulta especificada.  
  
-   [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies)  
  
    -   [Controlar o fluxo](api-management-advanced-policies.md#choose) - condicionalmente aplica-se as declarações de política com base na avaliação de expressões.  
  
    -   [Reencaminhar pedido](api-management-advanced-policies.md#ForwardRequest) -reencaminha o pedido para o serviço de back-end.  
  
    -   [Registo para o Hub de eventos](api-management-advanced-policies.md#log-to-eventhub) -envia mensagens no formato especificado para um destino de mensagem definido por uma entidade de registo.  
  
    -   [Repita](api-management-advanced-policies.md#Retry) -repete as tentativas de execução de declarações de política incluídos, se e até que a condição for satisfeita. Execução irá repetir a intervalos de tempo especificado e contagem de tentativas até especificado.  
  
    -   [Devolver a resposta](api-management-advanced-policies.md#ReturnResponse) -execução de pipeline de cancelamentos e devolve a resposta especificada diretamente para o autor da chamada.  
  
    -   [Enviar pedido unidirecional](api-management-advanced-policies.md#SendOneWayRequest) -envia um pedido para o URL especificado sem aguardar uma resposta.  
  
    -   [Enviar pedido](api-management-advanced-policies.md#SendRequest) -envia um pedido para o URL especificado.  
  
    -   [Definir variável](api-management-advanced-policies.md#set-variable) -manter um valor de uma variável de contexto nomeado para acesso posterior.  
  
    -   [Definir o método de pedido](api-management-advanced-policies.md#SetRequestMethod) -permite-lhe alterar o método HTTP para um pedido.  
  
    -   [Definir o código de estado](api-management-advanced-policies.md#SetStatus) -altera o código de estado HTTP para o valor especificado.  
  
    -   [Rastreio](api-management-advanced-policies.md#Trace) -adiciona uma cadeia para o [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/) saída.  
  
    -   [Aguarde](api-management-advanced-policies.md#Wait) -aguarda para colocado [pedido de envio](api-management-advanced-policies.md#SendRequest), [obter o valor da cache](api-management-caching-policies.md#GetFromCacheByKey), ou [controlar o fluxo](api-management-advanced-policies.md#choose) políticas seja concluída antes de continuar.  
  
-   [Políticas de autenticação](api-management-authentication-policies.md#AuthenticationPolicies)  
  
    -   [Autenticar com Basic](api-management-authentication-policies.md#Basic) -autenticar com um serviço de back-end utilizando a autenticação básica.  
  
    -   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) -autenticar com um serviço de back-end utilizando certificados de cliente.  
  
-   [Caching policies (Políticas de colocação em cache)](api-management-caching-policies.md#CachingPolicies)  
  
    -   [Obter a partir da cache](api-management-caching-policies.md#GetFromCache) -efetuar cache procurar e devolver uma resposta em cache válida quando disponível.  
  
    -   [Arquivo de cache](api-management-caching-policies.md#StoreToCache) -coloca em cache de resposta de acordo com a configuração de controlo de cache especificado.  
  
    -   [Obter o valor da cache](api-management-caching-policies.md#GetFromCacheByKey) -obter um item em cache por chave.  
  
    -   [Armazenar o valor na cache](api-management-caching-policies.md#StoreToCacheByKey) -armazenar um item na cache por chave.  
  
    -   [Remova o valor da cache](api-management-caching-policies.md#RemoveCacheByKey) -remover um item na cache por chave.  
  
-   [Políticas entre domínios](api-management-cross-domain-policies.md#CrossDomainPolicies)  
  
    -   [Permitir chamadas entre domínios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -faz com que a API acessível a partir de clientes baseada no browser Adobe Flash e o Microsoft Silverlight.  
  
    -   [CORS](api-management-cross-domain-policies.md#CORS) -adiciona os recursos de várias origens (CORS) com o suporte para uma operação ou uma API para permitir que as chamadas entre domínios de clientes baseados no browser de partilha.  
  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) -adiciona JSON com o suporte do preenchimento (JSONP) para uma operação ou uma API para permitir que as chamadas entre domínios de clientes baseada no browser do JavaScript.  
  
-   [Políticas de transformação](api-management-transformation-policies.md#TransformationPolicies)  
  
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
  
## <a name="next-steps"></a>Passos seguintes
Para obter mais informações para trabalhar com as políticas, consulte [políticas na API Management](api-management-howto-policies.md).  

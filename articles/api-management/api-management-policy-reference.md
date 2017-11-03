---
title: "Referência de política de gestão de API do Azure"
description: "Saiba mais sobre as políticas disponíveis para configurar a gestão de API."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: adc0c4415e10ddd0b4994cecef17f026546e91a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-policy-reference"></a>Referência de política de gestão de API do Azure
Esta secção fornece um índice para as políticas de [referência de política de gestão de API][API Management policy reference]. Para obter informações sobre adicionar e configurar as políticas, consulte [políticas na API Management][Policies in API Management].

As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como o [controlar o fluxo] [ Control flow] e [definir variável] [ Set variable] políticas baseadas em expressões de política. Para obter mais informações, consulte [políticas avançadas] [ Advanced policies] e [expressões de política][Policy expressions]

## <a name="policy-reference-index"></a>Índice de referência de política
* [Políticas de restrição de acesso][Access restriction policies]
  * [Cabeçalho HTTP de verificação] [ Check HTTP header] -impõe a existência de e/ou valor de um cabeçalho de HTTP.
  * [Limitar taxa de chamadas por subscrição] [ Limit call rate by subscription] -picos de utilização da API impede ao limitar taxa de chamadas, numa base por subscrição.
  * [Limitar taxa de chamadas por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) -picos de utilização da API impede ao limitar taxa de chamadas, numa base por chave.
  * [Restringir o chamador IPs] [ Restrict caller IPs] -chamadas de filtros (permite/nega) provenientes de endereços IP específicos e/ou intervalos de endereços.
  * [Definir quota de utilização por subscrição] [ Set usage quota by subscription] -permite-lhe impor uma renováveis duração da chamada de volume e/ou de largura de banda de quota ou, numa base por subscrição.
  * [Definir quota de utilização por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) -permite-lhe impor uma renováveis duração da chamada de volume e/ou de largura de banda de quota ou, numa base por chave.
  * [Validar JWT] [ Validate JWT] -impõe existência e validade de um JWT extraído de um cabeçalho de HTTP especificado ou um parâmetro de consulta especificada.
* [Políticas Avançadas][Advanced policies]
  * [Controlar o fluxo] [ Control flow] - condicionalmente aplica-se as declarações de política baseadas nos resultados da avaliação de Boolean [expressões][expressions].
  * [Reencaminhar pedido] [ Forward request] -reencaminha o pedido para o serviço de back-end.
  * [Registo para o Hub de eventos] [ Log to Event Hub] -envia mensagens no formato especificado para um destino de mensagem definido por um [registador](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) entidade.
  * [Repita](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) -repete as tentativas de execução de declarações de política incluídos, se e até que a condição for satisfeita. Execução irá repetir a intervalos de tempo especificado e contagem de tentativas até especificado.
  * [Devolver a resposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) -execução de pipeline de cancelamentos e devolve a resposta especificada diretamente para o autor da chamada.
  * [Enviar pedido unidirecional](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) -envia um pedido para o URL especificado sem aguardar uma resposta.
  * [Enviar pedido](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) -envia um pedido para o URL especificado.
  * [Definir o método de pedido](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) -permite-lhe alterar o método HTTP para um pedido.
  * [Definir o estado](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) -altera o código de estado HTTP para o valor especificado.
  * [Definir variável] [ Set variable] -manter um valor com um nome na [contexto] [ context] variável para acesso posterior.
  * [Rastreio](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) -adiciona uma cadeia para o [API Inspector](api-management-howto-api-inspector.md) saída.
  * [Aguarde](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) -aguarda para envio incluído, obter valor da cache, ou um pedido de controlar políticas de fluxo seja concluída antes de continuar.
* [Políticas de autenticação][Authentication policies]
  * [Autenticar com Basic] [ Authenticate with Basic] -autenticar com um serviço de back-end utilizando a autenticação básica.
  * [Autenticar com o certificado de cliente] [ Authenticate with client certificate] -autenticar com um serviço de back-end utilizando certificados de cliente.
* [Políticas de colocação em cache][Caching policies] 
  * [Obter a partir da cache] [ Get from cache] -efetuar cache procurar e devolver uma resposta em cache válida quando disponível.
  * [Arquivo de cache] [ Store to cache] -coloca em cache de resposta de acordo com a configuração de controlo de cache especificado.
  * [Obter o valor da cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) -obter um item em cache por chave.
  * [Armazenar o valor na cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) -armazenar um item na cache por chave.
  * [Remova o valor da cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) -remover um item na cache por chave.
* [Entre as políticas de domínio][Cross domain policies] 
  * [Permitir chamadas entre domínios] [ Allow cross-domain calls] -faz com que a API acessível a partir de clientes baseada no browser Adobe Flash e o Microsoft Silverlight.
  * [CORS] [ CORS] -adiciona os recursos de várias origens (CORS) com o suporte para uma operação ou uma API para permitir que as chamadas entre domínios de clientes baseados no browser de partilha.
  * [JSONP] [ JSONP] -adiciona JSON com o suporte do preenchimento (JSONP) para uma operação ou uma API para permitir que as chamadas entre domínios de clientes baseada no browser do JavaScript.
* [Políticas de transformação][Transformation policies] 
  * [Converter o JSON em XML] [ Convert JSON to XML] - converte ou um pedido de corpo de resposta JSON em XML.
  * [Converter XML para JSON] [ Convert XML to JSON] - converte ou um pedido de corpo de resposta a partir de XML para JSON.
  * [Localizar e substituir a cadeia no corpo] [ Find and replace string in body] - localiza uma subcadeia de pedido ou resposta e substitui-lo com uma subcadeia diferentes.
  * [Mascarar URLs no conteúdo] [ Mask URLs in content] -ligações reescreve (máscaras) na resposta body para que possam apontam para a ligação equivalente através do gateway.
  * [Definir o serviço de back-end] [ Set backend service] -o serviço de back-end para um pedido recebido é alterado.
  * [Definir o corpo] [ Set body] -define o corpo da mensagem para pedidos de enviados e receção.
  * [Cabeçalho de HTTP de conjunto] [ Set HTTP header] - atribui um valor para uma resposta existente e/ou o cabeçalho do pedido ou adiciona um cabeçalho de resposta e/ou pedido de novo.
  * [Defina o parâmetro de cadeia de consulta] [ Set query string parameter] - adiciona, substitui o valor de ou elimina o parâmetro de cadeia de consulta do pedido.
  * [URL de regravação] [ Rewrite URL] -converte um URL de pedido do respetivo formulário público para o formato esperado pelo serviço web.

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre as expressões de política, veja o vídeo seguinte.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx



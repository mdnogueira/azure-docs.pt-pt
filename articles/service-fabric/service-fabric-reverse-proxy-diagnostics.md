---
title: "Azure Service Fabric inverso diagnóstico proxy | Microsoft Docs"
description: Saiba como monitorizar e diagnosticar o processamento de pedidos no proxy inverso.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: 1c62d2390709577bfde6225b783642fb55396a6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorizar e diagnosticar o processamento de pedidos no proxy inverso

Começando com a versão 5.7 do Service Fabric, estão disponíveis para a recolha de eventos de proxy inverso. Os eventos estão disponíveis dois canais, uma com só erro eventos relacionados com a falha de processamento de pedidos no proxy inverso e segundo canal que contém eventos verbosos com entradas de pedidos com êxito ou falhadas.

Consulte [recolher eventos de proxy inverso](service-fabric-diagnostics-event-aggregation-wad.md#collect-reverse-proxy-events) para ativar a recolha de eventos destes canais no local e clusters de Service Fabric do Azure.

## <a name="troubleshoot-using-diagnostics-logs"></a>Resolver problemas com os registos de diagnóstico
Seguem-se alguns exemplos sobre como interpretar os registos comuns de falha que uma pode encontrar:

1. Proxy inverso devolve o código de estado de resposta 504 (tempo limite).

    Uma razão dever-se a falha do serviço em responder dentro do período de tempo limite de pedido.
O primeiro evento abaixo registos os detalhes do pedido foi recebido no proxy inverso. O segundo evento indica que o pedido falhou ao reencaminhamento ao serviço, devido a "Erro interno = ERROR_WINHTTP_TIMEOUT" 

    O payload inclui:

    *  **traceId**: GUID este pode ser utilizado para correlacionar todos os eventos correspondente a um único pedido. No abaixo dois eventos, o traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**, implying pertencem ao mesmo pedido.
    *  **requestUrl**: URL (URL de proxy inverso) ao qual o pedido foi enviado.
    *  **verbo**: verbo HTTP.
    *  **remoteAddress**: endereço do cliente a enviar o pedido.
    *  **resolvedServiceUrl**: serviço URL de ponto final ao qual o pedido de entrada foi resolvido. 
    *  **errorDetails**: informações adicionais sobre a falha.

    ```
    {
      "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
      "ProviderName": "Microsoft-ServiceFabric",
      "Id": 51477,
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
      "ProcessId": 57696,
      "Level": "Informational",
      "Keywords": "0x1000000000000021",
      "EventName": "ReverseProxy",
      "ActivityID": null,
      "RelatedActivityID": null,
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
        "verb": "GET",
        "remoteAddress": "::1",
        "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
        "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
      }
    }

    {
      "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
      ...
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
      ...
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "statusCode": 504,
        "description": "Reverse Proxy Timeout",
        "sendRequestPhase": "FinishSendRequest",
        "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
      }
    }
    ```

2. Proxy inverso devolve o código de estado de resposta 404 (não for encontrado). 
    
    Segue-se um evento de exemplo em que o proxy inverso devolve 404, uma vez que não foi possível localizar o ponto final de serviço correspondentes.
    As entradas de payload de interesse aqui são:
    *  **processRequestPhase**: indica a fase de durante o processamento de pedido quando a falha ocorreu, ***TryGetEndpoint*** revertidos ao tentar obter um ponto final do serviço para reencaminhar para. 
    *  **errorDetails**: apresenta os critérios de pesquisa de ponto final. Aqui pode ver que o listenerName especificado = **FrontEndListener** enquanto a lista de ponto final de réplica contém apenas um serviço de escuta com o nome **OldListener**.
    
    ```
    {
      ...
      "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
      "ProcessId": 57696,
      "Level": "Warning",
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
        ...
        "processRequestPhase": "TryGetEndoint",
        "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
      }
    }
    ```
    Outro exemplo em que o proxy inverso pode devolver 404 não é encontrado: parâmetro de configuração ApplicationGateway\Http **SecureOnlyMode** está definido como verdadeiro com o proxy reverso a escutar **HTTPS**; no entanto, todos os pontos finais de réplica são não seguros (escuta de HTTP).
    Inverte devolve proxy 404, uma vez que não é possível localizar um ponto final à escuta HTTPS para reencaminhar o pedido. Analisar os parâmetros no caso de payload ajuda a restringir o problema:
    
    ```
        "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
    ```

3. Pedido para o proxy reverso falha com um erro de tempo limite. 
    Os registos de eventos contêm um evento com os detalhes do pedido recebido (não apresentados aqui).
    O evento seguinte mostra que o serviço respondeu com um código de 404 estado e proxy inverso inicia resolver novamente. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Aquando da recolha de todos os eventos, verá uma formação de eventos que mostra cada resolve e a tentativa de pesquisa direta.
    O último evento na série mostra que o processamento do pedido falhou com um tempo limite, juntamente com o número de tentativas de resolver com êxito.
    
    > [!NOTE]
    > É recomendado para manter a recolha de eventos de canal verboso desativada por predefinição e ativá-la para resolução de problemas de forma necessário.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Se a coleção está ativada para eventos de erro crítico/apenas, verá um evento com detalhes sobre o tempo limite e o número de tentativas de resolver. 
    
    Os serviços que pretendem enviar um código de 404 estado novamente para o utilizador, deve adicionar um cabeçalho de "X ServiceFabric" na resposta. Depois do cabeçalho é adicionado à resposta, o proxy inverso reencaminha o código de estado para o cliente.  

4. Casos quando o cliente desligou-se o pedido.

    Seguinte evento é registado quando proxy inverso é o reencaminhamento a resposta ao cliente, mas o cliente desligar:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Proxy inverso devolve 404 FABRIC_E_SERVICE_DOES_NOT_EXIST

    Erro FABRIC_E_SERVICE_DOES_NOT_EXIST é devolvido se o esquema de URI não está especificado para o ponto final de serviço no manifesto de serviço.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Para resolver o problema, especifique o esquema URI no manifesto.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Eventos relacionados com o processamento de pedidos de websocket não são atualmente registados. Isto será adicionado na próxima versão.

## <a name="next-steps"></a>Passos seguintes
* [Agregação de eventos e coleção utilizando o Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) para ativar a recolha de registos em clusters do Azure.
* Para ver eventos de Service Fabric no Visual Studio, consulte [monitorizar e diagnosticar localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Consulte [Configurar proxy inverso para ligar aos serviços segurados](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) para o Azure Resource Manager amostras de modelo para configurar o secure proxy inverso com o certificado de serviço diferentes opções de validação.
* Leitura [Service Fabric proxy inverso](service-fabric-reverseproxy.md) para obter mais informações.

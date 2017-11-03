---
title: "Repetir lógica em que o SDK de Media Services para .NET | Microsoft Docs"
description: "O tópico fornece uma descrição geral da lógica de repetição no SDK de Media Services para .NET."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 859dd76db4ba06196a853469a1385703d835fa22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Repetir lógica em que o SDK de Media Services para .NET
Ao trabalhar com os serviços do Microsoft Azure, podem ocorrer falhas transitórias. Se ocorrer uma falha transitória, na maioria dos casos, após várias tentativas de alguns a operação é bem sucedida. O SDK de Media Services para .NET implementa a lógica de repetição para lidar com falhas transitórias associadas exceções e erros causados por pedidos web, execução de consultas, guardar as alterações e operações de armazenamento.  Por predefinição, o SDK de Media Services para .NET executa quatro tentativas antes de voltar a gerar a exceção à sua aplicação. O código na sua aplicação, em seguida, deve processar esta exceção corretamente.  

 Segue-se um breve orientação das políticas de pedido Web, armazenamento, consulta e SaveChanges:  

* A política de armazenamento é utilizada para operações de armazenamento de BLOBs (carregamentos ou transferência dos ficheiros de recurso).  
* A política de pedido Web é utilizada para pedidos web genérico (por exemplo, para obter um token de autenticação e a resolver o ponto final de cluster de utilizadores).  
* A política de consulta é utilizada para consultar entidades do REST (por exemplo, mediaContext.Assets.Where(...)).  
* A política de SaveChanges é utilizada para fazer tudo o que altera os dados no âmbito do serviço (por exemplo, criar uma entidade a atualizar uma entidade, chamar uma função de serviço para uma operação).  
  
  Este tópico lista os tipos de exceção e códigos de erro que são processados pelo SDK de Media Services para .NET repetir lógica.  

## <a name="exception-types"></a>Tipos de exceção
A tabela seguinte descreve as exceções que o SDK de Media Services para .NET processa ou não processa para algumas operações que podem causar falhas transitórias.  

| Exceção | Pedido Web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Para obter mais informações, consulte o [códigos de estado de WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) secção. |Sim |Sim |Sim |Sim |
| DataServiceClientException<br/> Para obter mais informações, consulte [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceQueryException<br/> Para obter mais informações, consulte [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceRequestException<br/> Para obter mais informações, consulte [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não |Sim |Sim |Sim |
| DataServiceTransportException |Não |Não |Sim |Sim |
| TimeoutException |Sim |Sim |Sim |Não |
| SocketException |Sim |Sim |Sim |Sim |
| StorageException |Não |Sim |Não |Não |
| IOException |Não |Sim |Não |Não |

### <a name="WebExceptionStatus"></a>Códigos de estado de WebException
A tabela seguinte mostra os códigos de erro WebException de lógica de repetição é implementada. O [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) enumeração define os códigos de estado.  

| Estado | Pedido Web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Sim |Sim |Sim |Sim |
| NameResolutionFailure |Sim |Sim |Sim |Sim |
| ProxyNameResolutionFailure |Sim |Sim |Sim |Sim |
| SendFailure |Sim |Sim |Sim |Sim |
| PipelineFailure |Sim |Sim |Sim |Não |
| ConnectionClosed |Sim |Sim |Sim |Não |
| KeepAliveFailure |Sim |Sim |Sim |Não |
| UnknownError |Sim |Sim |Sim |Não |
| ReceiveFailure |Sim |Sim |Sim |Não |
| RequestCanceled |Sim |Sim |Sim |Não |
| Tempo limite |Sim |Sim |Sim |Não |
| ProtocolError <br/>A repetição em ProtocolError é controlada pelo processamento de código de estado HTTP. Para obter mais informações, consulte [códigos de estado de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Sim |Sim |Sim |Sim |

### <a name="HTTPStatusCode"></a>Códigos de estado de erro HTTP
Quando as operações de consulta e SaveChanges throw DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, o código de estado de erro HTTP é devolvido na propriedade StatusCode.  A tabela seguinte mostra os códigos de erro de lógica de repetição é implementada.  

| Estado | Pedido Web | Armazenamento | Consulta | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Não |Sim |Não |Não |
| 403 |Não |Sim<br/>Tentativas com poucos tempo a processar. |Não |Não |
| 408 |Sim |Sim |Sim |Sim |
| 429 |Sim |Sim |Sim |Sim |
| 500 |Sim |Sim |Sim |Não |
| 502 |Sim |Sim |Sim |Não |
| 503 |Sim |Sim |Sim |Sim |
| 504 |Sim |Sim |Sim |Não |

Se pretender observe a implementação real do SDK de Media Services para a lógica de repetição de .NET, consulte [-sdk-para-media services do azure](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


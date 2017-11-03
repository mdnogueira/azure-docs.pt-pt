---
title: "Descrição geral do reencaminhamento do Azure APIs padrão do .NET | Microsoft Docs"
description: "Descrição geral de API padrão de .NET de reencaminhamento do Azure"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 58451bae409c74c319f41c38a1cec5f051619e0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Descrição geral de API padrão do reencaminhamento híbrido ligações .NET do Azure

Este artigo resume algumas das chaves do Azure reencaminhamento híbrido ligações .NET padrão [APIs cliente](/dotnet/api/microsoft.azure.relay).
  
## <a name="relay-connection-string-builder"></a>Construtor de cadeia de ligação de reencaminhamento

O [RelayConnectionStringBuilder] [ RelayConnectionStringBuilder] classe formatos cadeias de ligação que são específicas para as ligações híbridas de reencaminhamento. Pode utilizá-lo para verificar o formato de uma cadeia de ligação ou para criar uma cadeia de ligação a partir do zero. Consulte o seguinte código para obter um exemplo:

```csharp
var endpoint = "{Relay namespace}";
var entityPath = "{Name of the Hybrid Connection}";
var sharedAccessKeyName = "{SAS key name}";
var sharedAccessKey = "{SAS key value}";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Pode também passar uma cadeia de ligação diretamente para o `RelayConnectionStringBuilder` método. Esta operação permite-lhe verificar se a cadeia de ligação está num formato válido. Se qualquer um dos parâmetros são inválidos, o construtor gera um `ArgumentException`.

```csharp
var myConnectionString = "{RelayConnectionString}";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Fluxo de ligação híbrida
O [HybridConnectionStream] [ HCStream] classe é o objeto principal utilizado para enviar e receber dados de um ponto final de reencaminhamento do Azure, se estiver a trabalhar com um [HybridConnectionClient][HCClient], ou um [HybridConnectionListener][HCListener].

### <a name="getting-a-hybrid-connection-stream"></a>Obter um fluxo de ligação híbrida

#### <a name="listener"></a>Serviço de escuta
Utilizar um [HybridConnectionListener][HCListener], pode obter um `HybridConnectionStream` objeto da seguinte forma:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Cliente
Utilizar um [HybridConnectionClient][HCClient], pode obter um `HybridConnectionStream` objeto da seguinte forma:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>A receção de dados
O [HybridConnectionStream] [ HCStream] classe permite a comunicação bidirecional. Na maioria dos casos, receberá continuamente do fluxo. Se estiver a ler texto do fluxo, também poderá utilizar um [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) objeto, que permite o facilitar a análise dos dados. Por exemplo, pode ler dados como texto, em vez de como `byte[]`.

O seguinte código lê individuais linhas de texto da sequência até um cancelamento é pedido:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Envio de dados
Assim que tiver uma ligação estabelecida, pode enviar uma mensagem para o ponto final de reencaminhamento. Porque o objeto de ligação herda [fluxo](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), enviar os dados como um `byte[]`. O exemplo seguinte mostra como efetuar este procedimento:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

No entanto, se pretender enviar texto diretamente, sem necessidade de codificar a cadeia de cada vez, pode encapsular as `hybridConnectionStream` objeto com um [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) objeto.

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o reencaminhamento do Azure, visite estas ligações:

* [Referência de Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [APIs de reencaminhamento disponíveis](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener
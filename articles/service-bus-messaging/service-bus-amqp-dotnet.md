---
title: Service Bus com o .NET e AMQP 1.0 | Microsoft Docs
description: Utilizar o Service Bus do Azure a partir do .NET com AMQP
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 58a37c0dd24d54996f517961f3a7f1ec36639cfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-service-bus-from-net-with-amqp-10"></a>Utilização do Service Bus a partir do .NET com AMQP 1.0

## <a name="downloading-the-service-bus-sdk"></a>Transferir o SDK do Service Bus

Suporte de AMQP 1.0 está disponível o SDK do Service Bus versão 2.1 ou posterior. Pode garantir que dispõe da versão mais recente transferindo os bits do Service Bus do [NuGet][NuGet].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configurar aplicações de .NET para utilizar AMQP 1.0

Por predefinição, a biblioteca de cliente .NET do Service Bus comunica com o serviço do Service Bus utilizando um protocolo baseado em SOAP dedicado. Para utilizar AMQP 1.0, em vez do predefinido protocolo requer a configuração explícita na cadeia de ligação de barramento de serviço, conforme descrito na secção seguinte. Além desta alteração, o código da aplicação permanece inalterado quando utilizar AMQP 1.0.

Na versão atual, existem algumas funcionalidades de API que não são suportadas quando utilizar AMQP. Estas funcionalidades não suportadas são listadas mais tarde na secção [não suportado funcionalidades, restrições e diferenças comportamentais](#unsupported-features-restrictions-and-behavioral-differences). Algumas das definições de configuração avançada também tem um significado diferente quando utilizar AMQP.

### <a name="configuration-using-appconfig"></a>Configuração utilizando o App. config

É recomendável para as aplicações para utilizarem o ficheiro de configuração App. config para armazenar definições. Para aplicações de Service Bus, pode utilizar o App. config para armazenar a cadeia de ligação do Service Bus. Um exemplo de ficheiro App. config é o seguinte:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

O valor da `Microsoft.ServiceBus.ConnectionString` definição é a cadeia de ligação do Service Bus que é utilizada para configurar a ligação ao Service Bus. O formato é o seguinte:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Onde `namespace` e `SAS key` são obtidos a partir de [portal do Azure] [ Azure portal] quando cria um espaço de nomes do Service Bus. Para obter mais informações, consulte [criar um espaço de nomes do Service Bus com o portal do Azure][Create a Service Bus namespace using the Azure portal].

Quando utilizar AMQP, acrescentar a cadeia de ligação com `;TransportType=Amqp`. Este notation instrui a biblioteca de cliente para efetuar a ligação ao Service Bus através de AMQP 1.0.

## <a name="message-serialization"></a>Serialização da mensagem

Ao utilizar o protocolo predefinido, o comportamento de serialização predefinido da biblioteca de clientes .NET consiste em utilizar o [DataContractSerializer] [ DataContractSerializer] tipo serializar um [BrokeredMessage] [ BrokeredMessage] instância para o transporte entre a biblioteca de cliente e o serviço do Service Bus. Quando utilizar o modo de transporte AMQP, a biblioteca de cliente utiliza o sistema de tipo AMQP para a serialização do [mensagem mediada] [ BrokeredMessage] numa mensagem AMQP. Esta serialização permite que a mensagem a ser recebidos e interpretados por uma aplicação de receção potencialmente está em execução numa plataforma diferente, por exemplo, uma aplicação de Java que utiliza a API de JMS para aceder ao Service Bus.

Quando construir um [BrokeredMessage] [ BrokeredMessage] instância, pode fornecer um objeto de .NET como um parâmetro para o construtor para servir como corpo da mensagem. Para objetos que podem ser mapeados para tipos primitivos AMQP, o corpo é serializado para tipos de dados AMQP. Se o objeto não pode ser mapeado diretamente para um tipo primitivo AMQP; ou seja, um tipo personalizado é definido pela aplicação e o objeto está a ser serializado utilizando o [DataContractSerializer][DataContractSerializer], e são enviados bytes serializados numa mensagem de dados AMQP.

Para facilitar a interoperabilidade com clientes não .NET, utilize apenas os tipos de .NET que podem ser serializados diretamente nos tipos de AMQP para o corpo da mensagem. A tabela seguinte fornece detalhes sobre os tipos e o mapeamento para o sistema de tipo AMQP correspondente.

| Tipo de objeto de corpo de .NET | Tipo AMQP mapeada | Tipo de secção de corpo de AMQP |
| --- | --- | --- |
| bool |Valor booleano |Valor AMQP |
| Bytes |ubyte |Valor AMQP |
| ushort |ushort |Valor AMQP |
| UInt |UInt |Valor AMQP |
| ulong |ulong |Valor AMQP |
| SByte |Bytes |Valor AMQP |
| curto |curto |Valor AMQP |
| Int |Int |Valor AMQP |
| longa |longa |Valor AMQP |
| Número de vírgula flutuante |Número de vírgula flutuante |Valor AMQP |
| duplo |duplo |Valor AMQP |
| Decimal |decimal128 |Valor AMQP |
| char |char |Valor AMQP |
| DateTime |carimbo de data/hora |Valor AMQP |
| GUID |UUID |Valor AMQP |
| Byte] |Binário |Valor AMQP |
| Cadeia |Cadeia |Valor AMQP |
| Ser |lista |Valor AMQP: contidos na coleção de itens só podem ser aqueles que são definidos nesta tabela. |
| Array |Matriz |Valor AMQP: contidos na coleção de itens só podem ser aqueles que são definidos nesta tabela. |
| System.Collections.IDictionary |Mapa |Valor AMQP: contidos na coleção de itens só podem ser aqueles que são definidos nesta tabela. Nota: as chaves de cadeia só são suportadas. |
| URI |Descrito cadeia (consulte a tabela seguinte) |Valor AMQP |
| DateTimeOffset |Descrito longa (consulte a tabela seguinte) |Valor AMQP |
| TimeSpan |Descrito longa (consulte o seguinte) |Valor AMQP |
| Fluxo |Binário |Dados de AMQP (pode estar vários). As secções de dados contém os bytes não processados do objeto de sequência. |
| Outro objeto |Binário |Dados de AMQP (pode estar vários). Contém os binários serializados do objeto que utiliza o DataContractSerializer ou um serializador fornecida pela aplicação. |

| Tipo .NET | AMQP mapeada descrito tipo | Notas |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Funcionalidades não suportadas, restrições e diferenças comportamentais

As seguintes funcionalidades da API de .NET de barramento de serviço não são atualmente suportadas ao utilizar AMQP:

* Transações
* Enviar através de destino de transferência

Também existem algumas pequenas diferenças no comportamento da API .NET do Service Bus ao utilizar AMQP, em comparação comparada o protocolo predefinido:

* O [OperationTimeout] [ OperationTimeout] propriedade é ignorada.
* `MessageReceiver.Receive(TimeSpan.Zero)`é implementado como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* A concluir as mensagens por tokens de bloqueio só pode ser efetuada pelos recetores mensagem que inicialmente receberam as mensagens.

## <a name="controlling-amqp-protocol-settings"></a>Controlar as definições do protocolo AMQP

O [.NET APIs](/dotnet/api/) expõe várias definições para controlar o comportamento do protocolo AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: controla o crédito inicial aplicado a uma ligação. A predefinição é 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Abra o controla o tamanho máximo de moldura AMQP fornecido durante a negociação de ligação hora. A predefinição é 65,536 bytes.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: se as transferências são batchable, este valor determina o atraso máximo de envio dispositions. Herdado pelos remetentes/recetores por predefinição. Recetor/remetente individuais podem substituir o predefinido, que é 20 milissegundos.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: controla se as ligações AMQP são estabelecidas através de uma ligação SSL. A predefinição é **verdadeiro**.

## <a name="next-steps"></a>Passos seguintes

Está pronto para obter mais informações? Visite as seguintes ligações:

* [Descrição geral do Service Bus AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP no barramento de serviço para o Windows Server]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Descrição geral do Service Bus AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no barramento de serviço para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

<properties
    pageTitle="Descrição geral dos escalões de preços das Mensagens Standard e Premium do Service Bus | Microsoft Azure"
    description="Mensagens Standard e Premium do Service Bus"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/16/2016"
    ms.author="darosa;sethm"/>

# Escalões de mensagens Standard e Premium do Service Bus 

As mensagens mediadas do Service Bus que inclui entidades de mensagens como filas e tópicos, combina capacidades de mensagens empresariais com semântica de publicação- subscrição na escala da nuvem. As mensagens mediadas do Service Bus utilizam-se como infraestrutura de comunicação para várias soluções de nuvem sofisticadas.

O escalão *Premium* das mensagens do Service Bus atende pedidos de cliente comuns relativamente a escala, desempenho e disponibilidade de aplicações fundamentais para a missão. Embora os conjuntos de funcionalidades sejam praticamente idênticos, estes dois escalões de mensagens do Service Bus estão concebidos para utilizar-se em situações diferentes.

Na tabela abaixo, destacam-se algumas das principais diferenças.

| Premium                               | Standard                       |
|---------------------------------------|--------------------------------|
| Débito elevado                       | Débito variável            |
| Desempenho previsível               | Latência variável               |
| Preços previsíveis                   | Preços variáveis de utilização  |
| Possibilidade de aumentar verticalmente e horizontalmente a carga de trabalho | N/D                            |
| Tamanho da mensagem > 256 KB                  | O tamanho da mensagem é 256 KB          |

As **Mensagens Premium do Service Bus do Azure** fornecem isolamento de recursos na camada de CPU e memória para que cada carga de trabalho do cliente seja executada de forma isolada. Este contentor de recursos é designado por *unidade de mensagens*. A cada espaço de nomes premium é atribuído, pelo menos, uma unidade de mensagens. Pode comprar 1, 2 ou 4 unidades de mensagens para cada espaço de nomes Premium do Service Bus. Uma única carga de trabalho ou entidade pode abranger várias unidades de mensagens e o número de unidades de mensagens pode ser alterado à vontade, embora a faturação seja realizada com base em tarifas diárias ou de 24 horas. O resultado é um desempenho previsível e repetível da sua solução com base no Service Bus.

Este desempenho não só é mais previsível e repetível, como também mais rápido. As mensagens Premium do Service Bus baseiam-se no motor de armazenamento introduzido nos [Event Hubs do Azure](https://azure.microsoft.com/services/event-hubs/). Com as mensagens Premium, obter o máximo rendimento é muito mais rápido do que com o escalão Standard.

## Diferenças técnicas das Mensagens Premium

A seguir apresentam-se algumas diferenças entre as camadas de mensagens Standard e Premium.

### Entidades particionadas

As entidades particionadas são suportadas nas mensagens Premium, mas não funcionam da mesma forma que nos escalões de mensagens Standard e Basic do Service Bus. As mensagens Premium não utilizam SQL como armazém de dados e já não tem a possível concorrência de recursos associada a uma plataforma partilhada. Como resultado, não é necessário criar partições. Além disso, a quantidade de partições foi alterada de 16 partições nas mensagens Standard para duas partições nas Premium. Ter 2 partições garante a disponibilidade e é um número mais adequado para o ambiente de tempo de execução Premium. Para mais informações sobre a criação de partições, consulte o artigo [Entidades de Mensagens Particionadas](service-bus-partitioning.md).

### Entidades expressas

Uma vez que as mensagens Premium se executam num ambiente de tempo de execução completamente isolado, já não existe necessidade de entidades expressas. Por conseguinte, entidades expressas não são suportadas em espaços de nomes Premium. Para mais informações sobre a funcionalidade Express, consulte a propriedade [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx).

## Passos seguintes

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

- [Introdução às mensagens Premium do Service Bus do Azure (mensagem de blogue)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introdução às mensagens Premium do Service Bus do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Descrição geral de mensagens do Service Bus](service-bus-messaging-overview.md)
- [Descrição Geral da Arquitetura do Service Bus do Azure](service-bus-fundamentals-hybrid-solutions.md)
- [Como utilizar as filas do Service Bus ](service-bus-dotnet-how-to-use-queues.md)



<!--HONumber=Jun16_HO2-->



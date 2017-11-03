---
title: "O serviço de mensagens assíncrono do Service Bus | Microsoft Docs"
description: "Descrição do serviço de mensagens assíncrono do Service Bus do Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: d36360f3fb46adf96f53976584987590791b07d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Padrões de mensagens assíncronas e elevada disponibilidade

Serviço de mensagens assíncrono pode ser implementado numa variedade de formas diferentes. Com as filas, tópicos e subscrições, o Service Bus do Azure suporta asynchronism através de uma loja e o mecanismo de reencaminhar. Na operação (síncrona) normal, enviar mensagens de filas e tópicos e receber mensagens de filas e subscrições. As aplicações que escreve dependem estas entidades sempre que está a ser disponíveis. Quando altera o estado de funcionamento da entidade, devido a uma variedade de circunstâncias, terá de uma forma de fornecer uma entidade de capacidade reduzida que pode satisfazer a maioria das necessidades.

As aplicações, normalmente, utilizar padrões de mensagens assíncronas para ativar vários cenários de comunicação. Pode criar aplicações no qual os clientes possam enviar mensagens para serviços, mesmo quando o serviço não está em execução. Para aplicações essa experiência podem ajudar a bursts de comunicações, uma fila de nível da carga fornecendo um local para comunicações de memória intermédia. Por fim, pode obter um balanceador de carga simples, mas eficazes distribuir mensagens entre várias máquinas.

Para manter a disponibilidade de qualquer uma destas entidades, considere um número de diferentes formas em que estas entidades podem aparecer indisponíveis para um sistema de mensagens durável. Um modo geral, vemos a entidade ficam indisponíveis para aplicações, que iremos escrever das seguintes formas diferentes:

* Não é possível enviar mensagens.
* Não é possível receber mensagens.
* Não é possível gerir entidades (criar, obter, atualizar ou eliminar entidades).
* Não é possível contactar o serviço.

Para cada uma destas falhas, modos diferentes falha existem que permitem que uma aplicação continuar a executar o trabalho com alguns nível de capacidade reduzida. Por exemplo, um sistema que pode enviar mensagens, mas não recebê-las pode continuar a receber as ordens de clientes, mas não é possível processar as ordens. Este tópico aborda problemas potenciais que podem ocorrer, e como são atenuados desses problemas. Barramento de serviço foi introduzido um número de atenuações que tem a participar no, e este tópico descreve também as regras que rege a utilização desses mitigações de optar ativamente por participar no.

## <a name="reliability-in-service-bus"></a>Fiabilidade no Service Bus
Existem várias formas de lidar com problemas de mensagem e a entidade e existem diretrizes que rege a utilização adequada desses mitigações. Para compreender as diretrizes, tem primeiro de compreender o que pode efetuar no Service Bus. Devido a estrutura de sistemas do Azure, todos estes problemas tendem a ser curta duração. Um nível elevado, as causas diferentes de indisponibilidade aparecem da seguinte forma:

* Limitação de um sistema externo em que depende do Service Bus. Limitação ocorre de interações com os recursos de armazenamento e computação.
* Problema de um sistema de que depende de Service Bus. Por exemplo, uma determinada parte de armazenamento pode encontrar problemas.
* Falha do Service Bus no subsistema único. Nesta situação, um nó de computação pode obter num estado inconsistente e tem de reiniciar, fazendo com que todas as entidades serve para equilibrar a outros nós. Isto por sua vez, pode causar um curto período de tempo de processamento de mensagens lenta.
* Falha do Service Bus dentro de um datacenter do Azure. Esta é uma "Falha catastrófica" durante os quais o sistema está inacessível para o número de minutos ou algumas horas.

> [!NOTE]
> O termo **armazenamento** pode significar Storage do Azure e SQL Azure.
> 
> 

Barramento de serviço contém um número de mitigações para estes problemas. As secções seguintes abordam cada problema e os respetivas mitigações.

### <a name="throttling"></a>Limitação
Com o Service Bus, a limitação permite a gestão de taxa de mensagem cooperative. Cada nó de barramento de serviço individual aloja várias entidades. Cada uma dessas entidades torna exigências no sistema em termos de CPU, memória, armazenamento e outros aspetos. Quando qualquer uma destas facetas Deteta utilização que exceder os limiares definidos, Service Bus pode negar um pedido específico. O autor da chamada recebe um [ServerBusyException] [ ServerBusyException] e repete após 10 segundos.

Como uma mitigação, o código tem de ler o erro e pare qualquer tentativas da mensagem para, pelo menos, 10 segundos. Uma vez que o erro pode acontecer em partes da aplicação cliente, espera-se que cada peça executa independentemente a lógica de repetição. O código pode reduzir a probabilidade de que está a ser limitado, permitindo a criação de partições num fila ou tópico.

### <a name="issue-for-an-azure-dependency"></a>Problema de uma dependência do Azure
Outros componentes no Azure, ocasionalmente, podem ter problemas de serviço. Por exemplo, quando está a ser atualizado um sistema que utiliza o Service Bus, esse sistema temporariamente pode experimentar capacidades reduzidas. Para contornar estes tipos de problemas, o Service Bus regularmente investiga e implementa mitigações. Só aparecem efeitos secundários destas mitigações. Por exemplo, para lidar com problemas transitórios com o armazenamento, o Service Bus implementa um sistema permite operações de envio de mensagem trabalhar de forma consistente. Devido à natureza da mitigação, uma mensagem enviada pode demorar até 15 minutos a aparecer na fila afetada ou subscrição e estar pronto para uma operação de receção. Um modo geral, a maior parte das entidades não irá ocorrer este problema. No entanto, tendo em conta o número de entidades no Service Bus no Azure, este mitigação, por vezes, é necessário para um pequeno subconjunto de clientes do Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Falha de barramento de serviço de subsistema de um único
Com qualquer aplicação, circunstâncias podem fazer com que um componente interno do Service Bus para se tornar inconsistente. Quando o Service Bus Deteta isto, recolhe os dados da aplicação para ajudar a diagnosticar o que aconteceu. Depois dos dados são recolhidos, a aplicação é reiniciada numa tentativa de devolva-o num estado consistente. Este processo ocorre razoavelmente depressa e resultados numa entidade apresentação estar disponível para até alguns minutos, embora típico para baixo vezes são muito mais curtos.

Nestes casos, a aplicação cliente gera um [System.TimeoutException] [ System.TimeoutException] ou [MessagingException] [ MessagingException] exceção. Barramento de serviço contém uma mitigação para este problema sob a forma de lógica de repetição de clientes automatizada. Depois de se esgota o período e a mensagem não for entregue, pode explorar utilizar outras funcionalidades, tais como [emparelhado espaços de nomes][paired namespaces]. Espaços de nomes emparelhados têm outras limitações que são debatidas esse artigo.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Falha do Service Bus dentro de um datacenter do Azure
A razão mais provável para uma falha num datacenter do Azure é uma implementação de atualização falhada do Service Bus ou um sistema dependente. Dado que a plataforma amadureceu, o seu tem a probabilidade deste tipo de falha. Também pode ocorrer uma falha de centro de dados por motivos que incluem o seguinte:

* Falha de elétrica (fonte de alimentação e energia gerar desaparecer).
* Conectividade (quebra de internet entre os clientes e o Azure).

Em ambos os casos, um desastre natural ou man-made causou o problema. Para contornar este problema e certifique-se de que podem ainda enviar mensagens, pode utilizar [emparelhado espaços de nomes] [ paired namespaces] para ativar as mensagens sejam enviados para uma localização segundo enquanto a localização principal é efetuada em bom estado novamente. Para obter mais informações, consulte [melhores práticas para insulating aplicações contra falhas de Service Bus e perante desastres][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Espaços de nomes emparelhados
O [emparelhado espaços de nomes] [ paired namespaces] funcionalidade suporta cenários em que uma entidade de barramento de serviço ou a implementação no Centro de dados fica indisponível. Enquanto este evento ocorre com pouca frequência, ainda existem sistemas distribuídos que tem de ser preparados para processar cenários de caso pior. Normalmente, este evento ocorre porque algumas elemento de que depende de barramento de serviço estão a ocorrer um problema de curta duração. Para manter a disponibilidade da aplicação durante uma falha, os utilizadores do Service Bus podem utilizar dois espaços de nomes separados, de preferência nos centros de dados individual, para alojar as entidades de mensagens. O resto esta secção utiliza a seguinte terminologia:

* Espaço de nomes primário: O espaço de nomes com o qual a aplicação interage, para enviar e receber operações.
* Espaço de nomes secundário: O espaço de nomes que age como uma cópia de segurança para o espaço de nomes primário. Lógica da aplicação não interage com este espaço de nomes.
* Intervalo de ativação pós-falha: O período de tempo para falhas normais de aceitar antes da aplicação muda do espaço de nomes primário para o espaço de nomes secundário.

Suporte de espaços de nomes de emparelhar *enviar disponibilidade*. Envie disponibilidade preserva a capacidade para enviar mensagens. Para utilizar a disponibilidade de envio, a aplicação tem de cumprir os seguintes requisitos:

1. Apenas são recebidas mensagens do espaço de nomes primário.
2. As mensagens enviadas para uma determinada fila ou tópico poderá chegarem fora de ordem.
3. Poderão chegam fora de ordem mensagens dentro de uma sessão. Esta é uma quebra da funcionalidade normal das sessões. Isto significa que a sua aplicação utiliza sessões logicamente as mensagens de grupo.
4. Estado da sessão é mantido apenas no espaço de nomes primário.
5. A fila principal pode ficar online e iniciar a aceitação das mensagens antes da fila secundária fornece todas as mensagens na fila principal.

As secções seguintes abordam as APIs, como as APIs são implementadas e mostra o código de exemplo que utilizam a funcionalidade. Tenha em atenção que existem implicações de faturação associadas a esta funcionalidade.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>O MessagingFactory.PairNamespaceAsync API
A funcionalidade de espaços de nomes emparelhado inclui o [PairNamespaceAsync] [ PairNamespaceAsync] método no [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] classe:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Quando a tarefa estiver concluída, o emparelhamento do espaço de nomes, também é completa e pronto para agir em caso [MessageReceiver][MessageReceiver], [QueueClient] [ QueueClient] , ou [TopicClient] [ TopicClient] criado com o [MessagingFactory] [ MessagingFactory] instância. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] é a classe base para os diferentes tipos de emparelhamento que estão disponíveis com um [MessagingFactory] [ MessagingFactory] objeto. Atualmente, a única classe derivada é uma chamada [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], que implementa os requisitos de disponibilidade de envio. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] tem um conjunto de construtores que criar entre si. Observar o construtor com a maioria dos parâmetros, pode compreender o comportamento dos outros construtores.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Estes parâmetros têm os significados seguintes:

* *secondaryNamespaceManager*: um inicializado [NamespaceManager] [ NamespaceManager] instância para o espaço de nomes secundário que o [PairNamespaceAsync] [ PairNamespaceAsync] método pode utilizar para configurar o espaço de nomes secundário. O Gestor de espaço de nomes é utilizado para obter a lista de filas no espaço de nomes e certifique-se de que existem as filas de registo de segurança necessário. Se não existirem as filas, são criados. [NamespaceManager] [ NamespaceManager] requer a capacidade de criar um token com o **gerir** de afirmação.
* *messagingFactory*: O [MessagingFactory] [ MessagingFactory] instância para o espaço de nomes secundário. O [MessagingFactory] [ MessagingFactory] objeto é utilizado para enviar e, se o [EnableSyphon] [ EnableSyphon] propriedade está definida como **true**, receber mensagens de filas de registo de segurança.
* *backlogQueueCount*: O número de filas de registo de segurança para criar. Este valor tem de ser no mínimo 1. Quando enviar mensagens para o registo de segurança, um destas filas é escolhido aleatoriamente. Se definir o valor para 1, em seguida, apenas uma fila pode nunca ser utilizada. Quando isto acontece e a fila de um registo de segurança gera erros, o cliente não consegue experimentar uma fila de registo de segurança diferentes e pode falhar ao enviar a mensagem. Recomendamos que a definição deste valor para algumas maior valor e predefinido o valor para 10. Pode alterar isto para um valor superior ou inferior, dependendo da quantidade de dados a aplicação envia por dia. Cada fila de registo de segurança pode conter até 5 GB de mensagens em fila.
* *failoverInterval*: O período de tempo durante os quais irá aceitar falhas no espaço de nomes primário antes de mudar qualquer entidade única através de para o espaço de nomes secundário. As ativações pós-falha ocorrerem numa entidade pela entidade base. As entidades de um único espaço de nomes com frequência em direto em nós diferentes no Service Bus. Uma falha de uma entidade não implica uma falha no outro. Pode definir este valor como [System.TimeSpan.Zero] [ System.TimeSpan.Zero] a ativação pós-falha para o secundário imediatamente após a falha da primeira, não transitório. Falhas acionam o temporizador de ativação pós-falha são qualquer [MessagingException] [ MessagingException] no qual o [IsTransient] [ IsTransient] propriedade é false, ou um [ System.TimeoutException][System.TimeoutException]. Outras exceções, tal como [UnauthorizedAccessException] [ UnauthorizedAccessException] não fazer com que a ativação pós-falha, porque indicam que o cliente é configurado incorretamente. A [ServerBusyException] [ ServerBusyException] não causa ativação pós-falha porque o padrão correto é Aguarde 10 segundos, em seguida, envie a mensagem novamente.
* *enableSyphon*: indica que este emparelhamento específica deve também syphon mensagens do espaço de nomes secundário para o espaço de nomes primário. Em geral, as aplicações que enviam mensagens devem definir este valor como **falso**; as aplicações que receber mensagens devem definir este valor como **verdadeiro**. O motivo para isto é que frequentemente, existem menos recetores de mensagem que os remetentes de mensagens. Dependendo do número de recetores, pode optar por ter uma instância de aplicação único processar os deveres syphon. Utilizar vários recetores tem implicações de faturação para cada fila de registo de segurança.

Para utilizar o código, crie um site primário [MessagingFactory] [ MessagingFactory] instância de uma secundária [MessagingFactory] [ MessagingFactory] instância de uma secundária [ NamespaceManager] [ NamespaceManager] instância e um [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instância. A chamada pode ser tão simple como o seguinte:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Quando a tarefa devolvida pelo [PairNamespaceAsync] [ PairNamespaceAsync] método estar concluída, tudo está definido configurado e pronto a utilizar. Antes da tarefa é devolvida, poderá não concluir todo o trabalho em segundo plano necessário para que o emparelhamento do direito de trabalho. Como resultado, não deverá começar a enviar mensagens, até que a tarefa devolve. Se tiver ocorreram quaisquer falhas, tais como credenciais de incorretas ou uma falha ao criar as filas de registo de segurança, essas exceções serão emitidas uma vez concluída a tarefa. Depois da tarefa devolve, verifique se as filas foram encontradas ou criadas, examinando o [BacklogQueueCount] [ BacklogQueueCount] propriedade no seu [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instância. Para o código anterior, essa operação aparece da seguinte forma:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu as noções básicas do serviço de mensagens assíncrono no Service Bus, leia mais detalhes sobre [emparelhado espaços de nomes][paired namespaces].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md

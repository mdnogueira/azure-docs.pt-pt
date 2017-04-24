---
title: "O que é o Azure Event Hubs e por que motivo deve ser utilizado | Microsoft Docs"
description: "Descrição geral e introdução ao Azure Event Hubs – Ingestão de telemetria escalável na cloud a partir de Web sites, aplicações e dispositivos"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: sethm; babanisa
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 28a14cc68f44a278274e60cf46d5344c85dcc777
ms.lasthandoff: 04/18/2017


---
# <a name="what-is-event-hubs"></a>O que são os Hubs de Eventos?
Os Hubs de Eventos do Azure são uma plataforma de transmissão em fluxo de dados altamente escaláveis com capacidade de ingestão de milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Com a possibilidade de fornecer capacidades de subscrição de publicação com baixa latência e em dimensionamento maciço, os Hubs de Eventos servem de "rampa"para os Macrodados.

## <a name="why-use-event-hubs"></a>Por quê utilizar o Event Hubs?
As capacidades de processamento de eventos e telemetria do Event Hubs são úteis sobretudo para:

* Instrumentação de aplicações
* Experiência de utilizador ou de processamento de fluxo de trabalho
* Cenários Internet das Coisas (IoT)

O Event Hubs também inclui o controlo do comportamento em aplicações móveis, informações de tráfego de Web farms, captura de eventos nos jogos de consola ou telemetria recolhida a partir de máquinas industriais ou veículos ligados.

## <a name="azure-event-hubs-overview"></a>Descrição geral dos Event Hubs do Azure
Uma das funções comuns que o Event Hubs desempenha nas arquiteturas de solução é ser a "porta de entrada" para um pipeline de eventos, denominado frequentemente *ingestor de eventos*. Um ingestor de eventos é um componente ou serviço que se encontra entre os publicadores de eventos e os consumidores de eventos para desacoplar a produção de uma transmissão de eventos do consumo desses eventos. A imagem seguinte mostra esta arquitetura:

![Event Hubs](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

O Azure Event Hubs é um serviço de processamento de eventos que fornece ingestão de eventos e telemetria ao nível da cloud, com baixa latência e alta fiabilidade. Os Hubs de Eventos fornecem uma capacidade de processamento da transmissão de mensagens e têm características diferentes das mensagens empresariais tradicionais. As capacidades do Event Hubs são criadas em torno dos cenários de alto débito e processamento de eventos. Como tal, o Event Hubs não implementa algumas das capacidades de mensagens que estão disponíveis para as entidades de mensagens, como os tópicos.

É criado um hub de eventos ao nível do espaço de nomes e AMQP e HTTP são utilizados como as respetivas interfaces de API principais.

## <a name="event-publishers"></a>Publicadores de eventos
Qualquer entidade que envie dados para um hub de eventos é um *publicador de eventos*. Os publicadores de eventos podem publicar eventos através de HTTPS ou AMQP 1.0. Os publicadores de eventos utilizam um token de Assinatura de Acesso Partilhado (SAS) para se identificarem a si próprios perante um hub de eventos e podem ter uma identidade exclusiva ou utilizar um token SAS comum.

### <a name="publishing-an-event"></a>Publicar um evento
Pode publicar um evento através de AMQP 1.0 ou HTTPS. O Service Bus fornece uma classe [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) para publicar eventos num hub de eventos a partir de clientes .NET. Para outros tempos de execução e plataformas, pode utilizar qualquer cliente AMQP 1.0, como o [Apache Qpid](http://qpid.apache.org/). Pode publicar eventos individualmente ou em lotes. Uma única publicação (instância de dados de eventos) tem um limite de 256 KB, independentemente de ser um evento simples ou um lote. Publicar eventos maiores do que este limite origina um erro. É preferível os publicadores não terem conhecimento das partições dentro do hub de eventos e apenas especificarem uma *chave de partição* (apresentada na secção seguinte) ou a sua identidade através do respetivo token SAS.

A opção para utilizar AMQP ou HTTPS é específica do cenário de utilização. O AMQP requer o estabelecimento de um socket bidirecional persistente, para além da segurança de nível do transporte (TLS) ou SSL/TLS. O AMQP tem custos de rede superiores ao inicializar a sessão. No entanto, o HTTPS requer sobrecarga SSL adicional para cada pedido. O AMQP tem um desempenho superior para publicadores frequentes.

![Event Hubs](./media/event-hubs-what-is-event-hubs/partition_keys.png)

Os Hubs de Eventos garantem que todos os eventos que partilham o valor de chave de partição sejam entregues por ordem e para a mesma partição. Se as chaves de partição forem utilizadas com as políticas do publicador, a identidade do publicador e o valor da chave de partição devem coincidir. Caso contrário, ocorrerá um erro.

### <a name="publisher-policy"></a>Política do publicador
Os Event Hubs permitem um controlo granular sobre os publicadores de eventos através de *políticas do publicador*. As políticas do publicador são funcionalidades de tempo de execução concebidas para facilitar um elevado número de publicadores de eventos independentes. Com as políticas do publicador, cada publicador utiliza o seu próprio identificador exclusivo quando publica eventos num hub de eventos, utilizando o mecanismo seguinte:

```
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Não precisa de criar os nomes dos publicadores com antecedência, mas devem corresponder ao token SAS utilizado ao publicar um evento, para garantir identidades do publicador independentes. Ao utilizar as políticas do publicador, o valor **PartitionKey** é definido para o nome do publicador. Para que funcionem corretamente, estes valores têm de corresponder.

## <a name="partitions"></a>Partições
O Event Hubs fornece transmissão de mensagens através de um padrão de consumidor particionado em que cada consumidor só lê um subconjunto específico, ou partição, do fluxo de mensagens. Este padrão permite um dimensionamento horizontal do processamento de eventos e fornece outras funcionalidades centradas nos fluxos que não estão disponíveis nas filas e nos tópicos.

Uma partição é uma sequência ordenada de eventos mantida num hub de eventos. À medida que chegam novos eventos, eles são adicionados ao final desta sequência. Uma partição pode ser considerada como um "registo de consolidação".

![Event Hubs](./media/event-hubs-what-is-event-hubs/partition.png)

O Event Hubs mantém os dados de um período de retenção configurado aplicado a todas as partições no hub de eventos. Os eventos expiram em intervalos de tempo; não é possível eliminá-los explicitamente. Uma vez que as partições são independentes e contêm a sua própria sequência de dados, aumentam frequentemente com taxas diferentes.

![Event Hubs](./media/event-hubs-what-is-event-hubs/multiple_partitions.png)

O número de partições é especificado durante a criação e deve ser entre 2 e 32. O número de partições não é alterável, pelo que deve considerar uma escala a longo prazo quando definir o número de partições. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Pode aumentar o número de partições além de 32 ao contactar a equipa do Event Hubs.

Embora as partições sejam identificáveis e possam ser enviadas diretamente, isto não é recomendado. Em vez disso, pode utilizar construções de nível superior, apresentadas nas secções [Publicador de eventos](#event-publishers) e [Capacidade](#capacity). 

As partições são preenchidas por uma sequência de dados de eventos que contêm o corpo do evento, uma matriz de propriedades definida pelo utilizador e metadados, como o desvio na partição e o respetivo número na sequência de transmissão.

Para obter mais informações sobre as partições e o compromisso entre disponibilidade e fiabilidade, veja o [Guia de programação de Hubs de Eventos](event-hubs-programming-guide.md#partition-key) e o artigo [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md).

### <a name="partition-key"></a>Chave de partição
Pode utilizar uma [chave de partição](event-hubs-programming-guide.md#partition-key) para mapear dados de eventos recebidos em partições específicas para a finalidade de organização de dados. A chave de partição é um valor fornecido pelo remetente transmitido para um hub de eventos. É processada através de uma função hash estática que cria a atribuição de partições. Se não especificar uma chave de partição ao publicar um evento, é utilizada uma atribuição round robin.

O publicador de eventos apenas tem conhecimento da respetiva chave de partição, não da partição onde os eventos são publicados. Este desacoplamento da chave e da partição faz com que o remetente não tenha necessidade de saber muito sobre o processamento a jusante. Uma identidade por dispositivo ou utilizador exclusivo faz com que uma chave de partição seja segura, mas outros atributos como a geografia também podem ser utilizados para agrupar os eventos relacionados numa única partição.

## <a name="sas-tokens"></a>Tokens SAS
Os Hubs de Eventos utilizam *Assinaturas de Acesso Partilhado*, que estão disponíveis no espaço de nomes e ao nível do hub de eventos. Um token SAS é gerado a partir de uma chave SAS e é um hash SHA de um URL, codificado num formato específico. Através do nome da chave (política) e do token, o Event Hubs pode regenerar o hash e, assim, autenticar o remetente. Normalmente, os tokens SAS de publicadores de eventos são criados apenas com privilégios de **envio** num hub de eventos específico. Este mecanismo de URLs do token SAS é a base para a identificação do publicador apresentada na política do publicador. Para obter mais informações sobre como trabalhar com SAS, consulte [Autenticação da Assinatura de Acesso Partilhado com o Service Bus](../service-bus-messaging/service-bus-sas.md).

## <a name="event-consumers"></a>Consumidores de eventos
Qualquer entidade que leia os dados de eventos de um hub de eventos é um *consumidor de eventos*. Todos os consumidores de Hubs de Eventos ligam-se através da sessão AMQP 1.0 e os eventos são entregues durante a sessão à medida que ficam disponíveis. O cliente não necessita de consultar a disponibilidade dos dados.

### <a name="consumer-groups"></a>Grupos de consumidores
O mecanismo de publicação/subscrição de Hubs de Eventos é ativado através de *grupos de consumidores*. Um grupo de consumidores é uma vista (estado, posição ou desvio) de um hub de eventos completo. Os grupos de consumidores ativam várias aplicações de consumo e cada uma tem uma vista separada do fluxo de eventos e lê o fluxo de forma independente ao seu próprio ritmo e com os seus próprios desvios.

Na arquitetura de processamento de transmissão, cada aplicação a jusante equaciona um grupo de consumidores. Se pretender escrever dados de eventos para armazenamento de longa duração, essa aplicação de escrita de armazenamento é um grupo de consumidores. O processamento de eventos complexos pode ser efetuado por outro grupo de consumidores, em separado. Só pode aceder a partições através de um grupo de consumidores. Cada partição só pode ter um leitor ativo **de um determinado grupo de consumidores** de cada vez. Há sempre um grupo de consumidores predefinido num hub de eventos e pode criar até 20 grupos de consumidores para um hub de eventos de camada Standard.

Apresentamos a seguir exemplos da convenção de URI do grupo de consumidores:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

A imagem seguinte mostra a arquitetura do processamento de fluxos dos Hubs de Eventos:

![Event Hubs](./media/event-hubs-what-is-event-hubs/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Desvios de fluxo
Um *desvio* é a posição de um evento numa partição. Pode considerar um desvio igual a um cursor do lado do cliente. O desvio é uma numeração de bytes do evento. Este desvio permite que um consumidor de eventos (leitor) especifique um ponto no fluxo de eventos a partir do qual pretende começar a ler eventos. Pode especificar o desvio como um carimbo de data/hora ou como um valor de desvio. Os consumidores são responsáveis por armazenarem os seus próprios valores de desvio fora do serviço dos Event Hubs. Dentro de uma partição, cada evento inclui um desvio.

![Event Hubs](./media/event-hubs-what-is-event-hubs/partition_offset.png)

### <a name="checkpointing"></a>Ponto de verificação
O *ponto de verificação* é um processo pelo qual os leitores marcam ou confirmam a respetiva posição dentro de uma sequência de eventos da partição. O ponto de verificação é da responsabilidade do consumidor e ocorre numa base por partição dentro de um grupo de consumidores. Esta responsabilidade significa que para cada grupo de consumidores, cada leitor da partição tem de manter um controlo da respetiva posição atual no fluxo de eventos e pode informar o serviço quando considera o fluxo de dados completo.

Se um leitor for desligado de uma partição, quando voltar a ser ligado, começa a leitura no ponto de verificação que foi previamente submetido pelo último leitor dessa partição nesse grupo de consumidores. Quando o leitor é novamente ligado, ele passa este desvio para o hub de eventos para especificar a localização na qual deve começar a ler. Desta forma, pode utilizar o ponto de verificação para marcar os eventos como “concluídos” pelas aplicações a jusante e para fornecer resiliência se ocorrer uma ativação pós-falha entre os leitores em execução em computadores diferentes. É possível devolver dados mais antigos ao especificar um desvio inferior a partir deste processo de ponto de verificação. Através deste mecanismo, o ponto de verificação ativa a resiliência pós-falha e a repetição do fluxo de eventos.

### <a name="common-consumer-tasks"></a>Tarefas comuns do consumidor
Todos os consumidores de Hubs de Eventos ligam-se através de uma sessão do AMQP 1.0 e do canal de comunicação bidirecional com deteção de estado. Cada partição tem uma sessão do AMQP 1.0 que facilita o transporte de eventos segregados pela partição.

#### <a name="connect-to-a-partition"></a>Ligar a uma partição
Quando ligar a partições, é prática comum utilizar um mecanismo de locação para coordenar as ligações do leitor com partições específicas. Desta forma, é possível para cada partição num grupo de consumidores ter apenas um leitor ativo. O ponto de verificação, a locação e a gestão de leitores são simplificados através da classe [EventProcessorHost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost) para clientes .NET. O Anfitrião do Processador de Eventos é um agente de consumidor inteligente.

#### <a name="read-events"></a>Ler eventos
Depois de abrir uma sessão AMQP 1.0 e uma ligação para uma partição específica, os eventos são entregues para o cliente AMQP 1.0 pelo serviço de Event Hubs. Este mecanismo de entrega permite um maior débito e uma latência inferior do que os mecanismos baseados na solicitação como HTTP GET. Como os eventos são enviados para o cliente, cada instância de dados de eventos contém metadados importantes, como o número de sequência e desvio que são utilizados para facilitar o ponto de verificação numa sequência de eventos.

Dados do evento:
* Desvio
* Número de sequência
* Corpo
* Propriedades do utilizador
* Propriedades do sistema

É da sua responsabilidade gerir o desvio.

## <a name="capacity"></a>Capacidade
O Event Hubs tem uma arquitetura paralela altamente escalável e existem vários fatores principais a considerar no tamanho e dimensionamento.

### <a name="throughput-units"></a>Unidades de débito
A capacidade de débito do Event Hubs é controlada por *unidades de débito*. As unidades de débito são unidades de capacidade previamente compradas. Uma única unidade de débito inclui a seguinte capacidade:

* Entrada: até 1 MB por segundo ou 1000 eventos por segundo (o que ocorrer primeiro)
* Saída: até 2 MB por segundo

Além da capacidade das unidades de débito adquiridas, a entrada é limitada e é devolvida uma [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception). A saída não produz exceções de limitação, mas continua a ser limitada à capacidade das unidades de débito adquiridas. Se receber exceções da taxa de publicação ou estiver à espera de ver uma saída superior, não se esqueça de verificar quantas unidades de débito adquiriu para o espaço de nomes. Pode gerir as unidades de débito no painel **Dimensionamento** dos espaços de nomes no [portal do Azure][Azure portal]. Também pode gerir unidades de débito através de programação, com as APIs do Azure.

As unidades de débito são faturadas por hora e são previamente adquiridas. Assim que forem adquiridas, as unidades de débito são faturadas por um mínimo de uma hora. É possível adquirir até 20 unidades de débito para um espaço de nomes dos Hubs de Eventos e partilhá-las entre todos os Hubs de Eventos no espaço de nomes.

É possível adquirir mais unidades de débito em blocos de 20, até 100 unidades de débito, ao contactar o suporte do Azure. Para além disso, também pode comprar blocos de 100 unidades de débito.

Recomendamos que equilibre as unidades de débito e as partições para alcançar o dimensionamento ideal. Uma única partição tem um dimensionamento máximo de uma unidade de débito. O número de unidades de débito deve ser inferior ou igual ao número de partições num hub de eventos.

Para obter informações detalhadas sobre os preços, consulte [Preços dos Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Passos seguintes

* Introdução a um [Tutorial dos Hubs de Eventos][Event Hubs tutorial]
* [Guia de programação dos Event Hubs](event-hubs-programming-guide.md)
* [Disponibilidade e consistência em Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos]

[Event Hubs tutorial]: event-hubs-csharp-ephcs-getstarted.md
[Aplicações de exemplo que utilizam Hubs de Eventos]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Azure portal]: https://portal.azure.com


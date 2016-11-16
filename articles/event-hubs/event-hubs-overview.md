---
title: "Descrição geral dos Hubs de Eventos do Azure | Microsoft Docs"
description: "Introdução e descrição geral dos Event Hubs do Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f0e0dd20-f745-49c7-bfca-30ea1c46e873
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: df9897894a2a2a09735b0947fd335959e81a46cd


---
# <a name="azure-event-hubs-overview"></a>Descrição geral dos Event Hubs do Azure
As várias soluções modernas pretendem fornecer experiências adaptáveis a clientes ou melhorar os produtos através de comentários contínuos e telemetria automatizada. Essas soluções são confrontadas com o desafio de processar de forma segura e fiável grandes quantidades de informações de vários fabricantes em simultâneo. Os Event Hubs do Microsoft Azure são um serviço de plataforma gerido que fornece uma base para introdução de dados em grande escala numa variedade abrangente de cenários. Exemplos de tais cenários são o controlo do comportamento em aplicações móveis, as informações de tráfego de Web farms, a captura de eventos nos jogos de consola ou os dados telemétricos recolhidos de máquinas industriais ou veículos ligados. Uma das funções comuns que os Event Hubs desempenham nas arquiteturas de solução é atuarem como a "porta de entrada" para um pipeline de eventos, denominado frequentemente *ingestor de eventos*. Um ingestor de eventos é um componente ou serviço que se encontra entre os publicadores de eventos e os consumidores de eventos para desacoplar a produção de uma transmissão de eventos do consumo desses eventos.

![Event Hubs](./media/event-hubs-overview/IC759856.png)

Os Event Hubs do Azure são um serviço de processamento de eventos que permitem a entrada em grande escala de telemetria e eventos para a nuvem, com baixa latência e alta fiabilidade. Este serviço, utilizado com outros serviços a jusante, é particularmente útil em cenários Internet das Coisas (IoT), de processamento da experiência ou do fluxo de trabalho dos utilizadores e de instrumentação de aplicações. Os Event Hubs fornecem uma capacidade de processamento da transmissão de mensagens e, embora um Hub de Eventos seja uma entidade semelhante às filas e aos tópicos, tem características muito diferentes das mensagens empresariais tradicionais. Os cenários de mensagens empresariais necessitam normalmente de capacidades sofisticadas, como sequenciação, mensagens não entregues, suporte para transações e garantias de entrega seguras, enquanto a preocupação dominante da entrada de eventos se prende com o débito alto e a flexibilidade de processamento dos fluxos de eventos. Por conseguinte, as capacidades dos Event Hubs diferem dos tópicos do Service Bus na medida em que estes são totalmente direcionados para os cenários de processamento de eventos de débito alto. Como tal, os Event Hubs não implementam algumas das capacidades de mensagens que estão disponíveis para os tópicos. Se precisar dessas funcionalidades, os tópicos continuam a ser a escolha ideal.

Um Hub de Eventos é criado ao nível do espaço de nomes nos Hubs de Eventos de forma semelhante às filas e aos tópicos no Service Bus. Os Event Hubs utilizam AMQP e HTTP como respetivas interfaces API primárias. O diagrama seguinte mostra a relação entre os Event Hubs e o Service Bus.

![Event Hubs](./media/event-hubs-overview/ehoverview2.png)

## <a name="conceptual-overview"></a>Descrição geral conceptual
Os Event Hubs fornecem a transmissão de mensagens através de um padrão de consumidor particionado. As filas e os tópicos utilizam um modelo [Consumidor Concorrente](https://msdn.microsoft.com/library/dn568101.aspx) em que cada consumidor tenta ler a partir da mesma fila ou recurso. Esta disputa pelos recursos resulta, em última análise, em limites de dimensionamento e complexidade das aplicações de processamento dos fluxos. Os Event Hubs utilizam um padrão de consumidor particionado em que cada consumidor só lê um subconjunto específico, ou partição, do fluxo de mensagens. Este padrão permite um dimensionamento horizontal do processamento de eventos e fornece outras funcionalidades centradas nos fluxos que não estão disponíveis nas filas e nos tópicos.

### <a name="partitions"></a>Partições
Uma partição é uma sequência ordenada de eventos mantida num Hub de Eventos. À medida que chegam novos eventos, eles são adicionados ao final desta sequência. Uma partição pode ser considerada como um "registo de consolidação".

![Event Hubs](./media/event-hubs-overview/IC759857.png)

As partições mantêm os dados de um período de retenção configurado, definido ao nível do Hub de Eventos. Esta definição aplica-se em todas as partições do Hub de Eventos. Os eventos expiram em intervalos de tempo; não é possível eliminá-los explicitamente. Um Hub de Eventos contém várias partições. Cada partição é independente e contém a respetiva sequência de dados. Como resultado, as partições, muitas vezes, aumentam com taxas diferentes.

![Event Hubs](./media/event-hubs-overview/IC759858.png)

O número de partições é especificado no momento de criação do Hub de Eventos e deve ser entre 2 e 32 (a predefinição é 4). As partições são um mecanismo de organização dos dados e têm mais a ver com o grau de paralelismo a jusante necessário em aplicações de consumo do que com o débito dos Event Hubs. Isto faz com que a opção do número de partições num Hub de Eventos esteja diretamente relacionada com o número de leitores simultâneos que espera que ter. Após a criação do Hub de Eventos, não é possível alterar a contagem das partições; deve considerar este número em termos de dimensionamento esperado de longa duração. Pode aumentar o limite de 32 partições entrando em contacto com a equipa do Service Bus.

Apesar de as partições serem identificáveis e poderem ser enviadas diretamente, é preferível evitar o envio de dados para partições específicas. Em vez disso, pode utilizar construções de nível superior, apresentadas nas secções [Publicador de eventos](#event-publisher) e [Política do Publicador](#capacity-and-security).

No contexto dos Event Hubs, as mensagens são referidas como *dados de eventos*. Os dados de eventos contêm o corpo do evento, uma matriz de propriedades definida pelo utilizador e vários metadados sobre o evento, como o desvio na partição e o respetivo número na sequência de transmissão. As partições são preenchidas com uma sequência de dados de eventos.

## <a name="event-publisher"></a>Publicador de eventos
Qualquer entidade que envie eventos ou dados para um Hub de Eventos é um *publicador de eventos*. Os publicadores de eventos podem publicar eventos utilizando HTTPS ou AMQP 1.0. Os publicadores de eventos utilizam um token de Assinatura de Acesso Partilhado (SAS) para se identificarem a si próprios perante um Hub de Eventos e podem ter uma identidade exclusiva ou utilizar um token SAS comum, dependendo dos requisitos do cenário.

Para obter mais informações sobre como trabalhar com SAS, consulte [Autenticação da Assinatura de Acesso Partilhado com o Service Bus](../service-bus-messaging/service-bus-shared-access-signature-authentication.md).

### <a name="common-publisher-tasks"></a>Tarefas comuns do publicador
Esta secção descreve as tarefas comuns dos publicadores de eventos.

#### <a name="acquire-a-sas-token"></a>Adquirir um token SAS
A Assinatura de Acesso Partilhado (SAS) é o mecanismo de autenticação dos Event Hubs. O Service Bus fornece as políticas SAS no nível do espaço de nomes e do Hub de Eventos. Um token SAS é gerado a partir de uma chave SAS e é um hash SHA de um URL, codificado num formato específico. Utilizando o nome da chave (política) e o token, o Service Bus pode regenerar o hash e, assim, autenticar o remetente. Normalmente, os tokens SAS de publicadores de eventos são criados apenas com privilégios de **envio** num Hub de Eventos específico. Este mecanismo de URLs do token SAS é a base para a identificação do publicador apresentada na política do publicador. Para obter mais informações sobre como trabalhar com SAS, consulte [Autenticação da Assinatura de Acesso Partilhado com o Service Bus](../service-bus-messaging/service-bus-shared-access-signature-authentication.md).

#### <a name="publishing-an-event"></a>Publicar um evento
Pode publicar um evento através de AMQP 1.0 ou HTTPS. O Service Bus fornece uma classe [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) para publicar eventos num Hub de Eventos a partir de clientes .NET. Para outros tempos de execução e plataformas, pode utilizar qualquer cliente AMQP 1.0, como o [Apache Qpid](http://qpid.apache.org/). Pode publicar eventos individualmente ou em lotes. Uma única publicação (instância de dados de eventos) tem um limite de 256 KB, independentemente de ser um evento simples ou um lote. Publicar eventos maiores do que este limite origina um erro. É preferível os publicadores não terem conhecimento das partições dentro do Hub de Eventos e apenas especificarem uma *chave de partição* (apresentada na secção seguinte) ou a sua identidade através do respetivo token SAS.

A opção para utilizar AMQP ou HTTPS é específica do cenário de utilização. O AMQP requer o estabelecimento de um socket bidirecional persistente, para além da segurança de nível do transporte (TLS) ou SSL/TLS. Isto pode ser uma operação dispendiosa em termos de tráfego de rede, mas só ocorre no início de uma sessão AMQP. O HTTPS tem custos iniciais inferiores, mas exige custos de SSL adicionais para cada pedido. Para publicadores que frequentemente publicam eventos, o AMQP oferece poupança significativa a nível de desempenho, débito e latência.

### <a name="partition-key"></a>Chave de partição
Uma chave de partição é um valor que é utilizado para mapear dados de eventos recebidos em partições específicas para as finalidades de organização de dados. A chave de partição é um valor fornecido pelo remetente transmitido para um Hub de Eventos. É processada através de uma função hash estática e resulta na criação da atribuição de partições. Se não especificar uma chave de partição ao publicar um evento, é utilizada uma atribuição round robin. Quando utilizar chaves de partição, o publicador de eventos apenas tem conhecimento da respetiva chave de partição, não da partição para onde os eventos são publicados. Este desacoplamento da chave e da partição faz com que o remetente não tenha necessidade de saber muito sobre o processamento a jusante e o armazenamento de eventos. As chaves de partição são importantes para organizar os dados para o processamento a jusante, mas não estão de forma fundamental relacionadas às partições propriamente ditas. Uma identidade por dispositivo ou utilizador exclusivo faz com que uma chave de partição seja segura, mas outros atributos como a geografia também podem ser utilizados para agrupar os eventos relacionados numa única partição. A imagem seguinte mostra os remetentes de eventos a utilizar chaves de partição para afixar a partições.

![Event Hubs](./media/event-hubs-overview/IC759859.png)

Os Event Hubs garantem que todos os eventos que partilham o mesmo valor de chave de partição sejam entregues pela ordem e para a mesma partição. De destacar que se as chaves de partição forem utilizadas com as políticas do publicador, descritas na secção seguinte, a identidade do publicador e o valor da chave de partição devem coincidir. Caso contrário, ocorrerá um erro.

### <a name="event-consumer"></a>Consumidor de eventos
Qualquer entidade que leia os dados de eventos de um Hub de Eventos é um consumidor de eventos. Todos os consumidores de eventos leem o fluxo de eventos através de partições num grupo de consumidores. Cada partição deve ter apenas um leitor ativo de cada vez. Todos os consumidores de Event Hubs se ligam através da sessão AMQP 1.0, na qual os eventos são entregues à medida que ficam disponíveis. O cliente não necessita de consultar a disponibilidade dos dados.

#### <a name="consumer-groups"></a>Grupos de consumidores
O mecanismo de publicação/subscrição de Event Hubs é ativado através de grupos de consumidores. Um grupo de consumidores é uma vista (estado, posição ou desvio) de um Hub de Eventos completo. Os grupos de consumidores ativam várias aplicações de consumo e cada uma tem uma vista separada do fluxo de eventos e lê o fluxo de forma independente ao seu próprio ritmo e com os seus próprios desvios. Na arquitetura de processamento de transmissão, cada aplicação a jusante equaciona um grupo de consumidores. Se pretender escrever dados de eventos para armazenamento de longa duração, essa aplicação de escrita de armazenamento é um grupo de consumidores. O processamento de eventos complexos é efetuado por outro grupo de consumidores, em separado. Só pode aceder a partições através de um grupo de consumidores. Há sempre um grupo de consumidores predefinido num Hub de Eventos e pode criar até 20 grupos de consumidores para um Hub de Eventos de camada Standard.

Apresentamos a seguir exemplos da convenção de URI do grupo de consumidores:

    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>

A imagem seguinte mostra os consumidores de eventos dentro dos grupos de consumidores.

![Event Hubs](./media/event-hubs-overview/IC759860.png)

#### <a name="stream-offsets"></a>Desvios de fluxo
Um desvio é a posição de um evento dentro de uma partição. Pode considerar um desvio igual a um cursor do lado do cliente. O desvio é uma numeração de bytes do evento. Isto permite que um consumidor de eventos (leitor) especifique um ponto no fluxo de eventos a partir do qual pretende começar a ler eventos. Pode especificar o desvio como um carimbo de data/hora ou como um valor de desvio. Os consumidores são responsáveis por armazenarem os seus próprios valores de desvio fora do serviço dos Event Hubs.

![Event Hubs](./media/event-hubs-overview/IC759861.png)

Dentro de uma partição, cada evento inclui um desvio. Este desvio é utilizado pelos consumidores para mostrar a localização na sequência de eventos de uma determinada partição. Os desvios podem ser passados para o Hub de Eventos como um número ou como um valor de carimbo de data/hora quando é ligado um leitor.

#### <a name="checkpointing"></a>Ponto de verificação
O *ponto de verificação* é um processo pelo qual os leitores marcam ou confirmam a respetiva posição dentro de uma sequência de eventos da partição. O ponto de verificação é da responsabilidade do consumidor e ocorre numa base por partição dentro de um grupo de consumidores. Isto significa que para cada grupo de consumidores, cada leitor da partição tem de manter um controlo da respetiva posição atual no fluxo de eventos e pode informar o serviço quando considera o fluxo de dados completo. Se um leitor for desligado de uma partição, quando voltar a ser ligado, começa a leitura no ponto de verificação que foi previamente submetido pelo último leitor dessa partição nesse grupo de consumidores. Quando o leitor é novamente ligado, ele passa este desvio para o Hub de Eventos para especificar a localização na qual deve começar a ler. Desta forma, pode utilizar o ponto de verificação para marcar os eventos como "concluídos" pelas aplicações a jusante e para fornecer resiliência na eventualidade de uma ativação pós-falha entre os leitores em execução em computadores diferentes. Uma vez que os dados dos eventos são retidos durante o intervalo de retenção especificado no momento em que o Hub de Eventos é criado, é possível regressar aos dados antigos especificando um desvio inferior a partir deste processo de ponto de verificação. Através destes mecanismo, o ponto de verificação ativa a resiliência pós-falha e repetição do fluxo de eventos controlada.

#### <a name="common-consumer-tasks"></a>Tarefas comuns do consumidor
Esta secção descreve as tarefas comuns dos consumidores de eventos ou dos leitores de Event Hubs. Todos os consumidores de Event Hubs estabelecem ligação através de AMQP 1.0. AMQP 1.0 é uma sessão e um canal de comunicação bidirecional com deteção de estado. Cada partição tem uma sessão de ligação AMQP 1.0 que facilita o transporte de eventos segregados pela partição.

##### <a name="connect-to-a-partition"></a>Ligar a uma partição
Para poder consumir eventos a partir de um Hub de Eventos, um consumidor tem de se ligar a uma partição. Como mencionado anteriormente, deve aceder sempre a partições através de um grupo de consumidores. Como parte do modelo de consumidor particionado, apenas um único leitor deve estar ativo numa partição em qualquer momento, dentro de um grupo de consumidores. Quando ligar diretamente a partições, é prática comum utilizar um mecanismo de locação para coordenar as ligações do leitor com partições específicas. Desta forma, é possível para cada partição num grupo de consumidores ter apenas um leitor ativo. Gerir a posição na sequência de um leitor é uma tarefa importante, assegurada através do ponto de verificação. Esta funcionalidade é simplificada utilizando a classe [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) de clientes .NET. [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) é um agente de consumidor inteligente e está descrito na secção seguinte.

##### <a name="read-events"></a>Ler eventos
Depois de abrir uma sessão AMQP 1.0 e uma ligação para uma partição específica, os eventos são entregues para o cliente AMQP 1.0 pelo serviço de Event Hubs. Este mecanismo de entrega permite um maior débito e uma latência inferior do que os mecanismos baseados na solicitação como HTTP GET. Como os eventos são enviados para o cliente, cada instância de dados de eventos contém metadados importantes, como o número de sequência e desvio que são utilizados para facilitar o ponto de verificação numa sequência de eventos.

![Event Hubs](./media/event-hubs-overview/IC759862.png)

É da sua responsabilidade gerir este desvio de uma forma que melhor permita gerir o progresso no processamento do fluxo.

## <a name="capacity-and-security"></a>Capacidade e segurança
Os Event Hubs são uma arquitetura paralela altamente dimensionável para a entrada de fluxo. Como tal, existem vários aspetos fundamentais a considerar ao dimensionar uma solução baseada em Event Hubs. O primeiro destes controlos de capacidade é denominado *unidades de débito*, descritas na secção seguinte.

### <a name="throughput-units"></a>Unidades de débito
A capacidade de débito dos Event Hubs é controlada pelas unidades de débito. As unidades de débito são unidades de capacidade previamente compradas. Uma única unidade de débito inclui o seguinte:

* Entrada: até 1 MB por segundo ou 1000 eventos por segundo.
* Saída: até 2 MB por segundo.

A entrada está limitada à quantidade de capacidade fornecida pelo número de unidades de débito compradas. O envio de dados acima desta quantidade resulta numa exceção de "quota excedida". Esta quantidade é de 1 MB por segundo ou de 1000 eventos por segundo, o que ocorrer primeiro. A saída não produz exceções de limitação, mas é limitada à quantidade de transferência de dados fornecida pelas unidades de débito adquiridas: 2 MB por segundo por unidade de débito. Se receber exceções da taxa de publicação ou estiver à espera de ver uma saída superior, não se esqueça de verificar quantas unidades de débito adquiriu para o espaço de nomes no qual o Hub de Eventos foi criado. Para obter mais unidades de débito, pode ajustar a definição na página **Espaços de nomes** no separador **Dimensionamento** do [Portal Clássico do Azure][Portal Clássico do Azure]. Também pode alterar esta definição utilizando as APIs do Azure.

Enquanto as partições são um conceito de organização de dados, as unidades de débito são puramente um conceito de capacidade. As unidades de débito são faturadas por hora e são previamente adquiridas. Assim que forem adquiridas, as unidades de débito são faturadas por um mínimo de uma hora. Podem ser adquiridas até 20 unidades de débito para um espaço de nomes dos Hubs de Eventos e existe um limite de contas do Azure de 20 unidades de débito. Estas unidades de débito são partilhadas entre todos os Event Hubs num espaço de nomes especificado.

As unidades de débito são fornecidas numa base de melhor esforço e poderão nem sempre estar disponíveis para compra imediata. Se necessitar de uma capacidade específica, é recomendável adquirir essas unidades de débito com antecedência. Se precisar de mais de 20 unidades de débito, pode contactar o suporte do Azure para comprar unidades de débito, numa base de compromisso, em blocos de 20, até às primeiras 100 unidades de débito. Para além disso, também pode comprar blocos de 100 unidades de débito.

Recomendamos que equilibre cuidadosamente as unidades de débito e as partições para alcançar a escala ideal com os Hubs de Eventos. Uma única partição tem um dimensionamento máximo de uma unidade de débito. O número de unidades de débito deve ser inferior ou igual ao número de partições num Hub de Eventos.

Para obter informações detalhadas sobre os preços, consulte [Preços dos Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="publisher-policy"></a>Política do publicador
Os Event Hubs permitem um controlo granular sobre os publicadores de eventos através de *políticas do publicador*. As políticas do publicador são um conjunto de funcionalidades de tempo de execução concebidas para facilitar um elevado número de publicadores de eventos independentes. Com as políticas do publicador, cada publicador utiliza o seu próprio identificador exclusivo quando publica eventos num Hub de Eventos, utilizando o mecanismo seguinte:

    //<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>

Não precisa de criar os nomes dos publicadores com antecedência, mas devem corresponder ao token SAS utilizado ao publicar um evento, para garantir identidades do publicador independentes. Para obter mais informações sobre SAS, consulte [Autenticação da Assinatura de Acesso Partilhado com o Service Bus](../service-bus-messaging/service-bus-shared-access-signature-authentication.md). Ao utilizar as políticas do publicador, o valor **PartitionKey** é definido para o nome do publicador. Para que funcionem corretamente, estes valores têm de corresponder.

## <a name="summary"></a>Resumo
Os Event Hubs do Azure fornecem um serviço de processamento de telemetria e eventos de hiper escala que pode ser utilizado para aplicações comuns e para monitorizar o fluxo de trabalho do utilizador em qualquer escala. Com a possibilidade de fornecer capacidades de subscrição de publicação com baixa latência e em dimensionamento maciço, os Event Hubs servem de "rampa"para os Macrodados. Com a identidade baseada no publicador e listas de revogação, estas capacidades são expandidas para cenários Internet das Coisas comuns. Para obter mais informações sobre como desenvolver aplicações de Event Hubs, consulte o [Guia de programação dos Event Hubs](event-hubs-programming-guide.md).

## <a name="next-steps"></a>Passos seguintes
Agora que sabe o que são os Event Hubs, pode avançar os cenários seguintes:

* Introdução a um [Tutorial dos Hubs de Eventos].
* Uma [Aplicação de exemplo que utiliza os Hubs de Eventos] completa.

[Portal Clássico do Azure]: http://manage.windowsazure.com
[Tutorial dos Hubs de Eventos]: event-hubs-csharp-ephcs-getstarted.md
[Aplicação de exemplo que utiliza os Hubs de Eventos]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097



<!--HONumber=Nov16_HO2-->



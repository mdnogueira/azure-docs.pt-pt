---
title: "Descrição geral dos princípios básicos do Azure Service Bus | Microsoft Docs"
description: "Uma introdução à utilização do Service Bus para ligar aplicações Azure com outros programas de software."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 12654cdd-82ab-4b95-b56f-08a5a8bbc6f9
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/15/2017
ms.author: sethm
ms.openlocfilehash: af8b10f0a460e695a39879718174e81f78934ef8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-bus"></a>Service Bus do Azure

Se uma aplicação ou serviço é executado na nuvem ou de modo local, muitas vezes, tem de interagir com outros serviços ou aplicações. Para fornecer uma forma amplamente útil de efetuar este procedimento, o Microsoft Azure oferece o Service Bus. Este artigo analisa esta tecnologia, descreve o que é e por que razão poderá querer utilizá-la.

## <a name="service-bus-fundamentals"></a>Noções básicas sobre o Service Bus

Situações diferentes exigem estilos diferentes de comunicação. Por vezes, permitir às aplicações enviar e receber mensagens através de uma fila simples é a melhor solução. Noutras situações, uma fila comum não é suficiente e é melhor uma fila com um mecanismo de publicação e subscrição. Em alguns casos, o necessário é uma ligação entre as aplicações e não são precisas filas. O Service Bus fornece as três opções pelo que as suas aplicações podem interagir de várias maneiras diferentes.

O Service Bus é um serviço de nuvem multi-inquilino, o que significa que vários utilizadores partilham o serviço. Cada utilizador, como um programador de aplicações, cria um *espaço de nomes*, em seguida, define os mecanismos de comunicação que precisa dentro desse espaço de nomes. A Figura 1 mostra esta arquitetura.

![][1]

**Figura 1: O Service Bus fornece um serviço multi-inquilino para ligar aplicações através da nuvem.**

Dentro de um espaço de nomes, pode utilizar uma ou mais instâncias dos três diferentes mecanismos de comunicação, os quais ligam aplicações de forma diferente. As opções são:

* *Filas*, que permitem comunicação unidirecional. Cada fila funciona como um intermediário (por vezes denominado *mediador*) que armazena as mensagens enviadas até serem recebidas. Cada mensagem é recebida por um único destinatário.
* *Tópicos*, que proporcionam uma comunicação unidirecional através de *subscrições* – um só tópico pode ter várias subscrições. Tal como as filas, o tópico funciona como um mediador, mas cada subscrição pode utilizar opcionalmente um filtro para receber apenas as mensagens que correspondem a critérios específicos.
* *Reencaminhamentos*, que proporcionam comunicação bidirecional. Ao contrário das filas e tópicos, o reencaminhamento não armazena as mensagens em trânsito - não é um mediador. Simplesmente, transmite-as à aplicação de destino.

Quando cria uma fila, tópico ou reencaminhamento, dá-lhe um nome. Quando combinado com o espaço de nomes, cria-se um identificador exclusivo para o objeto. As aplicações podem fornecer este nome ao Service Bus e, em seguida, utilizar essa fila, tópico ou reencaminhamento para comunicar entre si. 

Para utilizar qualquer um destes objetos no cenário de reencaminhamento, as aplicações do Windows podem utilizar o Windows Communication Foundation (WCF). Este serviço é conhecido como [Reencaminhamento do WCF](../service-bus-relay/relay-what-is-it.md). Para as filas e tópicos, as aplicações do Windows podem utilizar as APIs de mensagens definidas pelo Service Bus. Para facilitar a utilização destes objetos a partir de aplicações que não sejam de Windows, a Microsoft disponibiliza SDKs para Java, Node.js e outras linguagens. Também pode aceder às filas e tópicos com as [REST APIs](/rest/api/servicebus/) através de HTTP(s). 

É importante compreender que, apesar do Service Bus propriamente dito ser executado na nuvem (ou seja, nos datacenters de Azure da Microsoft), as aplicações que o utilizam podem ser executadas em qualquer lugar. Pode utilizar o Service Bus para ligar aplicações que se executam, por exemplo, no Azure, ou aplicações que se executam dentro do seu próprio datacenter. Também pode utilizá-lo para ligar uma aplicação em execução no Azure ou noutra plataforma na nuvem com uma aplicação no local ou com telemóveis e tablets. Também é possível ligar aparelhos domésticos, sensores e outros dispositivos a uma aplicação central ou entre si. O Service Bus é um mecanismo de comunicação na nuvem que é acessível em praticamente qualquer lugar. O modo como o utiliza depende do que tem de realizar a aplicação.

## <a name="queues"></a>Filas

Suponha que decide ligar duas aplicações com uma fila do Service Bus. A figura 2 ilustra esta situação.

![][2]

**Figura 2: As filas do Service Bus proporcionam filas unidirecionais assíncronas.**

O processo é simples: um remetente envia uma mensagem para uma fila do Service Bus e um recetor apanha essa mensagem mais tarde. Uma fila pode ter apenas um recetor único, como mostra a Figura 2. Ou várias aplicações podem ler a partir da mesma fila. Na última situação, cada mensagem é lida por apenas um recetor. Num serviço multicast, deve utilizar um tópico.

Cada mensagem tem duas partes: um conjunto de propriedades, cada uma delas um par chave/valor e um payload de mensagem. O payload pode ser binário, texto ou até mesmo XML. O modo como são utilizados depende do que a aplicação está a tentar fazer. Por exemplo, uma aplicação que envia uma mensagem sobre uma venda recente pode incluir as propriedades **Vendedor="Ava"** e **Valor= 10000**. O corpo da mensagem poderá conter uma imagem digitalizada do contrato de venda assinado ou, se não existir, permanece vazio.

O recetor pode ler uma mensagem da fila do Service Bus de duas formas diferentes. A primeira opção, denominada *[ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode)*, remove a mensagem da fila e elimina-a imediatamente. Esta opção é simples, mas se há uma falha da parte do recetor antes de concluir o processamento da mensagem, esta será perdida. Dado que é removida da fila, nenhum outro recetor pode aceder à mesma. 

A segunda opção, *[PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode)*, foi criada para solucionar este problema. Como acontece com **ReceiveAndDelete**, uma leitura de **PeekLock** remove a mensagem da fila. No entanto, não elimina a mensagem. Neste caso, bloqueia a mensagem, pelo que fica invisível para os outros recetores, em seguida, aguarda que um dos três eventos ocorra:

* Se o recetor processar a mensagem com êxito, invoca [Concluir()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) e a fila elimina a mensagem. 
* Se o recetor decidir que não consegue processar a mensagem com êxito, invoca [Abandonar()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon). A fila, em seguida, remove o bloqueio da mensagem e torna-o disponível para outros recetores.
* Se o recetor não invocar nenhum destes métodos num período de tempo configurável (60 segundos, por predefinição), a fila assume que o recetor falhou. Neste caso, comporta-se como se o recetor invocasse **Abandonar**, pelo que a mensagem ficaria disponível para outros recetores.

Tenha em atenção o que pode acontecer aqui: a mesma mensagem poderá ser entregue duas vezes, talvez a dois recetores diferentes. As aplicações que utilizam filas do Service Bus devem estar preparadas para este evento. Para facilitar a deteção duplicada, cada mensagem dispõe de uma propriedade [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) exclusiva que não se modifica de forma predefinida, independentemente do número de vezes que se leia uma mensagem numa fila. 

As filas são úteis em determinadas situações. Permitem às aplicações comunicar entre si, mesmo quando ambas não estão a ser executadas ao mesmo tempo, algo que é especialmente útil com lotes e aplicações móveis. Uma fila com vários recetores também proporciona um balanceamento de carga automático, uma vez que as mensagens enviadas são distribuídas por estes recetores.

## <a name="topics"></a>Tópicos

Ainda que sejam úteis, as filas não sempre são a solução certa. Por vezes, os tópicos do Service Bus são melhores. A figura 3 ilustra a ideia.

![][3]

**Figura 3: Com base no filtro especificado por uma aplicação de subscrição, pode receber algumas ou todas as mensagens enviadas para um tópico do Service Bus.**

Um *tópico* é semelhante em muitos aspetos a uma fila. Os remetentes submetem mensagens a um tópico da mesma forma que submetem mensagens a uma fila e essas mensagens têm o mesmo aspeto que nas filas. A diferença é que os tópicos permitem a cada aplicação de receção criar a sua própria *subscrição* através da definição de um *filtro*. Consequentemente, o subscritor vê apenas as mensagens que correspondem a esse filtro. Por exemplo, a figura 3 mostra um remetente e um tópico com três subscritores, cada um com o seu próprio filtro:

* O subscritor 1 recebe apenas as mensagens que contêm a propriedade *Vendedor="Ava"*.
* O subscritor 2 recebe mensagens que contêm a propriedade *Vendedor="Ruby"* e/ou contem a propriedade *Valor* cujo valor é superior a 100.000. Talvez Ruby seja a gestora de vendas, pelo que pretende ver as suas próprias vendas e todas as vendas grandes, independentemente de quem as faz.
* O subscritor 3 definiu o seu filtro como *True*, o que significa que recebe todas as mensagens. Por exemplo, esta aplicação pode ser responsável por manter um registo de auditoria e, por conseguinte, precisa de ver todas as mensagens.

Como acontece com as filas, os subscritores de um tópico podem ler mensagens através de [ReceiveAndDelete ou de PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode). No entanto, ao contrário das filas, uma única mensagem enviada para um tópico pode recebida por várias subscrições. Esta abordagem, geralmente designada por *publicar e subscrever* (ou *pub/sub*), é útil quando várias aplicações estão interessadas nas mesmas mensagens. Se definir o filtro adequado, cada subscritor pode recuperar apenas a parte do fluxo de mensagens que quer ver.

## <a name="relays"></a>Reencaminhamentos

Tanto as filas como os tópicos proporcionam comunicação assíncrona unidirecional através de um mediador. O tráfego flui numa única direção e não existe uma ligação direta entre os remetentes e os recetores. Mas e se não quiser esta ligação? Suponha que as suas aplicações precisam de enviar e receber mensagens ou talvez pretenda uma ligação direta entre elas e não precisa de um mediador para armazenar as mensagens. Para solucionar soluções como esta, o Service Bus proporciona *reencaminhamentos*, como mostra a Figura 4.

![][4]

**Figura 4: O reencaminhamento do Service Bus proporciona comunicação síncrona bidirecional entre as aplicações.**

A questão óbvia sobre os reencaminhamentos que se coloca é esta: por que tenho de utilizar um? Mesmo que não precise de filas, por que motivo fazer com que as aplicações comuniquem através de um serviço em nuvem em vez de apenas interagir diretamente? A resposta é que comunicar diretamente pode ser mais difícil de que se pensa.

Suponha que pretende ligar duas aplicações no local, ambas em execução dentro dos datacenters empresariais. Cada uma dessas aplicações encontra-se protegida por uma firewall e cada datacenter utiliza provavelmente tradução de endereços de rede (NAT). A firewall bloqueia os dados recebidos em quase todas as portas e NAT significa que a máquina em que se executa cada aplicação não tem um endereço IP fixo ao qual pode aceder diretamente a partir de fora do datacenter. Se não houver ajuda extra, é complicado ligar estas aplicações através da Internet pública.

Um reencaminhamento do Service Bus pode ajudar. Para comunicar de forma bidirecional através de um reencaminhamento, cada aplicação estabelece uma ligação TCP de saída com o Service Bus e mantém-na aberta. Toda a comunicação entre as duas aplicações percorre essas ligações. Uma vez que cada ligação se estabeleceu de dentro do datacenter, a firewall permite o tráfego de entrada para cada aplicação sem abrir novas portas. Esta abordagem também contorna o problema de NAT porque cada aplicação dispõe de um ponto final consistente na nuvem durante toda a duração da comunicação. Ao trocar dados através do reencaminhamento, as aplicações podem evitar os problemas que dificultariam a comunicação. 

Para utilizar os reencaminhamentos do Service Bus, as aplicações baseiam-se no Windows Communication Foundation (WCF). O Service Bus proporciona enlaces WCF que facilitam a interação das aplicações de Windows através dos reencaminhamentos. As aplicações que já utilizam WCF podem normalmente especificar um destes enlaces e depois comunicar entre si através de um reencaminhamento. Todavia, ao contrário das filas e tópicos, a utilização de reencaminhamentos a partir de aplicações que não sejam de Windows, ainda que possível, necessita de algum esforço de programação; não são fornecidas bibliotecas padrão.

Ao contrário do que acontece com as filas e tópicos, as aplicações não criam reencaminhamentos de forma explícita. Quando uma aplicação que pretende receber mensagens estabelece uma ligação TCP com o Service Bus, é criado automaticamente um reencaminhamento. Este é eliminado quando se abandona a ligação. Para que uma aplicação encontre o reencaminhamento criado por um serviço de escuta específico, o Service Bus fornece um registo que permite às aplicações localizar um reencaminhamento específico por nome.

Os reencaminhamentos são a solução certa quando precisa de comunicação direta entre aplicações. Por exemplo, imagine um sistema de reserva de uma companhia aérea que se executa num datacenter no local e que tem de ser acedido em quiosques de check-in, dispositivos móveis e outros computadores. As aplicações que se executam em todos estes sistemas poderiam depender dos reencaminhamentos do Service Bus na nuvem para comunicar, independentemente do local em que estão em execução.

## <a name="summary"></a>Resumo

A ligação de aplicações sempre fez parte da criação de soluções completas e a variedade de cenários que precisam que as aplicações e os serviços comuniquem entre si aumentará logo que mais aplicações e dispositivos se liguem à Internet. Ao fornecer tecnologias baseadas na nuvem para comunicar através de filas, tópicos e reencaminhamentos, o Service Bus tem como objetivo tornar essa função essencial mais fácil de implementar e mais amplamente disponível.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu as noções básicas sobre o Service Bus do Azure, siga estas ligações para obter mais informação.

* Como utilizar as [filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* Como utilizar os [Tópicos do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* Como utilizar o [Reencaminhamento do Service Bus](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
* [Exemplos do Service Bus](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png

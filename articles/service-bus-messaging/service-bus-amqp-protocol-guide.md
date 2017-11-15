---
title: AMQP 1.0 no guia de protocolo do Service Bus do Azure e do Event Hubs | Microsoft Docs
description: "Guia de protocolo para expressões e a descrição do AMQP 1.0 no Service Bus do Azure e do Event Hubs"
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: clemensv
manager: timlt
editor: 
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: clemensv;hillaryc;sethm
ms.openlocfilehash: 4e1fa9db3b4801103069163c55a9b342a27d00ac
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 no guia de protocolo do Service Bus do Azure e do Event Hubs

1.0 protocolo avançadas de colocação de mensagens é um protocolo de pacotes e transferência padronizado de forma assíncrona, segura e fiável transferir mensagens entre duas partes. É o protocolo de principal de mensagens do Azure Service Bus e Event Hubs do Azure. Ambos os serviços também suportam HTTPS. O protocolo SBMP proprietário, que também é suportado está a ser descontinuado favor AMQP.

AMQP 1.0 é o resultado da colaboração do setor abrangente que colocados em conjunto fornecedores middleware, tais como Microsoft e Red Hat, com muitos utilizadores middleware mensagens, tais como JP Morgan Chase que representa a indústria de serviços financeiros. O Fórum uniformização técnica para as especificações de protocolo e a extensão AMQP é OASIS e tenha conseguido aprovação formal como um padrão internacional como ISO/IEC 19494.

## <a name="goals"></a>Objetivos

Este artigo resumidamente resume os conceitos principais do AMQP 1.0 mensagens especificação juntamente com um pequeno conjunto de especificações de extensão de rascunho que atualmente estão a ser finalizada no AD técnica OASIS AMQP e explica como o Service Bus do Azure implementa e baseia-se nestas especificações.

O objetivo é para qualquer programador utilizando qualquer pilha de cliente AMQP 1.0 existente em qualquer plataforma ser capazes de interagir com o Azure Service Bus através de AMQP 1.0.

Comuns pilhas AMQP 1.0 para fins gerais, tais como o Apache Proton ou AMQP.NET Lite, já implementam todos os protocolos de núcleos AMQP 1.0. Esses gestos básico, por vezes, são moldados com uma API de nível mais elevada; Apache Proton mesmo oferece dois, a API do Messenger imperativo e a API de Reactor reativa.

No debate seguinte, vamos partem do princípio de que a gestão de ligações, sessões e ligações AMQP e o processamento de moldura transferências e controlo de fluxo são processadas pela pilha de respetiva (como o Apache Proton-C) e não necessitam de atenção muito se qualquer específica aos programadores de aplicações. Modo abstrato presumimos a existência de alguns API primitivos como a capacidade para ligar e criar alguma forma de *remetente* e *recetor* objetos de abstração, que depois de ter alguma forma de `send()` e `receive()` operações, respetivamente.

Quando debater capacidades avançadas do Service Bus do Azure, tal como navegação na mensagem ou gestão de sessões, essas funcionalidades são explicadas em termos AMQP, mas também como uma pseudo-implementação em camadas sobre esta abstração assumida de API.

## <a name="what-is-amqp"></a>O que é AMQP?

AMQP é um protocolo de pacotes e transferência. Pacotes significa que fornece a estrutura de fluxos de dados binário que fluem em qualquer direção de uma ligação de rede. A estrutura fornece delineation de distinct blocos de dados, denominado *frames*, para ser trocadas entre as partes ligadas. As capacidades de transferência Certifique-se de que os dois interlocutores em comunicação podem estabelecer uma compreensão partilhada sobre quando frames deverá ser transferidos e, quando as transferências deverá ser consideradas concluídas.

Ao contrário das versões de anteriormente expirada rascunho produzidas pelo grupo de trabalho de AMQP que ainda estão em utilização por alguns mediadores de mensagens, protocolo do grupo de trabalho final e padronizado AMQP 1.0 não recomende uma presença de um mediador de mensagens ou qualquer topologia específica para entidades dentro de um mediador de mensagens.

O protocolo pode ser utilizado para comunicação de ponto-a-ponto simétrica, para interação com mediadores de mensagens que suportam a filas e publicar/subscrever entidades, como sucede do Service Bus do Azure. Também pode ser utilizado para interação com a infraestrutura de mensagens onde os padrões de interação são diferentes das filas regulares, como é o caso de Event Hubs do Azure. Um Hub de eventos funciona como uma fila, quando os eventos são enviados para o mesmo, mas mais funciona como um serviço de armazenamento série quando os eventos são lidos a partir da mesma; um pouco assemelha-se uma unidade de banda. O cliente escolhe um desvio para o fluxo de dados disponíveis e, em seguida, é fornecido a todos os eventos de que o desvio para o mais recente disponível.

O protocolo AMQP 1.0 foi concebido para ser extensível, ativar a especificações melhorar as respetivas capacidades. As especificações de três extensão abordadas neste documento ilustram isto. Para a comunicação através da infraestrutura existente do HTTPS/WebSockets onde configurar as portas AMQP TCP nativas pode ser difícil, uma especificação de enlace define como camada AMQP através de WebSockets. Para interagir com a infraestrutura de mensagens de uma forma de pedido/resposta para efeitos de gestão ou para fornecer a funcionalidade avançada, a especificação de gestão AMQP define as primitivas interação básica necessária. Para a integração do modelo de autorização federado, a especificação de afirmações com base-segurança AMQP define como associar e renovar associados com ligações de tokens de autorização.

## <a name="basic-amqp-scenarios"></a>Cenários AMQP básicos

Esta secção explica a utilização básica de AMQP 1.0 com o Service Bus do Azure, que inclui a criação de ligações, sessões e ligações e transferir as mensagens de e entidades do Service Bus, tais como filas, tópicos e subscrições.

A origem mais autoritativa para saber mais sobre como funciona o AMQP é a especificação de AMQP 1.0, mas a especificação foi escrita para precisamente guia de implementação e não para lhe ensinar o protocolo. Esta secção está centrado nas introduzir o mesmo terminologia conforme necessário para descrever como o Service Bus utiliza AMQP 1.0. Para uma introdução mais abrangente para AMQP, bem como ver um debate mais amplo de AMQP 1.0, pode rever [este vídeo decorrer][this video course].

### <a name="connections-and-sessions"></a>As ligações e sessões

AMQP chama os programas comunicar *contentores*; essas conter *nós*, quais são as entidades comunicar dentro desses contentores. Uma fila pode ser um de nós. AMQP permite multiplexação, pelo que uma ligação única pode ser utilizada para vários caminhos de comunicação entre nós; Por exemplo, um cliente de aplicação pode receber de uma fila em simultâneo e enviar para outra fila através da mesma ligação de rede.

![][1]

A ligação de rede, por conseguinte, está ancorada no contentor. É iniciado por contentor na função de cliente, efetuar uma ligação de socket TCP de saída para um contentor na função de recetor, a qual escuta e aceita ligações de TCP de entrada. O handshake de ligação inclui negociar a versão do protocolo declarar ou negociar a utilização de segurança de nível de transporte (TLS/SSL) e um handshake de autenticação/autorização no âmbito de ligação com base no SASL.

Service Bus do Azure requer a utilização de TLS permanente. Suporta ligações através da porta TCP 5671, whereby a ligação de TCP primeiro overlaid com TLS antes de introduzir o handshake de protocolo AMQP e também suporta ligações através da porta TCP 5672 whereby servidor imediatamente oferece uma atualização obrigatória da ligação ao TLS utilizando o modelo prescrita o AMQP. O enlace de AMQP WebSockets cria um túnel através da porta TCP 443, em seguida, é equivalente às ligações AMQP 5671.

Depois de configurar a ligação e TLS, o barramento de serviço oferece duas opções de mecanismo SASL:

* SASL simples é geralmente utilizado para passar as credenciais de nome de utilizador e palavra-passe para um servidor. Barramento de serviço não têm contas, mas com nome [regras de segurança de acesso partilhado](service-bus-sas.md), que confer direitos e estão associados uma chave. O nome de uma regra é utilizado como o nome de utilizador e a chave (como o texto de codificados base64) é utilizado como a palavra-passe. Os direitos associados a regra que escolheu governem as operações permitidas na ligação.
* SASL anónimo é utilizado para ignorar a autorização SASL quando o cliente que pretende utilizar o modelo de (CBS) de segurança afirmações com base em que é descrito posteriormente. Com esta opção, uma cliente pode ser estabelecida ligação anonimamente para um curto período de tempo durante os quais o cliente só pode interagir com o ponto final CBS e tem de concluir o handshake do CBS.

Depois de é estabelecida a ligação de transporte, os contentores declarar o tamanho máximo de moldura que estão dispostos a processar e após um tempo limite de inatividade irá forma unilateral Desligue se não existir nenhuma atividade na ligação.

Podem declarar também são suportados o número de canais em simultâneo. Um canal é um caminho de transferência unidirecionais, saída, virtual por cima da ligação. Uma sessão demora um canal de cada um dos contentores interligados para formar um caminho de comunicação bidirecional.

As sessões têm um modelo de controlo de fluxo baseado no Windows; Quando é criada uma sessão, a cada entidade declara frames quantos que está disposto a aceitar na respetiva janela de receção. Como os frames Jumbo exchange partes, preenchimento transferidos fotogramas que janela e as transferências de parar quando a janela está cheia e até que a janela obtém de reposição ou expandidos utilizando o *fluxo performative* (*performative* é o termo AMQP para gestos ao nível do protocolo trocados entre as duas partes).

Este modelo baseado no Windows é aproximadamente análogo ao conceito TCP do controlo de fluxo baseado no Windows, mas o nível de sessão no interior do socket. Existe conceito do protocolo de permitir que várias sessões em simultâneo para que o tráfego de alta prioridade foi rushed passado otimizado tráfego normal, como numa fila de express de autoestrada.

Service Bus do Azure utiliza atualmente exatamente uma sessão para cada ligação. O Service Bus frame-tamanho máximo é 262.144 bytes (256 K bytes) para padrão de barramento de serviço e do Event Hubs. É 1,048,576 (1 MB) para Premium do Service Bus. Barramento de serviço não impõe qualquer janela de limitação de nível de sessão específica, mas repõe a janela regularmente como parte do controlo de fluxo de nível de ligação (consulte [a secção seguinte](#links)).

Ligações, os canais e sessões estão efémeras. Se a ligação subjacente fecha, ligações, tem possível restabelecer túnel TLS, contexto de autorização de SASL e sessões.

### <a name="links"></a>Ligações

AMQP transfere mensagens através de ligações. Uma ligação é um caminho de comunicação criado ao longo de uma sessão que permite transferir mensagens uma direção; a negociação de estado de transferência está sobre a ligação e bidirecionais entre as partes ligadas.

![][2]

Ligações podem ser criadas por um contentor em qualquer altura e através de uma sessão existente, o que torna o AMQP diferentes de muitos outros protocolos, incluindo HTTP e MQTT, onde o início de transferências e o caminho de transferência é um privilégio exclusivo de terceiros criar a ligação de socket.

O contentor de iniciar a ligação pede-lhe o contentor oposto para aceitar uma ligação e escolhe uma função do remetente ou recetor. Por conseguinte, o contentor pode iniciar a criação de unidirecionais ou caminhos de comunicação bidirecional, com a última opção é modelada como pares de ligações.

As ligações são com o nome e associou connosco. Conforme indicado no início, nós são as entidades comunicar dentro de um contentor.

No Service Bus, um nó é diretamente equivalente a uma fila, um tópico, uma subscrição ou uma subfila de mensagens não entregues de uma fila ou a subscrição. O nome de nó utilizado no AMQP, por conseguinte, é o nome relativo da entidade dentro do espaço de nomes do Service Bus. Se uma fila com o nome **myqueue**, que também é o nome de nó AMQP. Uma subscrição de tópico segue a Convenção de API de HTTP pelo que está a ser ordenados por uma coleção de recursos "subscrições" e, assim, uma subscrição **sub** ou um tópico **mytopic** tem o nome de nó AMQP **mytopic/subscrições/subitens**.

O ligação cliente também é necessário para utilizar um nome de nó local para a criação de ligações; Barramento de serviço não é prescritiva sobre esses nomes de nó e não interpretá-los. Pilhas de cliente AMQP 1.0, geralmente, utilizam um esquema para garantir que estes nomes de nó efémeras são exclusivos no âmbito do cliente.

### <a name="transfers"></a>Transferências

Assim que tiver sido estabelecida uma ligação, mensagens podem ser transferidas através dessa ligação. Em AMQP, uma transferência é executada com um gesto de protocolo explícito (o *transferência* performative) que se move uma mensagem de remetente para recetor através de uma ligação. Uma transferência está concluída quando este é "settled", que significa que ambas as partes têm de estabelecer uma compreensão do resultado de transferência dessa partilhado.

![][3]

No caso mais simples, o remetente pode optar por enviar mensagens "previamente settled", que significa que o cliente não está interessado no resultado e o recetor não fornece quaisquer comentários sobre o resultado da operação. Este modo é suportado pelo Service Bus ao nível do protocolo AMQP, mas não é exposto em qualquer uma das APIs de cliente.

As maiúsculas e minúsculas regular são que as mensagens estão a ser enviadas unsettled e o recetor, em seguida, indica aceitação ou rejeição utilizando o *disposição* performative. Rejeição ocorre quando o recetor não pode aceitar a mensagem por qualquer motivo, e a mensagem de rejeição contém informações sobre o motivo, o que é uma estrutura de erro definida pelo AMQP. Se as mensagens são rejeitadas devido a erros internos no interior do Service Bus, o serviço devolve informações adicionais no interior essa estrutura que pode ser utilizada para fornecer sugestões de diagnóstico ao pessoal de suporte, se a apresentação de pedidos de suporte. Irá aprender mais detalhes sobre os erros mais tarde.

É uma forma especial de rejeição de *lançadas* Estado, que indica que o recetor tem nenhum objection técnica para a transferência, mas também não interesse no settling a transferência. Nesse caso existe, por exemplo, quando uma mensagem é enviada para um cliente do Service Bus e o cliente escolhe "abandone" a mensagem porque não é possível efetuar o trabalho resultantes de processamento da mensagem; a entrega de mensagens em si não é com falha. Uma variação desse Estado é a *modificado* Estado, que permite que as alterações à mensagem, como é libertado. Esse Estado não é utilizado pelo Service Bus no presente.

A especificação de AMQP 1.0 define uma disposição mais Estado chamado *recebido*, especificamente que ajuda a processar a ligação de recuperação. Recuperação de ligação permite reconstituting o estado de uma ligação e quaisquer pendentes entregas por cima de uma nova ligação e a sessão, quando a ligação anterior e a sessão foram perdidos.

Barramento de serviço não suporta a ligação de recuperação; Se o cliente perder a ligação ao Service Bus com uma mensagem unsettled transferência pendente, essa transferência de mensagens é perdida, e o cliente deve voltar a ligar, restabelecer a ligação e repita a transferência.

Como tal, o Service Bus e Hubs de eventos ", pelo menos, uma vez" suportam transferências em que o remetente pode ter a garantia para a mensagem foi armazenada e aceite, mas não suportam "exatamente uma vez" transferências ao nível do AMQP, onde o sistema seria tentar recuperar a ligação e continuar a negociação o estado de entrega para evitar a duplicação de transferência de mensagens.

Para compensar duplicado possíveis envia, Service Bus suporta a deteção duplicada como uma funcionalidade opcional filas e tópicos. Deteção duplicada regista os IDs de mensagem de todas as mensagens a receber durante uma janela de tempo definido pelo utilizador, em seguida, silenciosamente ignora todas as mensagens enviadas com os mesmos IDs de mensagem durante essa mesma janela.

### <a name="flow-control"></a>Controlo de fluxo

Para além do modelo de controlo de fluxo de nível de sessão que discutido anteriormente, cada ligação tem o seu próprio modelo de controlo de fluxo. Controlo de fluxo de nível de sessão protege o contentor de ter de lidar com demasiados frames em depois, controlo de fluxo de nível de ligação coloca a aplicação responsável pela quantidade de mensagens que que processar de uma ligação e quando.

![][4]

Numa ligação, as transferências só podem acontecer quando o remetente tem espaço suficiente *ligação crédito*. Ligação crédito é um contador definido pelo recetor utilizando o *fluxo* performative, que tem um âmbito para uma ligação. Quando o remetente é atribuído o crédito de ligação, este tenta utilizar a cópia de segurança que crédito por entrega de mensagens. Cada é decrementada entrega de mensagens de ligação restantes crédito por 1. Quando o crédito de ligação é utilizado a cópia de segurança, entregas parar.

Quando o Service Bus é na função de recetor, de forma instantânea fornece o remetente com crédito ligação ample, para que as mensagens podem ser enviadas imediatamente. Como é utilizado o crédito de ligação, Service Bus ocasionalmente envia um *fluxo* performative para o remetente para atualizar o saldo do crédito de ligação.

Na função de remetente, o barramento de serviço envia mensagens para utilizar a cópia de segurança qualquer crédito ligação pendentes.

Uma chamada "receber" ao nível da API traduz para um *fluxo* performative a ser enviados para o barramento de serviço pelo cliente e o Service Bus consome esse crédito ao colocar a primeira mensagem disponível, desbloqueada da fila, bloquear e transferi-lo. Se não houver nenhuma mensagem prontamente disponíveis para entrega, qualquer crédito pendente por qualquer ligação estabelecida com que a entidade específica permanece registada por ordem de chegada e mensagens são bloqueadas e transferidas à medida que ficam disponíveis para utilizar qualquer crédito pendente.

O bloqueio de uma mensagem for lançado quando a transferência é settled para um dos Estados de terminais *aceites*, *rejeitado*, ou *lançadas*. A mensagem é removida do Service Bus, quando o estado terminal for *aceites*. Continua a no Service Bus e é transmitido para o recetor seguinte quando a transferência atinge qualquer um dos outros Estados. Barramento de serviço move automaticamente a mensagem na fila de mensagens não entregues a entidade quando chega a contagem de entrega máximo permitida para a entidade devido a rejeições repetidas ou de versões.

Apesar das APIs de barramento de serviço não expõe diretamente essa uma opção hoje, um cliente de protocolo AMQP de nível inferior pode utilizar o modelo de crédito de ligação para ativar a interação de "solicitação-style" de emissão de uma unidade de crédito para cada pedido de receção para um modelo de "push-style" através da emissão de um grande número de créditos de ligação e, em seguida, receber mensagens à medida que ficam disponíveis sem qualquer interação do adicional. Push é suportado através de [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) ou [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount) definições de propriedade. Quando são diferente de zero, o cliente AMQP utiliza-o como o crédito de ligação.

Neste contexto, é importante compreender que o relógio da expiração do bloqueio na mensagem dentro da entidade é iniciado quando a mensagem é retirada da entidade, não quando a mensagem é colocada na rede. Sempre que o cliente indica preparação para receber mensagens ao emitir o crédito de ligação, espera-se, por conseguinte, a ser ativamente a extrair mensagens através da rede e estar pronto para lidar com os mesmos. Caso contrário, o bloqueio de mensagem pode ter expirado antes da mensagem é mesmo ser entregue. A utilização do controlo de fluxo de crédito ligação diretamente deve refletir a prontidão imediata para lidar com disponíveis mensagens distribuídas para o recetor.

Em resumo, as secções seguintes fornecem uma descrição do fluxo performative schematic durante diferentes interações de API. Cada secção descreve uma operação lógica diferente. Alguns desses interações podem ser "lento,", o que significa que só podem ser efetuadas quando necessário. Criar um remetente da mensagem não pode causar uma interação de rede até que a primeira mensagem seja enviada ou pedida.

As setas na tabela a seguir mostram a direcção do fluxo performative.

#### <a name="create-message-receiver"></a>Criar o recetor de mensagem

| Cliente | Service Bus |
| --- | --- |
| --> ligar (<br/>nome = {nome da ligação,}<br/>processar = {identificador numérico,}<br/>função =**recetor**,<br/>origem = {nome da entidade,}<br/>destino = {id de ligação de cliente}<br/>) |Cliente anexa a entidade como recetor |
| Respostas de barramento de serviço a expor o respetivo final da ligação |< – anexar (<br/>nome = {nome da ligação,}<br/>processar = {identificador numérico,}<br/>função =**remetente**,<br/>origem = {nome da entidade,}<br/>destino = {id de ligação de cliente}<br/>) |

#### <a name="create-message-sender"></a>Criar o remetente da mensagem

| Cliente | Service Bus |
| --- | --- |
| --> ligar (<br/>nome = {nome da ligação,}<br/>processar = {identificador numérico,}<br/>função =**remetente**,<br/>origem = {id de ligação de cliente,}<br/>destino = {nome da entidade}<br/>) |Nenhuma ação |
| Nenhuma ação |< – anexar (<br/>nome = {nome da ligação,}<br/>processar = {identificador numérico,}<br/>função =**recetor**,<br/>origem = {id de ligação de cliente,}<br/>destino = {nome da entidade}<br/>) |

#### <a name="create-message-sender-error"></a>Criar o remetente da mensagem (erro)

| Cliente | Service Bus |
| --- | --- |
| --> ligar (<br/>nome = {nome da ligação,}<br/>processar = {identificador numérico,}<br/>função =**remetente**,<br/>origem = {id de ligação de cliente,}<br/>destino = {nome da entidade}<br/>) |Nenhuma ação |
| Nenhuma ação |< – anexar (<br/>nome = {nome da ligação,}<br/>processar = {identificador numérico,}<br/>função =**recetor**,<br/>origem = null,<br/>destino = null<br/>)<br/><br/>< – desanexar (<br/>processar = {identificador numérico,}<br/>fechado =**verdadeiro**,<br/>Erro = {informações de erro}<br/>) |

#### <a name="close-message-receiversender"></a>Recetor/remetente da mensagem de fecho

| Cliente | Service Bus |
| --- | --- |
| --> desanexar (<br/>processar = {identificador numérico,}<br/>fechado =**verdadeiro**<br/>) |Nenhuma ação |
| Nenhuma ação |< – desanexar (<br/>processar = {identificador numérico,}<br/>fechado =**verdadeiro**<br/>) |

#### <a name="send-success"></a>Enviar (êxito)

| Cliente | Service Bus |
| --- | --- |
| --> (de transferência<br/>id de entrega = {identificador numérico,}<br/>etiqueta de entrega = {identificador binário,}<br/>settled =**falso**, mais =**falso**,<br/>estado =**nulo**,<br/>retomar =**false**<br/>) |Nenhuma ação |
| Nenhuma ação |< – disposição (<br/>função = recetor,<br/>primeiro = {id de entrega}<br/>última = {id de entrega}<br/>settled =**verdadeiro**,<br/>estado =**aceite**<br/>) |

#### <a name="send-error"></a>Enviar (erro)

| Cliente | Service Bus |
| --- | --- |
| --> (de transferência<br/>id de entrega = {identificador numérico,}<br/>etiqueta de entrega = {identificador binário,}<br/>settled =**falso**, mais =**falso**,<br/>estado =**nulo**,<br/>retomar =**false**<br/>) |Nenhuma ação |
| Nenhuma ação |< – disposição (<br/>função = recetor,<br/>primeiro = {id de entrega}<br/>última = {id de entrega}<br/>settled =**verdadeiro**,<br/>estado =**rejeitado**(<br/>Erro = {informações de erro}<br/>)<br/>) |

#### <a name="receive"></a>Receber

| Cliente | Service Bus |
| --- | --- |
| --> fluxo (<br/>ligação-crédito = 1<br/>) |Nenhuma ação |
| Nenhuma ação |< transferência (<br/>id de entrega = {identificador numérico,}<br/>etiqueta de entrega = {identificador binário,}<br/>settled =**falso**,<br/>mais =**falso**,<br/>estado =**nulo**,<br/>retomar =**false**<br/>) |
| --> disposição (<br/>função =**recetor**,<br/>primeiro = {id de entrega}<br/>última = {id de entrega}<br/>settled =**verdadeiro**,<br/>estado =**aceite**<br/>) |Nenhuma ação |

#### <a name="multi-message-receive"></a>Receber mensagens multi

| Cliente | Service Bus |
| --- | --- |
| --> fluxo (<br/>ligação-crédito = 3<br/>) |Nenhuma ação |
| Nenhuma ação |< transferência (<br/>id de entrega = {identificador numérico,}<br/>etiqueta de entrega = {identificador binário,}<br/>settled =**falso**,<br/>mais =**falso**,<br/>estado =**nulo**,<br/>retomar =**false**<br/>) |
| Nenhuma ação |< transferência (<br/>id de entrega = {identificador numérico + 1},<br/>etiqueta de entrega = {identificador binário,}<br/>settled =**falso**,<br/>mais =**falso**,<br/>estado =**nulo**,<br/>retomar =**false**<br/>) |
| Nenhuma ação |< transferência (<br/>id de entrega = {identificador numérico + 2},<br/>etiqueta de entrega = {identificador binário,}<br/>settled =**falso**,<br/>mais =**falso**,<br/>estado =**nulo**,<br/>retomar =**false**<br/>) |
| --> disposição (<br/>função = recetor,<br/>primeiro = {id de entrega}<br/>última = {id de entrega + 2}<br/>settled =**verdadeiro**,<br/>estado =**aceite**<br/>) |Nenhuma ação |

### <a name="messages"></a>Mensagens

As secções seguintes explicam as propriedades das secções de mensagem AMQP standard são utilizadas pelo Service Bus e a forma como efectuam o mapeamento para o conjunto de API do Service Bus.

#### <a name="header"></a>cabeçalho

| Nome do campo | Utilização | Nome da API |
| --- | --- | --- |
| durável |- |- |
| prioridade |- |- |
| valor de TTL |TTL para esta mensagem |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| primeiro acquirer |- |- |
| Contagem de entrega |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>propriedades

| Nome do campo | Utilização | Nome da API |
| --- | --- | --- |
| id da mensagem |Identificador definido pela aplicação, de forma gratuita para esta mensagem. Utilizado para a deteção de duplicados. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| id de utilizador |Identificador de utilizador definido pela aplicação, não interpretado pelo Service Bus. |Não está acessível através da API de barramento de serviço. |
| para |Identificador de destino definido pela aplicação, não interpretado pelo Service Bus. |[Para](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| Requerente |Identificador de objetivo do mensagem definidas pela aplicação, não interpretado pelo Service Bus. |[Etiqueta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| a resposta |Indicador de resposta-caminho definido pela aplicação, não interpretado pelo Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| id de correlação |Identificador de correlação definido pela aplicação, não interpretado pelo Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| tipo de conteúdo |Indicador definido pela aplicação de um tipo de conteúdo para o corpo, não interpretado pelo Service Bus. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| codificação de conteúdo |Definido pela aplicação codificação de conteúdo indicador para o corpo, não interpretado pelo Service Bus. |Não está acessível através da API de barramento de serviço. |
| hora de expiração absoluto |Declara no qual absoluto instantânea a mensagem de expira. Ignorado na entrada (cabeçalho TTL é observado) autoritativo no resultado. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| hora de criação |Declara em que momento a mensagem foi criada. Não é utilizado pelo Service Bus |Não está acessível através da API de barramento de serviço. |
| id do grupo |Identificador definido pela aplicação para um conjunto de mensagens relacionado. Utilizado para sessões de Service Bus. |[ID de sessão](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| sequência de grupo |Contador que identifica o número de sequência relativo a mensagem dentro de uma sessão. Ignorado pelo Service Bus. |Não está acessível através da API de barramento de serviço. |
| Responda ao grupo-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

## <a name="advanced-service-bus-capabilities"></a>Capacidades avançadas de Service Bus

Esta secção abrange as capacidades avançadas do Service Bus do Azure que são baseadas em extensões de rascunho para AMQP, atualmente a ser desenvolvidas no AD técnica OASIS para AMQP. Barramento de serviço implementa as versões mais recentes destes rascunhos e adopts alterações introduzidas como esses rascunhos alcancem estado padrão.

> [!NOTE]
> Mensagens do Service Bus são suportadas operações avançadas através de um padrão de pedido/resposta. Os detalhes destas operações são descritos no artigo [AMQP 1.0 no Service Bus: operações de pedido-resposta baseados em](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Gestão de AMQP

A especificação de gestão AMQP é o primeiro as extensões de rascunho abordadas neste artigo. Esta especificação define um conjunto de protocolos em camadas sobre o protocolo AMQP que permite interações de gestão com a infraestrutura de mensagens através de AMQP. A especificação define operações genéricas como *criar*, *ler*, *atualizar*, e *eliminar* para a gestão de entidades no interior de uma infraestrutura de mensagens e um conjunto de operações de consulta.

Todas essas gestos exigem a interação entre o cliente e a infraestrutura de mensagens para um pedido/resposta e, por conseguinte, a especificação define como modelo padrão que interação em cima AMQP: o cliente liga-se para a infraestrutura de mensagens, inicia uma sessão e, em seguida, cria um par de ligações. Uma ligação, o cliente funciona como remetente no outro atua como recetor, assim, criar um par de ligações que podem atuar como um canal bidirecional.

| Funcionamento lógico | Cliente | Service Bus |
| --- | --- | --- |
| Criar o caminho de resposta do pedido |--> ligar (<br/>nome = {*nome da hiperligação*},<br/>processar = {*identificador numérico*},<br/>função =**remetente**,<br/>origem =**nulo**,<br/>destino = "gestão myentity / $"<br/>) |Nenhuma ação |
| Criar o caminho de resposta do pedido |Nenhuma ação |\<-anexar (<br/>nome = {*nome da hiperligação*},<br/>processar = {*identificador numérico*},<br/>função =**recetor**,<br/>origem = null,<br/>destino = "myentity"<br/>) |
| Criar o caminho de resposta do pedido |--> ligar (<br/>nome = {*nome da hiperligação*},<br/>processar = {*identificador numérico*},<br/>função =**recetor**,<br/>origem = "gestão myentity / $",<br/>destino = "id de $myclient"<br/>) | |
| Criar o caminho de resposta do pedido |Nenhuma ação |\<-anexar (<br/>nome = {*nome da hiperligação*},<br/>processar = {*identificador numérico*},<br/>função =**remetente**,<br/>origem = "myentity"<br/>destino = "id de $myclient"<br/>) |

Ter que par de ligações no local, a implementação de pedido/resposta é simples: um pedido é uma mensagem enviada para uma entidade dentro da infraestrutura de mensagens que compreende este padrão. Nessa mensagem de pedido, o *responder a* campo no *propriedades* secção está definida como o *destino* identificador para a ligação para o qual pretende disponibilizar a resposta. A entidade de processamento processa o pedido e, em seguida, fornece a resposta através da ligação cujo *destino* identificador corresponde a indicados *responder a* identificador.

O padrão obviamente requer que o contentor de cliente e o identificador gerados pelo cliente para o destino de resposta são exclusivos em todos os clientes e, por motivos de segurança, também difícil de prever.

As trocas de mensagens utilizadas para o protocolo de gestão e para todos os outros protocolos que utilizam o mesmo padrão ocorrem ao nível da aplicação; não podem definir gestos de nível de protocolo AMQP novo. Que é intencional, para que as aplicações podem beneficiar imediata destas extensões com compatíveis pilhas de AMQP 1.0.

Barramento de serviço não implementa atualmente qualquer uma das funcionalidades principais da especificação de gestão, mas o padrão de pedido/resposta definido pela especificação de gestão é básico para a funcionalidade de segurança afirmações com base e para quase todas as capacidades avançadas de abordados nas secções seguintes.

### <a name="claims-based-authorization"></a>Autorização baseada em afirmações

O rascunho de especificação AMQP autorização afirmações com base em (CBS) baseia-se o padrão de pedido/resposta de especificação de gestão e descreve um modelo generalizado como utilizar os tokens de segurança federada com AMQP.

O modelo de segurança predefinidos de AMQP debatido na introdução baseia-se no SASL e integra-se com o handshake de ligação AMQP. Utilizar SASL tem a vantagem que fornece um modelo extensível para o qual um conjunto de mecanismos foram definidas a partir de qualquer protocolo de formally leans no SASL beneficiar. Desses mecanismos são "Simples" para a transferência de nomes de utilizador e palavras-passe, "EXTERNAL" para o enlace de segurança de nível de TLS, "Anónimo", para expressar a ausência de autenticação/autorização explícita e numa variedade abrangente de mecanismos adicionais que permitir a passagem de autenticação e/ou as credenciais de autorização ou tokens.

Integração de SASL do AMQP tem dois desvantagens:

* Todas as credenciais e tokens passam para a ligação. Uma infraestrutura de mensagens é útil fornecer controlo de acesso diferenciadas numa base por entidade; Por exemplo, permitindo que o portador de um token enviar para a fila A, mas não para a fila B. Com o contexto de autorização ancorado na ligação, não é possível utilizar uma ligação única e ainda Utilize tokens de acesso diferentes para a fila A e a fila B.
* Os tokens de acesso, normalmente, só são válidos durante um período de tempo limitado. Este validade exige que o utilizador ao readquirir periodicamente tokens e fornece uma oportunidade para o emissor de token para refuse emitir um token de raiz, se tem alterado as permissões de acesso do utilizador. Ligações AMQP poderão última durante longos períodos de tempo. O modelo SASL apenas fornece uma oportunidade para definir um token ao tempo de ligação, o que significa que a infraestrutura de mensagens que quer tem de desligar o cliente quando o token expira ou tem de aceitar o risco ao permitir que a comunicação contínua com um cliente que tenha direitos de acesso tiverem sido revogados no provisório.

A especificação de AMQP CBS implementada pelo Service Bus, ativa uma solução elegante para ambos os problemas: permite que um cliente para associar os tokens de acesso a cada nó e atualizar esses tokens antes de expirarem, sem interromper o fluxo de mensagens.

CBS define um nó virtual de gestão, com o nome *$cbs*, devem ser fornecidas pela infraestrutura de mensagens. O nó de gestão aceita tokens em nome de outros nós na infraestrutura de mensagens.

O gesto de protocolo é uma troca de pedido/resposta conforme definido pela especificação de gestão. Que significa que o cliente estabelecer um par de ligações com o *$cbs* nó e, em seguida, transmite um pedido de ligação de saída e, em seguida, aguarda que a resposta da ligação de entrada.

A mensagem de pedido tem as seguintes propriedades da aplicação:

| Chave | Opcional | Tipo de valor | Conteúdo de valor |
| --- | --- | --- | --- |
| operação |Não |Cadeia |**token de PUT** |
| tipo |Não |Cadeia |O tipo de token a put. |
| nome |Não |Cadeia |O "público-alvo" aos quais se aplica o token. |
| expiração |Sim |carimbo de data/hora |A hora de expiração do token. |

O *nome* propriedade identifica a entidade ao qual o token deverá ser associado. Barramento de serviço é o caminho para a fila ou a subscrição do tópico. O *tipo* propriedade identifica o tipo de token:

| Tipo de token | Descrição de token | Tipo de corpo | Notas |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web tokens (JWT) |Valor de AMQP (cadeia) |Ainda não está disponível. |
| amqp:swt |Token de Web simples (SWT) |Valor de AMQP (cadeia) |Só é suportada para os tokens SWT emitidos por AAD/ACS |
| servicebus.Windows.NET:sastoken |Token SAS do barramento de serviço |Valor de AMQP (cadeia) |- |

Tokens confer direitos. Barramento de serviço conhece três direitos fundamentais: "Enviar" permite o envio, "Escuta" permite a receção e "Gerir" permite a manipulação de entidades. Os tokens SWT emitidos por AAD/ACS explicitamente incluem esses direitos como afirmações. Os tokens SAS do barramento de serviço Consulte regras configuradas no espaço de nomes ou entidade e essas regras são configuradas com direitos. O token de assinatura com a chave associada essa regra, por conseguinte, faz com que o token rápida os respetivos direitos. O token associado uma entidade utilizando *put token* permite que o cliente ligado para interagir com a entidade pelos direitos de token. Uma ligação em que o cliente demora no *remetente* função necessita de "envio" à direita; colocar *recetor* requer a função "Escuta" à direita.

A mensagem de resposta tem o seguinte *propriedades da aplicação* valores

| Chave | Opcional | Tipo de valor | Conteúdo de valor |
| --- | --- | --- | --- |
| código de estado |Não |Int |Código de resposta HTTP **[RFC2616]**. |
| Descrição de estado |Sim |Cadeia |Descrição do Estado. |

O cliente pode chamar *put token* repetidamente e para qualquer entidade na infraestrutura de mensagens. Os tokens são um âmbito para o cliente atual e ancorados na ligação atual, que significa que o servidor ignora quaisquer tokens retidos quando ignora a ligação.

A implementação atual do Service Bus só permite CBS em conjunto com o método SASL "Anónimo". Uma ligação SSL/TLS tem de existir sempre antes do handshake SASL.

O mecanismo de anónimo, por conseguinte, deve ser suportado pelo cliente AMQP 1.0 que escolheu. Acesso anónimo significa que o handshake de ligação inicial, incluindo a criação da sessão inicial ocorre sem o Service Bus saber quem está a criar a ligação.

Assim que a ligação e a sessão for estabelecida, ligando as ligações para o *$cbs* nós e enviar o *put token* o só é permitidas operações de pedido. Um token válido tem de ser definido com êxito utilizando um *put token* pedido para alguns nós entidade dentro de 20 segundos depois da ligação for estabelecida, caso contrário, a ligação é forma unilateral ignorada pelo Service Bus.

O cliente é subsequentemente responsável para controlar expiração do token. Quando um token expira, o Service Bus retomadas rapidamente ignora todas as ligações na ligação para a respetiva entidade. Para evitar esta situação, o cliente pode substituir o token para o nó com uma nova em qualquer altura através de virtual *$cbs* o nó de gestão com o mesmo *put token* multitoque e sem obter in the way of do payload de tráfego que flui em ligações de diferentes.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre AMQP, visite as seguintes ligações:

* [Descrição geral do Service Bus AMQP]
* [Suporte de AMQP 1.0 para tópicos e filas do Service Bus particionada]
* [AMQP no barramento de serviço para o Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Descrição geral do Service Bus AMQP]: service-bus-amqp-overview.md
[Suporte de AMQP 1.0 para tópicos e filas do Service Bus particionada]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP no barramento de serviço para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

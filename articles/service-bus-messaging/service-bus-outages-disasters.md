---
title: "Insulating aplicações de Service Bus do Azure contra falhas e desastres inesperados | Microsoft Docs"
description: "Técnicas para proteger as aplicações em relação a uma potencial falha de Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fd9fa8ab-f4c4-43f7-974f-c876df1614d4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: 6dd9045d7aa8d4dc8b3a1acbe6f927e232d9b505
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Melhores práticas para insulating aplicações contra falhas de Service Bus e desastres inesperados

Aplicações fundamentais tem de operar continuamente, mesmo na presença de falhas não planeadas ou desastres inesperados. Este tópico descreve as técnicas que pode utilizar a proteger as aplicações de Service Bus contra um potencial interrupção do serviço ou um desastre.

Uma falha é definida como indisponibilidade temporária do Service Bus do Azure. A interrupção pode afetar alguns componentes do Service Bus, como um arquivo de mensagens ou mesmo o todo o datacenter. Depois de corrigir o problema, o barramento de serviço torna-se disponíveis novamente. Normalmente, uma falha não irá causar perda de mensagens ou outros dados. Um exemplo de uma falha de componente é indisponibilidade de um determinado arquivo de mensagens. Um exemplo de uma falha em todo o Centro de dados é uma falha de energia do Centro de dados ou um comutador de rede do datacenter defeituoso. Para alguns dias a uma falha pode última de alguns minutos.

Um desastre é definido como a perda permanente de uma unidade de escala do Service Bus ou centro de dados. O Centro de dados poderá ou poderá não ficar disponível novamente. Normalmente, um desastre provoca a perda de algumas ou todas as mensagens ou outros dados. Exemplos de perante desastres são fire, sobrecarregar ou terramoto.

## <a name="current-architecture"></a>Arquitetura atual
O Service Bus utiliza vários arquivos de mensagens para armazenar as mensagens que são enviadas para as filas ou tópicos. Uma fila não particionadas ou um tópico é atribuído a um arquivo de mensagens. Se este arquivo de mensagens não estiver disponível, todas as operações nessa fila ou um tópico irão falhar.

Todas as entidades de mensagens Service Bus (filas, tópicos, reencaminhamentos) residirem no espaço de nomes de serviço, o que está afiliado a um centro de dados. Barramento de serviço não ativar a georreplicação automática de dados nem permite um espaço de nomes abranger vários centros de dados.

## <a name="protecting-against-acs-outages"></a>Proteger contra falhas de ACS
Se estiver a utilizar credenciais de ACS e dos ACS ficar indisponível, os clientes já não podem obter tokens. Clientes que tenham um token no momento que ACs ficar inativo podem continuar a utilizar o Service Bus até que os tokens de expirarem. A duração do token predefinido é 3 horas.

Para proteger contra falhas de ACS, utilize tokens de assinatura de acesso partilhado (SAS). Neste caso, o cliente efetua a autenticação diretamente com o Service Bus ao iniciar um token de Self-minted com uma chave secreta. Chamadas para ACS já não são necessárias. Para mais informações sobre os tokens SAS, consulte [autenticação do Service Bus][Service Bus authentication].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Proteger a filas e tópicos contra falhas do arquivo de mensagens
Uma fila não particionadas ou um tópico é atribuído a um arquivo de mensagens. Se este arquivo de mensagens não estiver disponível, todas as operações nessa fila ou um tópico irão falhar. Uma fila particionada, é composta por outro lado, fragmentos vários. Cada fragmento é armazenado num arquivo de mensagens diferentes. Quando é enviada uma mensagem para uma fila particionada ou um tópico, o Service Bus atribui a mensagem para um dos fragmentos. Se o arquivo de mensagens correspondente estiver disponível, Service Bus escreve a mensagem para um fragmento diferentes, se possível. Para obter mais informações sobre entidades particionadas, consulte [entidades de mensagens Particionadas][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Proteger contra falhas do Centro de dados ou perante desastres
Para permitir uma ativação pós-falha entre dois centros de dados, pode criar um espaço de nomes de serviço do Service Bus em cada datacenter. Por exemplo, o Service Bus serviço espaço de nomes **contosoPrimary.servicebus.windows.net** pode estar localizada na região Centro-Norte/dos Estados Unidos, e **contosoSecondary.servicebus.windows.net**pode estar localizada na região dos EUA Sul/Central. Se uma entidade de mensagens do Service Bus tem de permanecer acessível na presença de uma falha de centro de dados, pode criar essa entidade em ambos os espaços de nomes.

Para obter mais informações, consulte a secção "Falha do Service Bus dentro de um datacenter do Azure" [padrões e elevada disponibilidade de mensagens assíncronas][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Proteger pontos finais de reencaminhamento contra falhas do Centro de dados ou perante desastres
Replicação geográfica dos pontos finais de reencaminhamento permite que um serviço que expõe um ponto final de reencaminhamento estar acessível na presença de falhas de Service Bus. Para alcançar a georreplicação, o serviço tem de criar dois pontos finais de reencaminhamento em diferentes espaços de nomes. Os espaços de nomes tem de residir em datacenters diferentes e os dois pontos finais tem de ter nomes diferentes. Por exemplo, um ponto final principal pode ser contactado em **contosoPrimary.servicebus.windows.net/myPrimaryService**, enquanto o respetivo homólogo secundário pode ser contactado em **contosoSecondary.servicebus.windows.net /mySecondaryService**.

O serviço de escuta, em seguida, em ambos os pontos finais e um cliente pode invocar o serviço através de um ponto final. Uma aplicação cliente aleatoriamente escolhe um dos reencaminhamentos como o ponto final principal e envia o pedido para o ponto final do Active Directory. Se a operação falhar com um código de erro, esta falha indica que o ponto final de reencaminhamento não está disponível. A aplicação abre-se de um canal para o ponto final de cópia de segurança e reissues o pedido. Nesse momento o Active Directory e os pontos finais de cópia de segurança mudar funções: a aplicação cliente considera o antigo ponto final do Active Directory para o novo ponto final da cópia de segurança e o ponto de final cópia de segurança antigo para o novo ponto final do Active Directory. Se ambos enviar operações falhar, as funções de duas entidades permanecem inalteradas e é devolvido um erro.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Proteger a filas e tópicos contra falhas do Centro de dados ou perante desastres
Para obter com a resiliência contra falhas do Centro de dados utilizando as mensagens mediadas, o Service Bus suporta duas abordagens: *Active Directory* e *passivo* replicação. Para cada abordagem, se uma determinada fila ou um tópico tem de permanecer acessível na presença de uma falha de centro de dados, pode criar-em ambos os espaços de nomes. Ambas as entidades podem ter o mesmo nome. Por exemplo, uma fila primária pode ser contactada em **contosoPrimary.servicebus.windows.net/myQueue**, enquanto o respetivo homólogo secundário pode ser contactado em **contosoSecondary.servicebus.windows.net/myQueue**.

Se a aplicação não necessitar de comunicação do remetente para recetor permanente, a aplicação pode implementar uma fila do lado do cliente durável para evitar a perda de mensagem e para proteger o remetente quaisquer erros transitórios do Service Bus.

## <a name="active-replication"></a>Replicação de Active Directory
Replicação de Active Directory utiliza entidades em ambos os espaços de nomes para cada operação. Qualquer cliente que envia uma mensagem envia duas cópias da mesma mensagem. A primeira cópia é enviada à entidade principal (por exemplo, **contosoPrimary.servicebus.windows.net/sales**), e a segunda cópia da mensagem é enviada para a entidade secundária (por exemplo,  **contosoSecondary.servicebus.windows.net/sales**).

Um cliente recebe mensagens de ambas as filas. O recetor processar a primeira cópia de uma mensagem e a segunda cópia é suprimida. Para suprimir mensagens duplicadas, o remetente tem de etiquetar cada mensagem com um identificador exclusivo. Ambas as cópias da mensagem têm de ser etiquetadas com o mesmo identificador. Pode utilizar o [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] ou [BrokeredMessage.Label] [ BrokeredMessage.Label] propriedades ou uma propriedade personalizada para marcar a mensagem. O recetor tem de manter uma lista de mensagens que o se já tiver recebido.

O [georreplicação com mensagens mediadas do barramento do serviço] [ Geo-replication with Service Bus Brokered Messages] exemplo demonstra a replicação de Active Directory de entidades de mensagens.

> [!NOTE]
> A abordagem de replicação do Active Directory duplica o número de operações, pelo que esta abordagem pode levar ao custo mais elevado.
> 
> 

## <a name="passive-replication"></a>Replicação passiva
No caso de falhas-gratuita, a replicação passiva utiliza apenas uma das duas entidades de mensagens. Um cliente envia a mensagem para a entidade de Active Directory. Se a operação na entidade Active Directory falhar com um código de erro que indica o datacenter que aloja a entidade de Active Directory poderão não estar disponível, o cliente envia uma cópia da mensagem para a entidade de cópia de segurança. Nesse momento o Active Directory e as entidades de cópia de segurança mudar funções: o cliente de envio considera que a entidade de Active Directory antiga para ser a entidade de cópia de segurança novo e a entidade de cópia de segurança antiga é a nova entidade Active Directory. Se ambos enviar operações falhar, as funções de duas entidades permanecem inalteradas e é devolvido um erro.

Um cliente recebe mensagens de ambas as filas. Porque não há a possibilidade de que o recetor recebe duas cópias da mesma mensagem, o recetor têm suprimir mensagens duplicadas. Pode suprimir os duplicados da mesma forma, conforme descrito para replicação de Active Directory.

Em geral, replicação passiva é mais económica que a replicação do Active Directory porque na maioria dos casos é executada apenas uma operação. Custo monetário, o débito e latência são idênticos para o cenário de não-replicados.

Quando utilizar replicação passiva, nos seguintes cenários de mensagens podem ser perdidas ou recebidas duas vezes:

* **Atraso de mensagem ou perda**: partem do princípio de que o remetente enviou com êxito uma mensagem m1 para a fila principal e, em seguida, a fila fica indisponível antes do recetor recebe m1. O remetente envia uma mensagem subsequentes m2 para a fila secundária. Se a fila principal está temporariamente indisponível, o recetor recebe m1 depois da fila de fica disponível novamente. Em caso de desastre, o recetor nunca poderá receber m1.
* **Duplicar receção**: partem do princípio de que o remetente envia uma mensagem m para a fila principal. Barramento de serviço com êxito processa m, mas não conseguir enviar uma resposta. Depois da operação de envio exceder o tempo limite, o remetente envia uma cópia idêntica m para a fila secundária. Se o recetor é capaz de receber a primeira cópia de m antes da fila principal fica indisponível, o recetor recebe ambas as cópias de m aproximadamente à mesma hora. Se o recetor não é possível receber a primeira cópia de m antes da fila principal fica indisponível, o recetor inicialmente recebe a segunda cópia da m, mas, em seguida, recebe uma segunda cópia da m quando a fila principal fica disponível.

O [georreplicação com o Service Bus as mensagens mediadas] [ Geo-replication with Service Bus Brokered Messages] exemplo demonstra replicação passiva de entidades de mensagens.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a recuperação após desastre, consulte estes artigos:

* [Continuidade de negócios de base de dados SQL do Azure][Azure SQL Database Business Continuity]
* [Ao conceber aplicações resilientes para o Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

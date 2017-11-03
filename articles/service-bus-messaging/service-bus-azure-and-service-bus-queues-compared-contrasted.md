---
title: As filas de armazenamento do Azure e filas do Service Bus - comparados e contrasted | Microsoft Docs
description: "Analisa as diferenças e semelhanças entre dois tipos de filas oferecidas pelo Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: tysonn
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/07/2017
ms.author: sethm
ms.openlocfilehash: 555759073507219188b59af76a82be74b112c57c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>As filas de armazenamento e de filas do Service Bus - comparados e contrasted
Este artigo analisa as diferenças e semelhanças entre os dois tipos de filas oferecidas pelo Microsoft Azure hoje: as filas de armazenamento e de filas do Service Bus. A utilização destas informações permite-lhe comparar e contrastar as respetivas tecnologias, e tomar uma decisão mais informada quanto à solução que melhor responde às suas necessidades.

## <a name="introduction"></a>Introdução
Azure suporta dois tipos de mecanismos de fila: **as filas de armazenamento** e **filas do Service Bus**.

**As filas de armazenamento**, que fazem parte do [storage do Azure](https://azure.microsoft.com/services/storage/) infraestrutura, funcionalidade uma interface de acesso baseado em REST GET/PUT/observar simples, fornecendo fiável, persistente mensagens dentro e entre os serviços.

**Filas do Service Bus** fazem parte de um mais ampla [mensagens do Azure](https://azure.microsoft.com/services/service-bus/) infraestrutura que suporta a colocação em fila, bem como de publicação/subscrição e, mais avançadas padrões de integração. Para obter mais informações sobre as filas/tópicos/subscrições do Service Bus, consulte o [descrição geral do Service Bus](service-bus-messaging-overview.md).

Embora ambas as tecnologias de colocação existirem em simultâneo, as filas de armazenamento foram introduzidas em primeiro lugar, como um mecanismo de armazenamento dedicado fila desenvolvido com serviços de armazenamento do Azure. Filas do Service Bus são criadas com base a infraestrutura de "mensagens" mais ampla concebida para integrar aplicações ou componentes da aplicação que podem abranger vários protocolos de comunicação, contratos de dados, os domínios de confiança, e/ou ambientes de rede.

## <a name="technology-selection-considerations"></a>Considerações de seleção de tecnologia
As filas de armazenamento e de filas do Service Bus são implementações da mensagem de colocação em fila de serviço atualmente disponibilizado no Microsoft Azure. Cada um tem um conjunto de funcionalidades ligeiramente diferentes, o que significa que pode escolher um ou outro, ou ambos, consoante as necessidades da sua solução específica ou um problema de negócio/técnica que são resolver.

Ao determinar o objetivo de uma determinada solução se adequa a tecnologia que colocação, aos programadores e arquitetos de soluções, devem considerar as recomendações abaixo. Para obter mais detalhes, consulte a secção seguinte.

Como um arquiteto de solução/programador, **deve considerar a utilização de filas de armazenamento** quando:

* A aplicação tem de armazenar mais de 80 GB de mensagens numa fila, em que as mensagens têm uma duração mais curta mais de 7 dias.
* A aplicação pretende controlar o progresso para processar uma mensagem dentro da fila. Isto é útil se o trabalho processar uma mensagem de falha. Uma função de trabalho subsequente, em seguida, pode utilizar essas informações para continuar a partir de onde parou o trabalho anterior.
* Precisa de registos do lado do servidor de todas as transações executadas contra as filas.

Como um arquiteto de solução/programador, **deve considerar a utilização de filas do Service Bus** quando:

* A solução deve ser capaz de receber mensagens sem ter de consultar a fila. Com o Service Bus, isto pode ser conseguido através da utilização da consulta de longa receber operação utilizando os protocolos baseada em TCP que suporta o Service Bus.
* A solução requer a fila para fornecer um garantida primeiro-na-primeiro-out (FIFO) ordenada de entrega.
* Pretende uma experiência simétrica no Azure e no Windows Server (nuvem privada). Para obter mais informações, consulte [barramento de serviço para o Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).
* A solução tem de ser capaz de suportar a deteção automática de duplicados.
* Pretender que a aplicação para processar mensagens como fluxos de longa execução paralelas (mensagens estão associadas a um fluxo utilizando o [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) propriedade na mensagem). Neste modelo, cada nó na aplicação de consumo compete para fluxos, por oposição a mensagens. Quando recebe uma transmissão em fluxo para um nó de consumo, o nó pode examinar o estado do fluxo do Estado da aplicação através de transações.
* A solução requer comportamento transacional e atomicity ao enviar ou receber várias mensagens numa fila.
* A time-to-live (TTL) uma característica da carga de trabalho específicas da aplicação pode exceder o período de 7 dias.
* A aplicação processa mensagens que podem exceder 64 KB, mas irá limitar abordagem é improvável 256 KB.
* Lidar com um requisito para fornecer um modelo de acesso baseado em funções para diferentes direitos/permissões e de filas para os remetentes e os recetores.
* O tamanho da fila não irá aumentar com mais de 80 GB.
* Pretende utilizar o protocolo de mensagens do baseada em normas de AMQP 1.0. Para mais informações sobre AMQP, consulte [descrição geral do Service Bus AMQP](service-bus-amqp-overview.md).
* Pode envision uma eventual migração de comunicação de ponto a ponto baseados em fila a um padrão do exchange de mensagem que lhe permite uma integração perfeita de recetores adicionais (subscritores), cada um dos que recebe cópias independentes de alguns ou todos os mensagens enviadas para a fila. A última opção refere-se a capacidade de publicar/subscrever nativamente fornecida pelo Service Bus.
* A solução de mensagens deve ser capaz de suportar a garantia de entrega "Em-maioria-uma vez" sem a necessidade de criar os componentes de infraestrutura adicionais.
* Gostaria de conseguir publicar e consumir lotes de mensagens em fila.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Comparar as filas de armazenamento e de filas do Service Bus
As tabelas nas secções seguintes fornecem um agrupamento lógico das funcionalidades de fila e permitem-lhe comparar, de forma rápida, as funcionalidades disponíveis em filas de armazenamento e de filas do Service Bus.

## <a name="foundational-capabilities"></a>Capacidades fundamentais sobre
Esta secção compara algumas das capacidades de colocação fundamentais fornecidas as filas de armazenamento e de filas do Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas de Service Bus |
| --- | --- | --- |
| Ordenação garantia |**Não** <br/><br>Para obter mais informações, consulte a nota primeiro na secção "Informações adicionais".</br> |**Sim - primeiro na primeira Out (FIFO)**<br/><br>(através da utilização de sessões de mensagens) |
| Garantia de entrega |**Em menos uma** |**Em menos uma**<br/><br/>**Em-maioria-uma vez** |
| Suporte de operação Atómica |**Não** |**Sim**<br/><br/> |
| Receber comportamento |**Bloquear o não**<br/><br/>(conclui imediatamente não se for encontrada nenhuma mensagem de novo) |**Bloquear com/sem tempo limite**<br/><br/>(oferece o período de tempo de consulta, ou o ["Técnica Comet"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Bloquear o não**<br/><br/>(através da utilização de .NET gerido API apenas) |
| API de estilo de push |**Não** |**Sim**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) e **OnMessage** sessões .NET API. |
| Receber modo |**Observar & concessão** |**Observar & bloqueio**<br/><br/>**Receber e eliminar** |
| Modo de acesso exclusivo |**Com base em concessão** |**Com base em bloqueio** |
| Duração de concessão/bloqueio |**30 segundos (predefinição)**<br/><br/>**7 dias (máximo)** (pode renovar ou uma mensagem através de concessão de versão a [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API.) |**60 segundos (predefinição)**<br/><br/>Pode renovar um bloqueio de mensagens utilizando o [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API. |
| / Bloqueio de concessão precisão |**Nível de mensagem**<br/><br/>(cada mensagem pode ter um valor de tempo limite diferentes, que, em seguida, pode atualizar conforme necessário durante o processamento da mensagem, utilizando o [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API) |**Nível de fila**<br/><br/>(cada fila tem uma precisão de bloqueio aplicada a todos os respetivos mensagens, mas podem renovar o bloqueio utilizando o [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API.) |
| Em lotes receber |**Sim**<br/><br/>(especifique explicitamente contagem de mensagens quando a obtenção de mensagens, até um máximo de 32 mensagens) |**Sim**<br/><br/>(implicitamente ativar uma propriedade de obtenção prévia ou explicitamente através da utilização de transações) |
| Em lote de envio |**Não** |**Sim**<br/><br/>(através da utilização de transações ou criação de batches do lado do cliente) |

### <a name="additional-information"></a>Informações adicionais
* Mensagens nas filas de armazenamento são normalmente primeiro no-first-escalamento, mas por vezes, podem estar fora de ordem; Por exemplo, quando duração do tempo limite de visibilidade de uma mensagem expira (por exemplo, como resultado de uma aplicação de cliente falhar durante o processamento). Quando o tempo limite de visibilidade expira, a mensagem fica visível numa fila de espera para outra função de trabalho de anular esta. Nessa altura, a mensagem recentemente visível pode ser colocada na fila (para ser removida novamente) depois de uma mensagem que foi originalmente colocados em fila depois.
* O padrão de FIFO garantido nas filas do Service Bus requer a utilização de sessões de mensagens. No caso de falha da aplicação ao processar uma mensagem recebida no **observar & Bloquear** modo, da próxima vez que um recetor de fila aceita uma sessão de mensagens, será iniciada com a mensagem de falha depois do time-to-live (TTL) período de expirar.
* As filas de armazenamento foram concebidas para suportar cenários colocação padrão, tais como desacoplamento componentes da aplicação para aumentar a escalabilidade e tolerância a falhas, carregar nivelamento e a criação de fluxos de trabalho do processo.
* Suporte de filas do Service Bus a *em menos uma* garantia de entrega. Além disso, o *em-maioria-uma vez* semântica pode ser suportado utilizando o estado da sessão para armazenar o estado da aplicação e utilizando transações atomically receber mensagens e atualizar o estado da sessão.
* As filas de armazenamento fornecem um modelo de programação uniform e consistente através de filas, tabelas e os BLOBs – para os programadores e equipas de operações.
* Filas do Service Bus fornecem suporte para transações locais no contexto de uma fila única.
* O **receber e eliminar** modo suportado pelo Service Bus fornece a capacidade para reduzir a contagem de operação de mensagens (e o custo associado) in exchange for garantia de entrega lowered.
* As filas de armazenamento fornecem concessões a capacidade de expandir as concessões de mensagens. Isto permite que os trabalhadores manter concessões curtos nas mensagens. Assim, se um trabalho falhar, a mensagem pode ser rapidamente processada novamente por outro worker. Além disso, uma função de trabalho pode alargar a concessão num mensagem se necessita de processar-mais do que o período de concessão atual.
* As filas de armazenamento oferecem um tempo limite de visibilidade que pode definir após a colocar ou dequeuing de uma mensagem. Além disso, pode atualizar uma mensagem com valores de concessão diferentes em tempo de execução e valores diferentes de atualização em mensagens na fila da mesma. Tempos limite de bloqueio do Service Bus são definidos nos metadados da fila; No entanto, podem renovar o bloqueio chamando a [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) método.
* O tempo limite máximo para uma bloquear a operação de receção de filas do Service Bus é de 24 dias. No entanto, os tempos limite baseado em REST tem um valor máximo de segundos 55.
* Do lado do cliente de criação de batches fornecida pelo Service Bus permite que um cliente de fila para várias mensagens do batch para uma operação de envio único. Criação de batches só está disponível para operações de envio assíncrono.
* Funcionalidades como o limite de 200 TB de filas de armazenamento (mais quando virtualizar contas) e de filas ilimitadas torná-lo numa plataforma ideal para fornecedores de SaaS.
* As filas de armazenamento fornecem um flexível e performant delegado mecanismo de controlo de acesso.

## <a name="advanced-capabilities"></a>Capacidades avançadas
Esta secção compara as capacidades avançadas disponibilizadas pelas filas de armazenamento e de filas do Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas de Service Bus |
| --- | --- | --- |
| Entrega agendada |**Sim** |**Sim** |
| Entregues inactivo automática |**Não** |**Sim** |
| Aumentar o valor time-to-live de fila |**Sim**<br/><br/>(através de atualização no local do tempo limite de visibilidade) |**Sim**<br/><br/>(fornecido através de uma função de API dedicada) |
| Poison suporte de mensagem |**Sim** |**Sim** |
| Atualização no local |**Sim** |**Sim** |
| Registo de transações do lado do servidor |**Sim** |**Não** |
| Métricas do Storage |**Sim**<br/><br/>**Minutos de métricas**: fornece métricas em tempo real para a API de disponibilidade, TPS, chamar contagens, contagens de erro e muito mais, todos em tempo real (agregados por minuto e comunicou dentro de alguns minutos a partir do que aconteceu apenas na produção. Para obter mais informações, consulte [sobre análise as métricas do Storage](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Sim**<br/><br/>(em massa consultas ao chamar [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Gestão de estado |**Não** |**Sim**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus.active), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus.disabled), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus.senddisabled), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus.receivedisabled) |
| Auto-reencaminhamento de mensagens |**Não** |**Sim** |
| Remover a função de fila |**Sim** |**Não** |
| Grupos de mensagem |**Não** |**Sim**<br/><br/>(através da utilização de sessões de mensagens) |
| Estado da aplicação por grupo de mensagem |**Não** |**Sim** |
| Deteção de duplicados |**Não** |**Sim**<br/><br/>(configurável no lado do remetente) |
| Navegação nos grupos de mensagem |**Não** |**Sim** |
| Obter as sessões de mensagens por ID |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Ambas as tecnologias de colocação permitem uma mensagem a ser agendada para entrega posterior.
* Fila auto-reencaminhamento permite milhares de filas para auto-reencaminhar as mensagens para uma fila única, a partir da qual a aplicação recetora consome a mensagem. Pode utilizar este mecanismo para alcançar a segurança, controlar o fluxo e isolar armazenamento entre cada fabricante de mensagem.
* As filas de armazenamento fornecem suporte para atualizar o conteúdo da mensagem. Pode utilizar esta funcionalidade para as informações de estado persistentes e atualizações incrementais de progresso para a mensagem para que possam ser processado a partir do último ponto de verificação conhecido, em vez de a partir do zero. Com as filas do Service Bus, pode ativar o cenário mesmo através da utilização de sessões de mensagens. Sessões permitem-lhe guardar e obter o estado de processamento de aplicação (através da utilização de [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Inutilizado lettering](service-bus-dead-letter-queues.md), que é apenas suportado por filas do Service Bus, pode ser útil para isolar as mensagens que não é possível processar com êxito pela aplicação recetora ou quando as mensagens não consegue contactar o seu destino devido a um expirada (time-to-live Propriedade TTL). O valor TTL especifica quanto uma mensagem permanece na fila. Com o Service Bus, a mensagem será movida para uma fila especial chamada $DeadLetterQueue quando o período TTL expire.
* Para localizar "nocivas" mensagens nas filas de armazenamento, quando dequeuing uma mensagem a aplicação examina o  **[DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx)**  propriedade da mensagem. Se **DequeueCount** é maior do que um determinado limiar, a aplicação move a mensagem para uma fila definido pela aplicação "entregues".
* As filas de armazenamento permitem-lhe obter um registo detalhado de todas as transações executadas contra a fila, como as métricas agregadas, bem como. Estas opções são úteis para depuração e compreender a forma como a aplicação utiliza as filas de armazenamento. Também são úteis para a aplicação otimização de desempenho e reduzir os custos de utilização de filas.
* O conceito de "sessões de mensagens" suportado pelo Service Bus permite que as mensagens que pertencem a um determinado grupo lógico estar associado um determinado recetor, que por sua vez, cria uma afinidade de sessão como entre as mensagens e os respetivos recetores. Pode ativar esta avançada funcionalidade do Service Bus, definindo o [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) propriedade de uma mensagem. Recetores, em seguida, podem escutar num ID de específica de sessão e receber mensagens que partilham o identificador de sessão especificado.
* A funcionalidade de deteção de duplicação suportada pelo filas do Service Bus automaticamente remove duplicadas mensagens enviadas para uma fila ou um tópico, com base no valor da [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) propriedade.

## <a name="capacity-and-quotas"></a>Capacidade e quotas
Esta secção compara as filas de armazenamento e de filas do Service Bus da perspetiva de [capacidade e quotas](service-bus-quotas.md) que podem ser aplicadas.

| Critérios de comparação | Filas de armazenamento | Filas de Service Bus |
| --- | --- | --- |
| Tamanho máximo da fila |**500 TB**<br/><br/>(limitado a um [única capacidade das contas de armazenamento](../storage/common/storage-introduction.md#queue-storage)) |**1 GB para 80 GB**<br/><br/>(definido após a criação de uma fila e [ativar a criação de partições](service-bus-partitioning.md) – consulte a secção "Informações adicionais") |
| Tamanho da mensagem máximo |**64 KB**<br/><br/>(48 KB quando utilizar **Base64** codificação)<br/><br/>Azure suporta mensagens grandes através da combinação de filas e blobs – ponto em que pode colocar em fila até 200GB para um único item. |**256 KB** ou **1 MB**<br/><br/>(incluindo o cabeçalho e corpo, tamanho do cabeçalho máximo: 64 KB).<br/><br/>Depende do [camada de serviço](service-bus-premium-messaging.md). |
| TTL da mensagem máximo |**7 dias** |**`TimeSpan.Max`** |
| Número máximo de filas |**Ilimitado** |**10,000**<br/><br/>(por espaço de nomes de serviço, pode ser aumentada) |
| Número máximo de clientes em simultâneo |**Ilimitado** |**Ilimitado**<br/><br/>(limite de ligações simultâneas 100 apenas se aplica a comunicação baseada no protocolo TCP) |

### <a name="additional-information"></a>Informações adicionais
* Barramento de serviço impõe limites de tamanho da fila. O tamanho máximo da fila é especificada durante a criação da fila e pode ter um valor entre 1 e 80 GB. Se o valor de tamanho da fila definido na criação da fila for atingido, serão rejeitadas mensagens de entrada adicionais e uma exceção será recebida pelo código da chamada. Para obter mais informações sobre as quotas no Service Bus, consulte [Quotas de barramento de serviço](service-bus-quotas.md).
* No [escalão Standard](service-bus-premium-messaging.md), pode criar filas do Service Bus em tamanhos de 1, 2, 3, 4 ou 5 GB (a predefinição é de 1 GB). No escalão Premium, pode criar filas até 80 GB de tamanho. No padrão camadas, com a criação de partições ativada (que é a predefinição), Service Bus cria 16 partições para cada GB que especificar. Como tal, se criar uma fila que é de 5 GB de tamanho, com 16 partições o tamanho máximo da fila fique (5 * 16) = 80 GB. Pode ver o tamanho máximo da fila particionada ou tópico observando a entrada [portal do Azure][Azure portal]. No escalão Premium, apenas 2 partições são criadas por fila.
* Com as filas de armazenamento, se o conteúdo da mensagem não é segura de XML, em seguida, tem de ser **Base64** codificado. Se lhe **Base64**-codificar a mensagem, o payload de utilizador pode ter até 48 KB, em vez de 64 KB.
* Com as filas do Service Bus, cada mensagem armazenada numa fila é composta por duas partes: um cabeçalho e um corpo. O tamanho total da mensagem não pode exceder o tamanho da mensagem máximo suportado pela camada de serviço.
* Quando os clientes comunicam com as filas do Service Bus através do protocolo TCP, o número máximo de ligações simultâneas para uma fila de barramento de serviço única está limitado a 100. Este número é partilhado entre os remetentes e os recetores. Se esta quota é atingida, os pedidos subsequentes para ligações adicionais serão rejeitados e uma exceção será recebida pelo código da chamada. Este limite não é imposto nos clientes, a ligação a filas baseado em REST API a utilizar.
* Se necessitar de mais de 10 000 filas num único espaço de nomes de barramento de serviço, pode contactar a equipa de suporte do Azure e pedir um aumento. Dimensionar para além dos 10 000 filas com o Service Bus, pode também criar espaços de nomes adicionais utilizando o [portal do Azure][Azure portal].

## <a name="management-and-operations"></a>Gestão e operações
Esta secção compara as funcionalidades de gestão fornecidas, as filas de armazenamento e de filas do Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas do Service Bus |
| --- | --- | --- |
| Protocolo de gestão |**REST através de HTTP/HTTPS** |**REST através de HTTPS** |
| Protocolo de tempo de execução |**REST através de HTTP/HTTPS** |**REST através de HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP com TLS)** |
| API .NET |**Sim**<br/><br/>(.NET armazenamento cliente API) |**Sim**<br/><br/>(Barramento de serviço de .NET API) |
| C++ nativas |**Sim** |**Sim** |
| API de Java |**Sim** |**Sim** |
| API DE PHP |**Sim** |**Sim** |
| API node.js |**Sim** |**Sim** |
| Suporte de metadados arbitrário |**Sim** |**Não** |
| Regras de nomenclatura de filas |**Até 63 carateres de comprimento**<br/><br/>(Letras no nome da fila tem de estar em minúsculas.) |**Máximo de 260 carateres de comprimento**<br/><br/>(Nomes e caminhos de fila são sensível.) |
| Obter a função de comprimento da fila |**Sim**<br/><br/>(Aproximado valor se mensagens expirarem para além do valor TTL sem ser eliminado.) |**Sim**<br/><br/>(Valor exato, no momento.) |
| Observar função |**Sim** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* As filas de armazenamento oferecem suporte para atributos arbitrários que podem ser aplicados a descrição da fila, sob a forma de pares nome/valor.
* Ambas as tecnologias de fila oferecem a possibilidade de observar uma mensagem sem ter de bloqueio, que podem ser úteis quando implementar uma ferramenta de explorer/browser de fila.
* O .NET de barramento de serviço mediadas mensagens APIs tire partido full-duplex ligações TCP para um melhor desempenho quando comparado com REST através de HTTP e suportam o protocolo de padrão de AMQP 1.0.
* Os nomes de filas de armazenamento pode ter entre 3 e 63 carateres, pode conter letras minúsculas, números e hífenes. Para obter mais informações, consulte [nomenclatura de filas e metadados](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Nomes de filas do Service Bus podem ser até 260 carateres e ter regras de nomenclatura menos restritivas. Nomes de filas do Service Bus podem conter letras, números, períodos, hífenes e carateres de sublinhado.

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Esta secção descreve as funcionalidades de autenticação e autorização suportadas pelo filas de armazenamento e de filas do Service Bus.

| Critérios de comparação | Filas de armazenamento | Filas de Service Bus |
| --- | --- | --- |
| Autenticação |**Chave simétrica** |**Chave simétrica** |
| Modelo de segurança |Acesso delegado através de SAS tokens. |SAS |
| Federação do fornecedor de identidade |**Não** |**Sim** |

### <a name="additional-information"></a>Informações adicionais
* Todos os pedidos para qualquer uma das tecnologias de colocação em fila tem de ser autenticado. As filas públicas com acesso anónimo não são suportadas. Utilizar [SAS](service-bus-sas.md), pode resolver este cenário através da publicação de uma SAS só de escrita, SAS só de leitura ou mesmo uma SAS de acesso completo.
* O esquema de autenticação fornecidos pelo armazenamento de filas envolve a utilização de uma chave simétrica, que é uma base em hash Message Authentication Code (HMAC), calculada com o algoritmo SHA-256 e codificada como uma **Base64** cadeia. Para obter mais informações sobre o respetivo protocolo, consulte [autenticação para os serviços de armazenamento do Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Filas do Service Bus suportam um modelo semelhante utilizando chaves simétricas. Para obter mais informações, consulte [autenticação de assinatura de acesso partilhado com o Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Conclusão
Por obter uma conhecimento mais aprofundado sobre as duas tecnologias, conseguirá tomar uma decisão informada mais que tecnologia de fila para utilizar e quando. A decisão sobre quando utilizar as filas de armazenamento ou filas do Service Bus claramente depende de um número de fatores. Estes fatores podem depender fortemente as necessidades da sua aplicação e respetiva arquitetura individuais. Se a aplicação já utiliza as principais funcionalidades do Microsoft Azure, poderá preferir escolher as filas de armazenamento, especialmente se necessitar de comunicação básica e processamento de mensagens entre serviços necessidade partilhadas ou filas podem ser superiores a 80 GB de tamanho.

Porque as filas do Service Bus fornecem diversas funcionalidades avançadas, tais como sessões, as transações, deteção, automática de duplicar capacidades de mensagens não entregues e durável publicação/subscrição, podem ser uma opção preferencial se está a criar uma versão híbrida aplicação ou se a sua aplicação, caso contrário requer estas funcionalidades.

## <a name="next-steps"></a>Passos seguintes
Os artigos seguintes fornecem mais informações sobre como utilizar as filas de armazenamento ou filas do Service Bus e documentação de orientação.

* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Melhores práticas para melhorias de desempenho utilizando o Service Bus mensagens mediadas](service-bus-performance-improvements.md)
* [Introdução às filas e tópicos de Service Bus do Azure (blogue)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Guia do programador ao Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Utilizar o serviço de colocação no Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com


---
title: Conceitos de grelha de eventos do Azure
description: Descreve os conceitos e grelha de eventos do Azure. Define alguns componentes chave da grelha de eventos.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: ccbd861c985e54a3808c0d4e8ea6169b6a61f134
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="concepts-in-azure-event-grid"></a>Conceitos na grelha de eventos do Azure

Os principais conceitos na grelha de eventos do Azure são:

## <a name="events"></a>Eventos

Um evento é a mais pequena quantidade de informações que descreve algo de completamente ocorrida no sistema.  Cada evento tem informações comuns, como: origem de evento, o tempo que demorou o evento local e o identificador exclusivo.  Cada evento também contém informações específicas que só são relevantes para o tipo específico do evento. Por exemplo, um evento sobre um novo ficheiro a ser criado no armazenamento do Azure contém detalhes sobre o ficheiro, tal como o `lastTimeModified` valor. Em alternativa, um evento sobre reiniciar uma máquina virtual contém o nome da máquina virtual e o motivo para reiniciar o computador. Cada evento está limitado a 64 KB de dados.

## <a name="event-sourcespublishers"></a>Origens/publicadores de eventos

Uma origem de evento é onde ocorre o evento. Por exemplo, o Storage do Azure é a origem do evento para o criar eventos de blob. Recursos de infraestrutura de VM do Azure é a origem de evento para eventos de máquina virtual. Origens de eventos são responsáveis por publicar eventos à grelha de eventos.

## <a name="topics"></a>Tópicos

Os publicadores categorizam eventos em tópicos. O tópico inclui um ponto final onde o publicador envia eventos. Para responder a determinados tipos de eventos, os subscritores decida quais tópicos para subscrever. Tópicos fornecem também um esquema de eventos, para que os subscritores podem detetar como consumir os eventos adequadamente.

Tópicos de sistema são tópicos incorporados fornecidos pelos serviços do Azure. Tópicos de personalizados são tópicos de terceiros e aplicações.

## <a name="event-subscriptions"></a>Subscrições de eventos

Uma subscrição instrui a grelha de evento que eventos de um tópico está interessado em receção um subscritor.  Uma subscrição também contém informações sobre como os eventos devem ser entregues para o subscritor.

## <a name="event-handlers"></a>Processadores de eventos

A partir de uma perspetiva de grelha de evento, um processador de eventos é o local onde o evento é enviado. O processador demora algumas medidas adicionais para processar o evento.  Grelha de evento suporta vários tipos de subscritor. Dependendo do tipo de subscritor, o evento grelha segue diferentes mecanismos para garantir a entrega do evento.  Para processadores de eventos de webhook HTTP, o evento é repetido até que o processador devolve um código de estado de `200 – OK`. Para a fila de armazenamento do Azure, os eventos são repetidos até que o serviço de fila é capaz de processar com êxito o push de mensagens na fila.

## <a name="filters"></a>Filtros

Ao subscrever um tópico, pode filtrar os eventos que são enviados para o ponto final. Pode filtrar por tipo de evento, ou um padrão de assunto. Para obter mais informações, consulte [esquema de subscrição de evento grelha](subscription-creation-schema.md).

## <a name="security"></a>Segurança

Grelha de evento fornece segurança de subscrição de tópicos e tópicos de publicação. Quando subscrever, tem de ter permissões do recurso ou tópico. Quando a publicação, tem de ter um SAS token ou a autenticação de chave para o tópico. Para obter mais informações, consulte [grelha de eventos de segurança e autenticação](security-authentication.md).

## <a name="failed-delivery"></a>Entrega falhou

Quando a grelha de eventos não é possível confirmar que foi recebido um evento por ponto final do subscritor, redelivers o evento. Para obter mais informações, consulte [entrega de mensagens de evento grelha e volte a tentar](delivery-and-retry.md).

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à grelha de eventos, consulte [sobre eventos grelha](overview.md).
* Para rapidamente começar a utilizar a grelha de eventos, consulte o artigo [criar e rota eventos personalizados com o Azure eventos grelha](custom-event-quickstart.md).

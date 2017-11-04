---
title: Azure Notification Hubs
description: "Saiba como adicionar capacidades de notificação push com Notification Hubs do Azure."
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
ms.openlocfilehash: a1be0b13cd1feb582a23965df142e44d90ac6851
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-notification-hubs"></a>Azure Notification Hubs
## <a name="overview"></a>Descrição geral
Os Hubs de notificação do Azure fornecem um motor de push de fácil utilização, várias plataformas, escalamento horizontal. Com uma chamada de API única plataforma, pode facilmente enviar notificações push segmentada e personalizada para qualquer plataforma móvel de qualquer back-end em nuvem ou no local.

Os Notification Hubs funciona muito bem para cenários de empresa e de consumidor. Seguem-se alguns exemplos aos clientes utilizam Notification Hubs para:

* Envie notificações de notícias de última para milhões de pessoas com baixa latência.
* Envie cupões com base na localização a segmentos de utilizadores interessado.
* Envie notificações relacionadas com o evento a utilizadores ou grupos para aplicações de suporte de dados/desporto/Finanças/jogos.
* Emitir promocional conteúdo para aplicações para interagir com e mercado aos clientes.
* Notificar os utilizadores de eventos de empresa como novas mensagens e itens de trabalho.
* Envie códigos para autenticação multifator.

## <a name="what-are-push-notifications"></a>O que são as Notificações Push?
Notificações push é um formulário de comunicação da aplicação-a-utilizador onde os utilizadores de aplicações móveis são notificados de determinadas informações pretendidas, normalmente, na caixa de pop-up ou caixa de diálogo. Os utilizadores, geralmente, podem optar por visualizar ou ignorar a mensagem e escolher a primeira ação irá abrir a aplicação móvel que tinha comunicados a notificação.

Notificações push é vital para aplicações de consumidor aumentar o engagement de aplicação e a utilização de e para aplicações da empresa na comunicação das informações de negócio atualizadas. É a comunicação de aplicação-a-utilizador melhor porque está disponível e de energia-eficiente para dispositivos móveis, flexíveis para remetentes notificações, enquanto as correspondentes aplicações não estão ativas.

Para mais informações sobre as notificações push para algumas plataformas populares:
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Como Funcionam as Notificações Push
Notificações push são entregues através de infraestruturas específicas da plataforma denominadas *sistemas de notificação de plataforma* (PNSes). Oferecem funcionalidades de push barebone a mensagem de entrega para um dispositivo com um fornecido processam e tem uma interface comum. Para enviar uma notificação a todos os clientes através de iOS, Android e Windows versões de uma aplicação, o programador tem de trabalhar com APNS (serviço Apple Push Notification), FCM (Firebase Cloud Messaging) e o WNS (serviço de notificação do Windows), durante a criação de batches de envia.

Um nível elevado, eis como funciona o push:

1. A aplicação cliente decide que pretende receber pushes, por conseguinte, contacta o PNS para obter o respetivo Identificador exclusivo e temporário push correspondente. O tipo de identificador depende do sistema (por exemplo, WNS tem URIs enquanto APNS tem tokens).
2. A aplicação cliente armazena este identificador no back-end da aplicação ou do fornecedor.
3. Para enviar uma notificação push, o back-end da aplicação contacta o PNS utilizando o identificador como destino uma aplicação de cliente específico.
4. O PNS reencaminha a notificação para o dispositivo especificado pelo identificador.

![][0]

## <a name="the-challenges-of-push-notifications"></a>Os Desafios das Notificações Push
Enquanto PNSes são poderosas, estes deixam muito trabalho ao programador da aplicação para implementar cenários de notificação push, mesmo comuns, como difundir ou enviar notificações push para utilizadores segmentados.

Push é uma das funcionalidades mais pedidas nos serviços de nuvem móveis, porque o respetivo trabalho requer infraestruturas complexas não relacionadas com a lógica de negócio principal da aplicação. Alguns dos desafios infraestrutura são:

* **Dependência de plataforma**: 

  * O back-end tem de ter complexa e difícil manter depende da plataforma a lógica para enviar notificações para dispositivos em diversas plataformas, como PNSes não são unificadas.
* **Escala**:

  * Pelas diretrizes PNS, os tokens de dispositivo têm de ser atualizados após cada iniciação da aplicação. Isto significa que o back-end é lidar com uma grande quantidade de tráfego e da base de dados acesso apenas para manter os tokens atualizados. Quando o número de dispositivos crescimentos centenas e milhares de milhões, o custo de criar e manter esta infraestrutura é grande.
  * A maioria das PNSes não suportam a difusão para vários dispositivos. Isto significa que a difusão simple para resultados de dispositivos de um milhões num milhões de chamadas para o PNSes. Dimensionamento esta quantidade de tráfego com latência mínima é Trivial.
* **Encaminhamento**:
  
  * Embora PNSes proporcionam uma forma para enviar mensagens para dispositivos, a maioria das notificações de aplicações são direcionadas para os utilizadores ou grupos de interesses. Isto significa que o back-end tem de manter um registo a associar dispositivos, grupos de interesses, os utilizadores, propriedades, etc. Esta sobrecarga soma-se a hora para os custos de comercialização e manutenção de uma aplicação.

## <a name="why-use-notification-hubs"></a>Porquê Utilizar Notification Hubs?
Notification Hubs elimina todas as complexidades associadas ativar push seu próprio. A infraestrutura de notificação push de várias plataformas, escalamento horizontal reduz códigos de push e simplifica o back-end. Com os Notification Hubs, os dispositivos são simplesmente responsáveis por registar os respetivos identificadores PNS com um concentrador, enquanto o back-end envia mensagens para utilizadores ou grupos de interesses, conforme mostrado na figura seguinte:

![][1]

Os Notification hubs é o motor de push pronta a utilizar com as seguintes vantagens:

* **Entre plataformas**

  * Suporte para todas as plataformas de push principais, incluindo iOS, Android, Windows e Kindle e Baidu.
  * Uma interface comum para enviar para todas as plataformas em formatos específicos da plataforma ou independentes da plataforma com nenhum trabalho específico da plataforma.
  * Dispositivo processe a gestão num único local.
* **Cruzada back-ends**
  
  * Nuvem ou no local
  * .NET, Node.js, Java, etc.
* **Conjunto completo de padrões de entrega**:

  * *Difusão para uma ou várias plataformas*: instantaneamente pode difusão para milhões de dispositivos em plataformas com uma única chamada de API.
  * *Push para o dispositivo*: pode visar notificações para dispositivos individuais.
  * *Enviar para o utilizador*: funcionalidades etiquetas e modelos de ajudam a alcançar a todos os dispositivos de várias plataformas de um utilizador.
  * *Enviar por push para o segmento com etiquetas dinâmicas*: funcionalidade etiquetas ajuda-o a dispositivos de segmento e push aos mesmos, consoante as suas necessidades, se estiver a enviar para um segmento ou uma expressão de segmentos (por exemplo, de Active Directory e vida em Seattle não novo utilizador). Em vez de ser restringida ao pub sub, pode atualizar as etiquetas de dispositivo em qualquer lugar e em qualquer altura.
  * *Localizado push*: a funcionalidade de modelos ajuda a alcançar a localização sem afetar o código de back-end.
  * *Push silenciosa*: pode permite o padrão de push para extração mediante o envio de notificações automáticas em dispositivos e a acionar a sua conclusão determinadas obtém pelos mesmos ou ações.
  * *Agendadas push*: pode agendar a enviar notificações em qualquer altura.
  * *Direcionar push*: pode ignorar registar dispositivos com o nosso serviço e diretamente do batch push para uma lista de identificadores do dispositivo.
  * *Personalizar push*: notificações com personalizadas pares chave-valor de push de variáveis de push de dispositivo personalizada ajuda a enviar específicos do dispositivo.
* **Telemetria completa**
  
  * Telemetria de push, dispositivo, erro e operação geral está disponível no portal do Azure e através de programação.
  * Por mensagem de telemetria controla cada push da chamada de pedido inicial ao nosso serviço com êxito os pushes saída de criação de batches.
  * Comentários de sistema de notificação de plataforma comunica todos os comentários de sistemas de notificação de plataforma fiável para ajudar a depurar.
* **Escalabilidade** 
  
  * Envie mensagens rápidas para milhões de dispositivos sem fragmentação rearquitetar ou dispositivo.
* **Segurança**

  * Segredo de acesso partilhado (SAS) ou autenticação federada.

## <a name="integration-with-app-service-mobile-apps"></a>Integração com as Aplicações Móveis do Serviço de Aplicações
Para facilitar uma experiência totalmente integrada e unificadora em todos os serviços do Azure, as [Aplicações Móveis do Serviço de Aplicações] têm suporte incorporado para notificações push com os Hubs de Notificação. As [Aplicações Móveis do Serviço de Aplicações] oferecem uma plataforma de desenvolvimento de aplicações móveis altamente dimensionável e globalmente disponível para Programadores Empresariais e Integradores de Sistemas, que fornece um conjunto completo de capacidades para programadores móveis.

Os programadores de Mobile Apps podem utilizar Notification Hubs com o fluxo de trabalho seguinte:

1. Obter o identificador PNS do dispositivo
2. Registar o dispositivo com Notification Hubs através da conveniente API de registo do SDK de cliente de aplicações móveis
   * Tenha em atenção que, por motivos de segurança, as Mobile Apps eliminam todas as etiquetas ao registarem-se. Trabalhar com Notification Hubs diretamente no seu back-end para associar etiquetas a dispositivos.
3. Enviar notificações a partir do seu back-end de aplicação com Notification Hubs

Aqui estão algumas das conveniências que esta integração proporciona a programadores:

* **SDKs de cliente de aplicações móveis**: estes SDKs Multiplataforma fornecem APIs simples para o registo e comunicar com o notification hub ligado com a aplicação móvel automaticamente. Os programadores não precisam de analisar a fundo as credenciais dos Notification Hubs e trabalham com um serviço adicional.

  * *Enviar para o utilizador*: os SDKs etiquetam automaticamente o dispositivo especificado com o ID utilizador autenticado de Mobile Apps para ativar o push para o cenário de utilizador.
  * *Push para o dispositivo*: os SDKs utilizam automaticamente o ID de instalação de aplicações móveis como GUID para registar com os Notification Hubs, poupando aos programadores o trabalho de manterem GUIDs de serviço.
* **Modelo de instalação**: as Mobile Apps funcionam com o modelo push mais recente dos Notification Hubs para representar todas as propriedades push associadas um dispositivo numa instalação JSON que está alinhada com notificações push e é fácil de utilizar.
* **Flexibilidade**: os programadores podem sempre optar por trabalhar diretamente com Notification Hubs, mesmo com a integração no local.
* **Experiência integrada no [portal do Azure]**: o Push como uma capacidade está representada visualmente em Mobile Apps e os programadores podem trabalhar facilmente com o notification hub associado através das Mobile Apps.

## <a name="next-steps"></a>Passos Seguintes
Pode encontrar mais informações sobre os Notification Hubs nos seguintes tópicos:

* **[Como os clientes estão a utilizar os Hubs de Notificação]**
* **[Tutoriais e guias dos Hubs de Notificação]**
* **Tutoriais de introdução aos Hubs de notificação**: [iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [xamarin. IOS], [xamarin. Android]

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[Como os clientes estão a utilizar os Hubs de Notificação]: http://azure.microsoft.com/services/notification-hubs
[Tutoriais e guias dos Hubs de Notificação]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[xamarin. IOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[xamarin. Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[Aplicações Móveis do Serviço de Aplicações]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[portal do Azure]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

<properties
    pageTitle="Notification Hubs do Azure"
    description="Saiba como utilizar as notificações push no Azure. Exemplos de código escrito em C# utilizando a API .NET."
    authors="wesmc7777"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="wesmc"/>


#Notification Hubs do Azure

##Descrição geral

Os Notification Hubs do Azure fornecem uma infraestrutura push ampliada multiplataformas de fácil utilização, que lhe permite enviar notificações push móveis a partir de qualquer back-end (na nuvem ou no local) para qualquer plataforma móvel.

Com os Hubs de Notificação, pode enviar facilmente notificações push personalizadas para várias plataformas, abstraindo os detalhes dos sistemas de notificação das várias plataformas (PNS, Platform Notification Systems). Com uma única chamada API, pode visar utilizadores individuais ou segmentos de audiência inteiros contendo milhões de utilizadores, em todos os respetivos dispositivos.

Pode utilizar os Notification Hubs para cenários de empresa e de consumidor. Por exemplo:

- Envie notificações de notícias de última hora a milhões de pessoas com baixa latência (as Notification Hubs alimentam as aplicações Bing pré-instaladas em todos os dispositivos Windows e Windows Phone).
- Envie cupões com base na localização a segmentos de utilizadores.
- Envie notificações de eventos a utilizadores ou grupos para aplicações de desportos/finanças/jogos.
- Notifique utilizadores de eventos de empresa como novas mensagens/e-mails e vendas potenciais.
- Envie palavras-passe de utilização única necessária para o Multi-Factor Authentication.



##O que são as Notificações Push?

Os smartphones e os tablets podem “notificar” os utilizadores quando tiverem ocorrido eventos. Estas notificações podem tomar várias formas.

Em aplicações da Loja Windows e Windows Phone, a notificação pode ter a forma de um _alerta_: é apresentada uma janela sem modo, com um som, para assinalar uma nova notificação. São suportados outros tipos de notificações, incluindo notificações de _mosaico_, _sem formato definido_ e de _destaque_. Para obter mais informações sobre os tipos de notificações suportados pelos dispositivos Windows, veja [Tiles, Badges, and Notifications (Mosaicos, Destaques e Notificações)](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

Em dispositivos Apple iOS, o push notifica, do mesmo modo, o utilizador com uma caixa de diálogo que solicita que veja ou feche a notificação. Ao clicar em **Ver**, abre-se a aplicação que está a receber a mensagem. Para obter mais informações sobre as Notificações de iOS, veja [iOS Notifications (Notificações iOS)](http://go.microsoft.com/fwlink/?LinkId=615245).

As notificações push ajudam os dispositivos móveis a apresentar informações novas mantendo a eficiência energética. As notificações podem ser enviadas pelos sistemas de back-end para dispositivos móveis, mesmo quando as correspondentes aplicações não estão ativas num dispositivo. As notificações push são um componente vital para as aplicações de consumidor, onde são utilizadas para aumentar a fidelização e a utilização da aplicação. As notificações também são úteis para empresas, em que informações atualizadas aumentam a capacidade de resposta dos funcionários a eventos empresariais.

Alguns exemplos específicos de cenários de envolvimento móvel:

1.  Atualizar um mosaico no Windows 8 ou Windows Phone com informações financeiras atuais.
2.  Alertar um utilizador com um alerta que um item de trabalho lhe foi atribuído, numa aplicação empresarial baseada em fluxo de trabalho.
3.  Apresentar um destaque com o número de potenciais vendas atuais numa aplicação CRM (por exemplo, a Microsoft Dynamics CRM).

##Como Funcionam as Notificações Push

As notificações push são entregues através de infraestruturas específicas da plataforma denominadas _Sistemas de Notificação de Plataforma_ (PNS). Um PNS oferece funções básicas (ou seja, sem suporte de difusão ou personalização) e não tem uma interface comum. Por exemplo, para enviar uma notificação para uma aplicação da Loja Windows, o programador tem de contactar o WNS (Windows Notification Service). Para enviar uma notificação para um dispositivo iOS, o mesmo programador tem de contactar o APNS (serviço Apple Push Notification) e enviar a mensagem uma segunda vez. Os Notification Hubs do Azure ajudam, fornecendo uma interface comum, juntamente com outras funcionalidades, para suportar notificações push em cada plataforma.

No entanto, a um nível elevado, todos os sistemas de notificação de plataforma seguem o mesmo padrão:

1.  A aplicação cliente contacta o PNS para obter o seu _identificador_. O tipo de identificador depende do sistema. Para WNS, é um URI ou “canal de notificação”. Para APNS, é um token.
2.  A aplicação cliente armazena este identificador no _back-end_ da aplicação para utilização posterior. Para WNS, o back-end é normalmente um serviço na nuvem. Para Apple, o sistema chama-se _fornecedor_.
3.  Para enviar uma notificação push, o back-end da aplicação contacta o PNS utilizando o identificador para visar uma instância específica da aplicação cliente de destino.
4.  O PNS reencaminha a notificação para o dispositivo especificado pelo identificador.

![][0]

##Os Desafios das Notificações Push

Estes sistemas são muito poderosos, mas deixam, mesmo assim, muito trabalho ao programador da aplicação para implementar até mesmo cenários de notificação push comuns, como difundir ou enviar notificações push para utilizadores segmentados.

As notificações push são uma das funcionalidades mais pedidas aos serviços em nuvem para aplicações móveis. O motivo para isto é que a infraestrutura necessária para que funcionem é relativamente complexa e basicamente não relacionada com a lógica principal de negócio da aplicação. Alguns dos desafios na construção de uma infraestrutura de push a pedido são:

- **Dependência de plataforma.** Para enviar notificações para dispositivos em diferentes plataformas, é preciso codificar múltiplas plataformas no back-end. Não são só os detalhes de baixo nível que diferem, a própria apresentação da notificação (mosaico, alerta ou destaque) também depende da plataforma. Estas diferenças podem levar a um código de back-end complexo e difícil manter.

- **Dimensionamento.** O dimensionamento desta infraestrutura tem dois aspetos:
    + De acordo com as diretrizes PNS, os tokens de dispositivo têm de ser atualizados sempre que a aplicação é iniciada. Esta situação origina uma grande quantidade de tráfego (e consequente acesso às da base de dados) só para manter atualizados os tokens de dispositivo. Quando aumenta o número de dispositivos (possivelmente para milhões), o custo de criar e manter esta infraestrutura deixa de ser negligenciável.

    + A maioria dos PNSs não suporta a difusão para vários dispositivos. Consequentemente, a difusão para milhões de dispositivos resulta em milhões de chamadas para os PNSs. A capacidade de dimensionar estes pedidos é importante, porque, normalmente, os programadores de aplicações preferem manter a latência total reduzida. Por exemplo, o último dispositivo a receber a mensagem não deverá receber a notificação 30 minutos depois do envio, uma vez que, em muitos casos, isso iria contra o objetivo das notificações push.
- **Encaminhamento.** Os PNSs proporcionam um modo de enviar uma mensagem para um dispositivo. No entanto, na maioria das aplicações as notificações destinam-se a utilizadores e/ou grupos de interesses (por exemplo, todos os funcionários atribuídos a uma determinada conta de cliente). Sendo assim, para encaminhar as notificações para os dispositivos corretos, o back-end da aplicação tem de manter um registo que associa os grupos de interesses a tokens de dispositivo. Esta sobrecarga soma-se ao tempo total de comercialização e aos custos de manutenção de uma aplicação.

##Porquê Utilizar Notification Hubs?

Os Notification Hubs acabam com a complexidade: não terá de gerir os desafios das notificações push. Pode, em vez disso, utilizar um Notification Hub. Os Notification Hubs utilizam uma infraestrutura completa de notificações push multiplataforma ampliada e reduzem significativamente o código específico de push que é executado no back-end da aplicação. Os Notification Hubs implementam todas as funcionalidades de uma infraestrutura push. Os dispositivos são apenas responsáveis pelo registo de identificadores PNS e o back-end é responsável pelo envio de mensagens independentes da plataforma a utilizadores ou grupos de interesses, como se mostra na figura seguinte:

![][1]


Os Notification Hubs fornecem uma infraestrutura de notificação push pronta a utilizar com as seguintes vantagens:

- **Várias plataformas.**
    +  Suporte para todas as principais plataformas móveis. Os Notification Hubs podem enviar notificações push para aplicações da Loja Windows, iOS, Android e Windows Phone.

    +  Os Notification Hubs constituem uma interface comum para enviar notificações para todas as plataformas suportadas. Não são necessários protocolos específicos da plataforma. O back-end da aplicação pode enviar notificações em formatos específicos da plataforma ou independentes da plataforma. A aplicação comunica apenas com os Notification Hubs.

    +  Gestão de identificadores do dispositivo. Os Notification Hubs mantêm o registo dos identificadores e a resposta dos PNSs.

- **Funciona com qualquer back-end**: na nuvem ou no local, .NET, PHP, Java, Nó, etc.

- **Dimensionamento.** Dimensionamento dos Notification Hubs para milhões de dispositivos sem a necessidade de nova arquitetura ou de partições horizontais.


- **Conjunto completo de padrões de entrega**:

    - *Difusão*: permite a difusão quase simultânea para milhões de dispositivos com uma única chamada de API.

    - *Unicast/Multicast*: emite via push para etiquetas que representam utilizadores individuais, incluindo todos os respetivos dispositivos; ou um grupo mais amplo; por exemplo, fatores de forma separados (tablet vs. telefone).

    - *Segmentação*: emite via push para segmentação complexa definida pelas expressões de etiqueta (por exemplo, dispositivos em Nova Iorque que seguem os Yankees).

    Cada dispositivo, ao enviar o respetivo identificador a um Notification Hub, pode especificar uma ou mais _etiquetas_. Para obter mais informações, consulte [etiquetas]. As etiquetas não têm de ser previamente aprovisionadas nem eliminadas. As etiquetas proporcionam uma forma simples de enviar notificações para utilizadores ou grupos de interesses. Uma vez que as etiquetas podem conter qualquer identificador específico de uma aplicação (por exemplo, IDs de utilizadores ou de grupos), a sua utilização retira ao back-end da aplicação o fardo de ter de armazenar e gerir os identificadores dos dispositivos.

- **Personalização**: cada dispositivo pode ter um ou mais modelos, para alcançar a localização por dispositivo e personalizar sem afetar o código do back-end.

- **Segurança**: Segredo de Acesso Partilhado (SAS) ou autenticação federada.

- **Telemetria completa**: disponível no portal e através de programação.


##Integração com as Aplicações Móveis do Serviço de Aplicações

Para facilitar uma experiência totalmente integrada e unificadora em todos os serviços do Azure, as [Aplicações Móveis do Serviço de Aplicações] têm suporte incorporado para notificações push com os Hubs de Notificação. As [Aplicações Móveis do Serviço de Aplicações] oferecem uma plataforma de desenvolvimento de aplicações móveis altamente dimensionável e globalmente disponível para Programadores Empresariais e Integradores de Sistemas, que fornece um conjunto completo de capacidades para programadores móveis.

Os programadores de Mobile Apps podem utilizar Notification Hubs com o fluxo de trabalho seguinte:

1. Obter o identificador PNS do dispositivo
2. Registar o dispositivo e [modelos] com os Notification Hubs através da conveniente API de registo do SDK de Cliente de Mobile Apps
    + Tenha em atenção que, por motivos de segurança, as Mobile Apps eliminam todas as etiquetas ao registarem-se. Trabalhar com Notification Hubs diretamente no seu back-end para associar etiquetas a dispositivos.
3. Enviar notificações a partir do seu back-end de aplicação com Notification Hubs

Aqui estão algumas das conveniências que esta integração proporciona a programadores:

- **SDKs Cliente das Mobile Apps** Estes SDKs multiplataforma fornecem APIs simples para registar e falar com o Notification Hub ligado automaticamente à aplicação móvel. Os programadores não precisam de analisar a fundo as credenciais dos Notification Hubs e trabalham com um serviço adicional.
    + Os SDKs etiquetam automaticamente o dispositivo especificado com ID de utilizador autenticado de Mobile Apps para ativar o push para o cenário do utilizador.
    + Os SDKs utilizam automaticamente o ID de Instalação das Mobile Apps como GUID para se registarem nos Notification Hubs, poupando aos programadores o trabalho de manterem GUIDs para vários serviços.
    
- **Modelo de instalação** As Mobile Apps funcionam com o modelo push mais recente dos Notification Hubs para representar todas as propriedades push associadas a um dispositivo numa instalação JSON que se alinha com os Serviço de Notificações Push e é fácil de utilizar.

- **Flexibilidade.** Os programadores podem sempre optar por trabalhar diretamente com Notification Hubs, mesmo com a integração instalada.

- **Experiência integrada no [Portal do Azure].** O Push como uma capacidade está representado visualmente em Mobile Apps e os programadores podem trabalhar facilmente com o Notification Hub associado através das Mobile Apps.



##Passos Seguintes

Pode encontrar mais informações sobre os Notification Hubs nos seguintes tópicos:

+ **[Como os clientes estão a utilizar os Notification Hubs]**

+ **[Tutoriais e guias dos Notification Hubs]**

+ **Tutoriais de Introdução aos Notification Hubs** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

As referências relevantes à API .NET gerida para notificações push estão localizadas aqui:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Como os clientes estão a utilizar os Notification Hubs]: http://azure.microsoft.com/services/notification-hubs
  [Tutoriais e guias dos Notification Hubs]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Aplicações Móveis do Serviço de Aplicações]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [modelos]: notification-hubs-templates-cross-platform-push-messages.md
  [Portal do Azure]: https://portal.azure.com
  [etiquetas]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)



<!--HONumber=sep16_HO1-->



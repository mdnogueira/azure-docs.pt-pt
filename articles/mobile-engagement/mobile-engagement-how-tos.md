---
title: Interface de utilizador do Azure Mobile Engagement - alcance como
description: "Descrição geral de Interface de utilizador do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 33a0a9d0c399cb7f0a791c4c16dde2e2d62364ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Como começar a utilizar e gerir pushes para aceder aos seus utilizadores finais
Assim que o SDK está totalmente integrado na aplicação, pode começar a utilizar a secção de alcance da IU para enviar notificações Push para os utilizadores da sua aplicação.  

## <a name="do-your-first-push-notification-campaign"></a>Efetue a sua primeira campanha de notificações Push
* Certifique-se de que o alcance está integrado na aplicação com o SDK. 
* Selecione a aplicação

![First1][1]

* Vá para a secção "Alcance" e clique em "novo anúncio"

![First2][2]

* Criar uma nova campanha e nome
  
![First3][3]

* Selecione a forma como a notificação deve ser entregue, como na aplicação apenas

![First4][4]

* Criar a mensagem que pretender emitir

![First5][5]

* Pode escrever um título na notificação (opcional).
* Escreva o conteúdo da mensagem push.
* Pode carregar uma imagem. Lembre-se de que o tamanho do ficheiro não pode exceder 32,768 bytes.
* Também tem a capacidade de selecionar mais opções, mas para o objetivo deste tutorial, Vemos que mais tarde.
* Selecione o tipo de conteúdo como notificação apenas

![First6][6]

* Criar a sua campanha push e irá aparecer na lista de campanha.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>Testar a sua campanha de notificações Push
![test1][8]

* Registe o seu dispositivo.
* Clique na caixa de verificação do dispositivo que pretende push.
* Clique no botão "Teste" para enviar o push para o dispositivo.

![TEST2][9]

* Ativar a campanha

![test3][10]

* Agora que criou a sua campanha apenas terá de ativar para a notificação ser enviado para os seus utilizadores.

## <a name="send-personalized-pushes"></a>Envie Pushes personalizados
* Este exemplo cria um push onde introduzido um código personalizado rebate na notificação push.

![Personalize1][11]

Funciona ao substituir um marcador através de uma tag de informações de aplicação, por isso, a personalização, terá de certificar-se de que o utilizador tem o app-info adequada definido pela primeira vez. Neste exemplo, os utilizadores-alvo terá uma etiqueta de informações de aplicação com o nome rebate_code definido.
Como ver acima o conteúdo de notificação push inclui o marcador ${rebate_code}, que irá indicar que está a ser substituído pelo conteúdo real da etiqueta de informações de aplicação.

> [!WARNING]
> Se a etiqueta de informações da aplicação não está definida para o utilizador, o utilizador não irá receber push.

* resultado

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Ainda pode personalizar o texto da notificação
![Personalize3][13]

* Incluindo o título da notificação,
* E o conteúdo da mensagem.
* Escolha o tipo de anúncio (vista Web ou de vista de texto)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>O corpo de um anúncio também pode ser personalizado com:
* O URL de ação, deve que pretende personalizar a página de destino
* O título
* O corpo da mensagem.

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Diferenciar a notificação Push (entrada ou fora da aplicação)
* Escolha o tipo de notificação serão push, selecione a aplicação, aceda à secção "Alcançar", selecione ou crie uma campanha push e aceda à secção "Notificações".
* Clique no "modo de entrega" quiser.
* Clique na caixa de verificação "Restringir as atividades" quando pretender que a notificação ocorre em determinadas atividades (ecrãs).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Modo de entrega "Apenas fora da aplicação"
![Differentiate2][16]

"Apenas fora da aplicação" modo de entrega fornece notificação push, quando a aplicação está fechada. Esta é a notificação push padrão.
Ao selecionar "apenas fora da aplicação", que tem já forneceu os certificados da plataforma que a aplicação está a criar em (APNS ou GCM).

### <a name="see-also"></a>Consultar também
* [Certificados de serviço Apple Push Notification –](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging – certificado](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"na aplicação apenas" modo de entrega
![Differentiate3][17]

Modo de entrega "Na aplicação apenas" fornece notificação push, quando a aplicação está em execução.
Para obter esta notificação, não terá de percorrer o sistema do APNS e GCM.
Pode utilizar o sistema de entrega de na aplicação para alcançar os utilizadores finais.
Totalmente pode personalizar a notificação e decidir na qual a atividade (ecrã) a notificação será apresentada.

### <a name="anytime-delivery-mode"></a>Modo de entrega "Em qualquer altura"
Pode escolher um modo de entrega "Em qualquer altura", garante a alcançar o utilizador final, se a aplicação está em execução ou não.
Ao selecionar "Em qualquer altura", que tem já forneceu os certificados da plataforma que a aplicação está a criar após (APNS ou GCM). 

## <a name="schedule-a-push-campaign"></a>Agendar uma campanha de emissão
### <a name="plan-to-start-a-campaign"></a>Plano para iniciar uma campanha
![Shedule1][18]

É o dia 21 de Março e ter um anúncio para tornar e planed para o 22 de Março de à meia-noite. Não tem de permanecer à frente da interface de fazer um push! Pode Planeie antecipadamente o minuto exato serão enviadas notificações.

* Verificação de anular "None" caixa de verificação e selecione uma hora de início 
* Selecione a data e hora em que pretende iniciar a campanha push.

### <a name="plan-to-end-a-campaign"></a>Plano para terminar uma campanha
![Shedule2][19]

Pretende que a campanha para parar no 25th de Março em 3.00 pm mas sabe que não será existe fazê-lo.
Não tem de permanecer à frente da interface de push! Pode Planeie antecipadamente o minuto exato que irá parar a sua campanha.

* Clique em "None" caixa de verificação ou selecione uma hora de fim
* Selecione a data e hora em que pretende concluir a campanha push.

### <a name="end-a-campaign-manually"></a>Terminar uma campanha manualmente
![Shedule3][20]

Por predefinição, o "None" caixas de verificação estão selecionadas.
Isto significa que a campanha iniciará assim que a ativa na secção de alcance e terminará quando deixa-lo na secção de alcance.

> [!NOTE]
> As campanhas criadas sem uma data de fim armazenam push localmente no dispositivo e mostram-lo da próxima vez que abrir a aplicação, mesmo que a campanha manualmente seja terminada.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Melhorar a uma notificação Push com uma vista de texto
### <a name="what-is-a-text-view"></a>O que é uma vista de texto?
![TextView1][21]

Uma vista de texto é um pop-up com conteúdo de texto. Este pop-up aparece depois do utilizador final tiver clicado na notificação push.
Uma vista de texto permite-lhe apresentar mais conteúdos para o utilizador final. Isto também se a oportunidade para apresentar uma chamada para a ação, tal como jumping para uma página da sua aplicação, redirecionar para um arquivo, abrir uma página web, enviar uma mensagem de correio eletrónico a partir de uma pesquisa geolocalizados, etc...

### <a name="example-text-view"></a>Exemplo: Vista de texto
* Criar a sua campanha de notificações Push na secção "Alcance" e atribua um nome a sua campanha

![TextView2][22]

* Escreva a mensagem que será apresentado na notificação.
* Selecione o tipo de conteúdo do anúncio de "text"

![TextView3][23]

> [!NOTE]
> Quando enviar uma vista de texto, este sempre inclui uma notificação primeiro. 

* Definir o texto (depois de ter selecionado o conteúdo do anúncio de texto, será apresentada a secção secundárias, permitindo-lhe definir o texto que pretende apresentar.)

![TextView4][24]

* Escreva o título que será apresentada na parte superior da mensagem.
* Escreva o conteúdo da vista de texto principal.
* Escreva o conteúdo que será apresentado no botão de ação (um botão de ação permite que a aplicação efetuar uma ação específica, tais como abrir uma página da aplicação, redireccionamento para uma loja de aplicações ou de qualquer tipo de origens que pode fornecer).
* Escrever o conteúdo que será apresentado no botão de saída (clicando no botão de saída, a vista de texto desaparecerá.)
* Criar a sua campanha de notificações push e irá aparecer na lista de campanha.

![TextView5][25]

* Ative a sua campanha de notificações push para enviar a vista de texto para os seus utilizadores.

![TextView6][26]

* resultado

![TextView7][27]

* O utilizador recebe a notificação e clique no mesmo.
* A vista de texto é apresentado como um pop-up permitindo que o utilizador interagir com ele.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Melhorar a uma notificação Push com uma vista Web
### <a name="what-is-a-web-view"></a>O que é uma vista Web?
![WebView1][28]

Uma vista web é um pop-up com conteúdo da web. Este pop-up aparece quando o utilizador final tiver clicado na notificação push.
Uma vista Web permite-lhe ter mais interação com o utilizador final.
Isto também se a oportunidade para apresentar uma chamada para a ação, tal como o redirecionamento para a App Store, abrir uma página web, enviar uma mensagem de correio eletrónico, a partir de uma pesquisa geolocalizados, etc...

### <a name="example-web-view"></a>Exemplo: Vista de Web
* Criar a sua campanha Push na secção "Alcance" e atribua um nome a sua campanha.

![WebView2][29]

* Escreva a mensagem que será apresentado na notificação.
* Selecione o tipo de conteúdo do anúncio como "web"

![WebView3][30]

### <a name="about-announcement-types"></a>Sobre os tipos de anúncio:
* Apenas notificação: é uma notificação de padrão simple. O que significa que se um utilizador clica no mesmo, será apresentado sem vista adicional, mas ocorrerá apenas a ação associada ao mesmo.
* Anúncio de texto: é uma notificação que envolva ao utilizador para ver uma vista de texto.
* Anúncio Web: é uma notificação que envolva ao utilizador para ver uma vista web.
  Selecione o conteúdo de "Anúncio Web".

> [!NOTE]
> Quando enviar uma vista web, é sempre vem com uma notificação primeiro.

* Definir o conteúdo web (depois de ter selecionado conteúdo de anúncio web, aparecerá a subsecção, permitindo-lhe definir o conteúdo de vista web que pretende apresentar.)

![WebView4][31]

* Escreva o título que será apresentada na parte superior da mensagem (opcional).
* Escreva o seu código HTML aqui.
* Clique na origem de botão de modo para edição de comutador e ver o aspeto que tem de editar.
* Escreva o conteúdo que será apresentado no botão de ação (um botão de ação permite que a aplicação efetuar uma ação específica, tais como abrir uma página da aplicação, redireccionamento para um arquivo ou de qualquer tipo de origens que pode fornecer).
* Escrever o conteúdo que será apresentado no botão de saída (clicando no botão de saída, a vista web desaparecerá).
* resultado

![WebView5][32]

* O utilizador recebe a notificação e clique no mesmo.
* A vista de texto é apresentado como um pop-up permitindo que o utilizador interagir com ele.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md


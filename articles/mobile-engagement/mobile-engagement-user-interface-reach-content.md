---
title: "Interface de utilizador do Azure Mobile Engagement - alcance conteúdo"
description: "Saiba como gerir o conteúdo exclusivo dos diferentes tipos de campanhas de notificações push no Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: add64f06-43c9-475c-8722-51cd00bb844b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3741a43b74af5846e95e42d8a7b533621e780f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Como gerir o conteúdo exclusivo dos diferentes tipos de campanhas de notificações push
Pode utilizar a secção de conteúdo de uma nova campanha de alcance para modificar o conteúdo do anúncios, inquéritos, Pushes de dados e os mosaicos (apenas Windows Phone). A definição de conteúdo das campanhas de Push é específica para o tipo da campanha. 

### <a name="content-types"></a>Tipos de conteúdo:
* Anúncios
* Inquérito
* Pushes de dados
* Mosaicos (apenas Windows Phone)

## <a name="content-of-announcements"></a>Conteúdo de anúncios
 ![Content1 alcance][30] 

### <a name="choose-the-type-of-your-announcement"></a>Selecione o tipo do anúncio:
* Apenas notificação: é uma notificação de padrão simple. O que significa que se um utilizador clica no mesmo, será apresentado sem vista adicional, mas ocorrerá apenas a ação associada ao mesmo.
* Anúncio de texto: é uma notificação que envolva ao utilizador para ver uma vista de texto.
* Anúncio Web: é uma notificação que envolva ao utilizador para ver uma vista web.

### <a name="see-also"></a>Consultar também
* [Alcançar - como Tos - anúncios][Link 3] 

### <a name="about-web-view-announcements"></a>Sobre anúncios de vista Web:
As ocorrências do padrão "{deviceid}" no código HTML ou no código JavaScript que aqui indicadas serão substituídas automaticamente pelo identificador do dispositivo que apresenta o anúncio. Esta é uma forma fácil de obter os identificadores dos dispositivos do Azure Mobile Engagement em serviços web externos alojados no seu back office.
Se pretender criar uma vista web de ecrã inteiro (sem as ação e saída botões predefinidos que fornecemos) pode utilizar as seguintes funções no código de JavaScript do seu anúncio de vista de web: 

* executar a ação de anúncio: ReachContent.actionContent()
* sair do anúncio: ReachContent.exitContent()

### <a name="choose-your-action"></a>Escolha a ação:
### <a name="about-action-urls"></a>Sobre os URLs de ação:
Qualquer URL que pode ser interpretado pelo sistema operativo de um dispositivo pode ser utilizado como um URL de ação.
Qualquer URL dedicado que a sua aplicação suporte (por exemplo, para que os utilizadores acedam a um ecrã específico) também pode ser utilizado como um URL de ação.
Cada ocorrência do padrão {"deviceid"} é substituída automaticamente pelo identificador do dispositivo que executa a ação. Isto pode ser utilizado para obter facilmente os identificadores dos dispositivos do Azure Mobile Engagement através de serviços web externos alojados no seu back office.

* **Android + iOS ações**
  * Abrir uma página web
  * http://\[web-site-domínio\] 
  * Exemplo: http://www.azure.com
  * Enviar um e-mail
  * mailto:\[destinatário do correio electrónico\]? requerente =\[requerente\]& body =\[mensagem\] 
  * Example:mailto:foo@example.com? requerente = saudações % 20from % 20Azure % 20Mobile % 20Engagement! & body = 20stuff boa %!
  * Enviar SMS
  * SMS:\[número de telefone\] 
  * Exemplo: sms:2125551212
  * Marcar um número de telefone
  * Tel:\[número de telefone\] 
  * Exemplo: tel:2125551212
* **Ações apenas Android**
  * Transferir uma aplicação na Play Store
  * Market://details?ID=\[pacote de aplicação\] 
  * Exemplo: market://details?id=com.microsoft.office.word
  * Iniciar uma pesquisa geolocalizados
  * Geo:0, 0? q =\[consulta de pesquisa\] 
  * Exemplo: geo:0, 0? q = starbucks, paris
* **ações apenas do iOS**
  * Transferir uma aplicação na loja de aplicações
  * formado de /app/ [nome da aplicação] http://iTunes.Apple.com/ [País] [id da aplicação]? mt = 8 
  * Exemplo: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
  * Ações de Windows
  * Abrir uma página web
  * http://\[web-site-domínio\] 
  * Exemplo: http://www.azure.com
  * Enviar um e-mail
  * mailto:\[destinatário do correio electrónico\]? requerente =\[requerente\]& body =\[mensagem\] 
  * Example:mailto:foo@example.com? requerente = saudações % 20from % 20Azure % 20Mobile % 20Engagement! & body = 20stuff boa %!
  * Enviar SMS (aplicação de loja Skype obrigatória)
  * SMS:\[número de telefone\] 
  * Exemplo: sms:2125551212
  * Marcar um número de telefone (aplicação de loja Skype obrigatória)
  * Tel:\[número de telefone\] 
  * Exemplo: tel:2125551212
  * Transferir uma aplicação na Play Store
  * MS-windows-arquivo: PDP? PFN =\[ID de pacote de aplicação\] 
  * Exemplo: ms-windows-arquivo: PDP? PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
  * Iniciar uma pesquisa de bingmaps
  * bingmaps:? q =\[consulta de pesquisa\] 
  * Exemplo: bingmaps:? q = starbucks, paris
  * Utilizar um esquema personalizado
  * \[esquema personalizado\]://\[os parâmetros do esquema personalizado\] 
  * Exemplo: myCustomProtocol://myCustomParams
  * Utilizar um pacote de dados (aplicação loja para extensão ler obrigatória)
  * \[pasta\]\[dados\].\[ extensão\] 
  * Example:myfolderdata.txt

### <a name="build-a-tracking-url"></a>Compilar um URL de controlo:
* Consulte a secção "Definições" o <UI Documentation> para instruções sobre como criar um URL de controlo que permitirá aos utilizadores transferir uma das outras aplicações.

### <a name="define-the-texts-of-your-announcement"></a>Definir os textos do anúncio
Preencha o título, o conteúdo e a textos do botão do anúncio. Pode visar um público-alvo de uma campanha futura com base nos comentários alcance da forma como os utilizadores respondeu para esta campanha. Público-alvo filtragem pode basear-se nos comentários de indica se esta campanha foi apenas enviadas por push, respondidas, alvo de ação ou fechadas.

### <a name="see-also"></a>Consultar também
* [IU documentação - alcance - novo critério de Push][Link 28]

## <a name="content-of-polls"></a>Conteúdo de inquéritos
![Content2 alcance][31] 

Preencha o título, a descrição e o textos do botão do anúncio. Em seguida, adicione perguntas e opções para as respostas às suas perguntas.
Pode visar um público-alvo de uma campanha futura com base nos comentários alcance da forma como os utilizadores respondeu para esta campanha. Público-alvo filtragem pode basear-se no se esta campanha foi apenas enviadas por push, respondidas, alvo de ação ou fechadas. Público-alvo filtragem também pode basear-se nos comentários de resposta de consulta, onde a pergunta e resposta escolha são utilizadas como critérios.

### <a name="see-also"></a>Consultar também
* [IU documentação - alcance - novo critério de Push][Link 28]

## <a name="content-of-data-pushes"></a>Conteúdo de Pushes de dados
![Content3 alcance][32] 

### <a name="choose-the-type-of-your-data"></a>Escolha o tipo de dados:
* Texto
* Dados binários
* Dados em Base64

### <a name="define-the-content-of-your-data"></a>Definir o conteúdo dos seus dados
* Caso tenha optado por enviar dados de texto, copie e cole o texto na caixa de "conteúdo".
* Caso tenha optado por enviar dados binários ou base64, utilize o botão "carregar o ficheiro" para carregar o ficheiro.
* Pode visar um público-alvo de uma campanha futura com base nos comentários alcance da forma como os utilizadores respondeu para esta campanha. Público-alvo filtragem pode basear-se no se esta campanha foi apenas enviadas por push, respondidas, alvo de ação ou fechadas.

### <a name="see-also"></a>Consultar também
* [IU documentação - alcance - novo critério de Push][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Conteúdo de mosaicos (apenas Windows Phone)
![Content4 alcance][33]

### <a name="define-the-content-of-your-tile"></a>Definir o conteúdo do mosaico
O payload de mosaico é o texto a ser apresentadas no mosaico da sua aplicação em dispositivos Windows Phone.
Um mosaico push é a versão do serviço de notificação Push da Microsoft (MPNS) de um push nativo para Windows Phone. O tipo de mosaico de push é o único tipo de push que não tenha uma resposta e, por isso, não é possível basear o público-alvo de campanhas futuras nos resultados de uma campanha de emissão de mosaico. 

### <a name="see-also"></a>Consultar também
* [Push nativas de documentação - API de alcance - API][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md


---
title: Interface de utilizador do Azure Mobile Engagement - alcance
description: "Saiba como aceder aos utilizadores da sua aplicação com notificações push utilizando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ce30456e41ff1a2f4824bcb64246ee115fdd1ef7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Como aceder aos utilizadores da sua aplicação com notificações push
Este artigo descreve o **ALCANÇAR** separador do **Mobile Engagement** portal. Utilizar o **Mobile Engagement** portal para monitorizar e gerir as suas aplicações móveis. Tenha em atenção que para começar a utilizar o portal terá primeiro de criar um **Azure Mobile Engagement** conta. Para obter mais informações, consulte [criar uma conta do Azure Mobile Engagement](mobile-engagement-create.md).

A secção de alcance da IU é a ferramenta de gestão de campanha Push onde pode criar/editar/ative/concluir/monitor e obter estatísticas sobre campanhas de notificações Push e as funcionalidades que também podem ser acedidas através da API de alcance (e alguns elementos de nível baixo Push API). Lembre-se de que, se estiver a utilizar as APIs ou o IU, terá de integrar o Azure Mobile Engagement e alcance na sua aplicação para cada plataforma com o SDK antes de poder utilizar campanhas de alcance.

> [!NOTE]
> Demasiadas secções do **Mobile Engagement** portal IU contêm o **Mostrar ajudar** botão. Prima este botão para obter mais informações contextuais sobre uma secção.
> 
> 

## <a name="four-types-of-push-notifications"></a>Quatro tipos de notificações Push
1. Anúncios - permitem-lhe para enviar mensagens de publicidade aos utilizadores que redirecioná-los noutra localização dentro da sua aplicação ou para enviá-los para uma página Web ou arquivo fora da sua aplicação. 
2. Inquérito - permitem-lhe recolher informações dos utilizadores finais ao colocar-lhes perguntas.
3. Pushes de dados - permitem-lhe enviar um ficheiro de dados binários ou base64. As informações contidas num push de dados são enviadas para a aplicação a modificar a experiência de atual dos utilizadores na sua aplicação. A aplicação tem de ser capaz de processar os dados num push de dados.

## <a name="campaign-details"></a>Detalhes da campanha
Pode editar, clonar, eliminar ou ativar campanhas que não tenham sido ativadas ainda por cima os respetivos nomes ou pode clicar para os abrir. Pode clonar campanhas que já tem sido ativadas pelo posicionado sobre os respetivos nomes ou pode clicar para os abrir. No entanto, não é possível alterar uma campanha depois de ser ativado.

![Reach1][18]

## <a name="reach-feedback"></a>Alcançar os seus comentários
Clique em **estatísticas** para ver os detalhes de uma campanha de alcance. O **simples** vista fornece uma representação visual sob a forma de um gráfico de barras de coluna sobre o que aconteceu depois de uma campanha. O **avançadas** vista fornece os detalhes mais granulares sobre a campanha push. Estes detalhes não estará disponíveis se estiver a enviar uma campanha de teste ou seja, um push enviado para um dispositivo de teste. Eis como deve interpretar estes detalhes:

1. **Enviado** -esta ação Especifica o número de mensagens enviadas por push para os dispositivos. Este número dependerá do público-alvo que especificou ao criar a campanha push. Se não especificar qualquer público-alvo, em seguida, este push será enviado para todos os dispositivos registados. Como todos os outros serviços push, iremos as notificações push não diretamente para os dispositivos, mas em vez disso, emiti-las para a respetiva plataforma notificações push específico (PNS - GCM/APNS/WNS) para que eles podem fornecer as notificações para os dispositivos. 
2. **Entregar** -esta ação Especifica o número de mensagens que são fornecidas pelo PNS para o dispositivo e confirmadas com êxito como recebida pelo SDK do Mobile Engagement. 
   
   *Razões para entregues contagem que está a ser inferior à contagem de computadores premida:*
   
   1. Se o utilizador desinstalou a aplicação do dispositivo, mas o PNS desconheça-la no momento, que enviar push para o PNS a mensagem será removida.
   2. Se o dispositivo com a aplicação, mas os dispositivos foram offline durante longos períodos de tempo, em seguida, o PNS falhará entregar a mensagem para o dispositivo. 
   3. Se a mensagem obter entregue no dispositivo, mas o Mobile Engagement SDK na aplicação não reconhecer o conteúdo da mensagem, ignora essa mensagem. Isto pode acontecer se a personalização da notificação na aplicação gerar uma exceção que iremos catch no SDK e remova a mensagem. Isto também pode ocorrer se a aplicação no dispositivo está a utilizar uma versão do SDK do Mobile Engagement que não é capaz de compreender a versão mais recente da mensagem push enviada a partir da plataforma, mas isto é apenas quando a aplicação tiver sido atualizada após a notificação foi distribuída da plataforma de serviço. O **avançadas** separador informará a quantidade de mensagens que foram ignorados. 
   4. Em dispositivos iOS, mensagens, por vezes, não obter entregar se o dispositivo estiver numa bateria ou se a aplicação está a consumir uma quantidade significativa de energia durante o processamento de notificações remotas. Esta é uma limitação dos dispositivos iOS.   
3. **Apresentado** -esta ação Especifica o número de mensagens que são apresentadas com êxito para o utilizador de aplicação no dispositivo sob a forma de uma notificação push/fora-de-aplicação do sistema no Centro de notificações ou uma notificação na aplicação na aplicação móvel.  O **avançadas** separador irá indicar quantos foram as notificações do sistema e quantas foram nas notificações na aplicação. 
   
   *Contagem de razões para apresentadas que está a ser inferior à contagem de entregues (a aguardar a apresentar)*
   
   1. Se a campanha de notificação tinha uma data de fim no mesmo, em seguida, é possível que a notificação foi entregue, mas quando o tempo fornecido para abrir e apresente-a para o utilizador de aplicação, já expirou isso nunca foi apresentado.   
   2. Se a notificação é uma notificação na aplicação, em seguida, a notificação só é apresentada quando o utilizador de aplicação abre a aplicação. Nos casos em que o utilizador de aplicação ainda não abriu a aplicação, o SDK irão comunicar que a notificação foi entregue, mas ainda não apresentada até que a aplicação ser aberta. 
   3. Se a notificação é uma notificação na aplicação e configurado ser mostrada num atividade/ecrã específico, em seguida, também a notificação será reportada como enviada, mas ainda não foram fornecidas até que o utilizador abre a aplicação num ecrã específico. 
4. **Interações do utilizador** -esta ação Especifica o número de mensagens que o utilizador de aplicação tem Interagiu com e incluirá as mensagens que são alvo de ação ou fechadas. 
   
   * *O utilizador de aplicação pode definir uma ação uma notificação em qualquer uma das seguintes formas:*
     
     1. Se a notificação é uma sistema/fora-de-notificação da aplicação ou uma notificação na aplicação enviado como só de notificação, em seguida, o utilizador de aplicação clica na notificação.
     2. Se a notificação é uma notificação na aplicação com um texto ou de vista web ou de inquérito, em seguida, o utilizador de aplicação clica no botão de ação na notificação.
     3. Se a notificação é uma notificação na aplicação com uma vista web, em seguida, o utilizador de aplicação clica num URL na vista web [disponível apenas Android]
   * *O utilizador de aplicação pode sair de uma notificação em qualquer uma das seguintes formas:*
     
     1. Clicar no botão Fechar na notificação diretamente. 
     2. Percorrer ausente ou eliminar a notificação. 
     3. Nas notificações na aplicação com o conteúdo de texto/web e inquéritos, normalmente, são apresentadas ao utilizador da aplicação num processo de dois passos. Verá uma notificação pela primeira vez e quando clicar nela, verá o conteúdo de texto/web/consulta subsequente. O utilizador de aplicação pode sair de uma notificação em qualquer um destes passos e os detalhes na vista avançada captura isto. 
5. **Alvo de ação** -esta ação Especifica o número de mensagens que foram explicitamente alvo de ação pelo utilizador de aplicação. Este é o número mais interessante, isto indica quantos utilizadores da aplicação foram interessados da mensagem que é enviado na notificação. 

> [!NOTE]
> No iOS & Windows abrir plataformas, se o utilizador tiver a aplicação e a campanha foi uma campanha de "Em qualquer altura", em seguida, é possível que ambos fora da aplicação e nas notificações na aplicação são apresentados ao mesmo tempo. Isto pode provocar uma contagem de apresentadas ou superior a entregues. Se o utilizador interage ou ações a notificação, em seguida, mesmo a contagem de utilizador interações/Actioned pode ser maior que entregues. 
> 
> 

![Reach2][19]

## <a name="see-also"></a>Consultar também
* [Conceitos][Link 6]
* [Serviço de guia de resolução de problemas][Link 24]

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


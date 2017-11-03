---
title: Interface de utilizador do Azure Mobile Engagement - Monitor
description: "Saiba como monitorizar dados em tempo real sobre a sua aplicação utilizando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: b91ad89a-b89d-4377-abb0-cc2d16a2836d
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 5f8a02e35db93585e0fe46d77b3ad18b94c99597
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-real-time-data-about-your-application"></a>Como monitorizar dados em tempo real sobre a sua aplicação
Este artigo descreve o **MONITOR** separador do **Mobile Engagement** portal. Utilizar o **Mobile Engagement** portal para monitorizar e gerir as suas aplicações móveis. Tenha em atenção que para começar a utilizar o portal terá primeiro de criar um **Azure Mobile Engagement** conta. 

A secção de Monitor da IU fornece informações de análise em tempo real e permite-lhe definir alertas quando os limiares são atingidos para a maioria dessas mesmas informações que está disponíveis em historicamente o [análise](mobile-engagement-user-interface-analytics.md) secção da IU. Consulte o **glossário** secção o [conceitos](http://go.microsoft.com/fwlink/?LinkId=525555) tópico definições dos termos e abreviaturas na monitorização e de análise (tais como o seguinte: utilizador ativo, novo utilizador, utilizador retido, sessão, gráfico de caminho de utilizador, mapa de utilizadores, URLs de controlo, tendências, atividade, evento, o trabalho, erro, informações adicionais, falhas e App-info).

> [!NOTE]
> Demasiadas secções do **Mobile Engagement** portal IU contêm o **Mostrar ajudar** botão. Prima este botão para obter mais informações contextuais sobre uma secção.
> 
> 

## <a name="monitor---sessions-jobs-events-errors-and-crashes"></a>Monitor - sessões, tarefas, eventos, erros e falhas
Pode ver o número de utilizadores que está atualmente na sessão e ecrãs específicos ou fazer as ações específicas. Pode ver a atividade do utilizador dividida pelo sessões, tarefas, eventos, erros e falhas. Pode ver a informação atual e mostrar as informações de última hora, dia ou semana. Pode ver todas as informações em cada categoria ou a ordenação pelo específica de sessão, o trabalho, eventos, erros e falhas.  A monitorização em direto é útil para utilizar durante a eventos tal uma campanha de emissão para ver se existe um uptick em ação à direita depois de enviar a notificação Push.

![Monitor1][14]  

## <a name="troubleshooting-with-monitor---events---details"></a>Resolução de problemas com o Monitor - eventos - detalhes
Gerar um evento na sua aplicação do seu dispositivo de teste e encontrá-las no Monitor - eventos - detalhes é uma das formas mais fácil para localizar o ID do dispositivo para o seu dispositivo de teste e confirmar que integração do Azure Mobile Engagement de análise, monitorização e segmentos está a funcionar da sua aplicação. Assim que tiver o ID de dispositivo do seu dispositivo de teste, pode adicioná-lo nos seus dispositivos de teste em "conta -"dispositivos da minha. Se não é possível gerar um evento, certifique-se de que o Azure Mobile Engagement corretamente está integrado na sua aplicação Android/iOS/Web/Windows/Windows Phone com o SDK.

Para obter mais informações, consulte: [documentação do SDK][Link 5]

![Monitor2][15]  

## <a name="troubleshooting-with-monitor---crashes---details"></a>Resolução de problemas com o Monitor - falhas - detalhes
Pode rever informações da falha sobre a sua aplicação de detalhes de Monitor - falhas - para ajudar a determinar o motivo pelo qual a aplicação esteja a falhar. Também deve procurar problemas conhecidos com cada versão do SDK nas notas de versão para cada versão do SDK para Android/iOS/Web/Windows/Windows Phone.

Para obter mais informações, consulte: [documentação do SDK - notas de versão][Link 5]

![Monitor3][16]

## <a name="monitor---alerts"></a>Monitor - alertas
Também pode especificar as condições para alertas que serão automaticamente enviados para si através de correio electrónico ou por mensagem instantânea. (Todos os serviços compatíveis com XMPP, como o gtalk, da Google ou ichat, da Apple são suportados). Alertas são baseados num limiar de deteção predefinido maior (>) ou inferior a (<) num número específico de sessões, tarefas, eventos, erros ou falhas por segundo, minuto ou hora. Alertas podem monitorizar todas as atividades de um determinado tipo ou monitorizar apenas uma atividade específica da tarefa, eventos ou erro. 

Também pode especificar uma taxa de deteção mínima, que é a quantidade mínima de minutos que separará duas notificações para o mesmo alerta para se certificar de que quando o alerta é acionado, nunca receberá mais do que 1 notificação por intervalo especificado.

![Monitor4][17]

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

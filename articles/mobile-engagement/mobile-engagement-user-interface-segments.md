---
title: Interface de utilizador do Azure Mobile Engagement - segmentos
description: "Saiba como criar e gerir segmentos de utilizadores para identificar padrões de utilização, utilizando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 087f4a1fef420abe9669f8dfe2b84c7a847ce263
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Como criar e gerir segmentos de utilizadores para identificar padrões de utilização
Este artigo descreve o **segmentos** separador do **Mobile Engagement** portal. Utilizar o **Mobile Engagement** portal para monitorizar e gerir as suas aplicações móveis.

A secção de segmentos da IU permite-lhe trabalhar nos segmentar os utilizadores com base no comportamento diferente e análises que pode aproveitar a aplicação e também pode aceder através da API de segmentos. Segmentos primeiro são calculados 24 horas depois de estes são criados e estes são recomputed a cada 24 horas com base nas mais recentes informações de análise. Assim que é calculado um segmento, apresenta um gráfico de "Dia ao histórico do dia" por dia.

> [!NOTE]
> Demasiadas secções do **Mobile Engagement** portal IU contêm o **Mostrar ajudar** botão. Prima este botão para obter mais informações contextuais sobre uma secção.
> 
> 

## <a name="create-segments"></a>Criar segmentos
Pode criar um segmento com base nos até 10 critérios num período específico cópias de segurança para 60 dias no passado da secção de análise. Por exemplo, pode criar um segmento com base nas pessoas que tem visualizado determinadas páginas ou pesquisou conteúdos específicos na sua aplicação nos últimos 10 dias. Esta informação está disponível na secção de análise. Por isso, pode utilizá-lo para criar um segmento e, em seguida, configure uma notificação push para este subconjunto de utilizadores de destino para obtê-las para voltar para a aplicação. 

> [!NOTE]
> Depois de um segmento tem foi calculado, não pode ser editada; só pode ser clonado (copiado) ou destruição (eliminado). Um segmento pode ser clonado em segurança na mesma aplicação (com o mesmo AppID) e também pode ser clonado em outras aplicações (com um AppID diferente). 

 ![segments1][35] 

## <a name="examples-segments"></a>Segmentos de exemplos
 ![segments2][36]

Segmentos permitem-lhe segmentar os utilizadores finais da sua aplicação.
Segmentar os utilizadores é uma estratégia de marketing importante. O Azure Mobile Engagement permite-lhe obter os dados históricos e criar segmentos personalizados. Esta ferramenta poderosa permite-lhe saber mais sobre a experiência dos seus clientes na sua aplicação. Pode facilmente analisar os seus segmentos e utilizar os seus segmentos como destinos de push.
Um caso de utilização comum é que pretende enviar um push uma notificação a encorajar os utilizadores finais para classificar a aplicação na loja. Em vez de enviar uma notificação a todos os utilizadores finais, pode criar um segmento que especifique apenas os utilizadores que utilizaram a sua aplicação todos os dias durante o último mês e tem tido um excelente experiência de utilizador. Enviar notificações push menos altamente direcionados, obter um melhor ROI.

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Segmentos que pode criar com base nos elementos principais do Azure Mobile Engagement:
* Evento: Crie um segmento de um evento específico que tem como destino da aplicação que ocorreram mais de duas vezes por semana. 
* Sessão: Crie um segmento de utilizadores que tenham utilizado a aplicação mais de 5 vezes última semana.
* Atividade: Crie um segmento de utilizadores que utilizaram um página ou conteúdo maior ou menor do que 10 tempo último mês.
* Tarefa: Crie um segmento de utilizadores que concluiu uma tarefa de mais de duas vezes por dia.
* Falhas: Crie um segmento de todos os utilizadores que tenham uma falha de mais de 10 vezes última semana. (Foi push neste segmento com um apology ou mesmo uma coupon!)
* Erro: Crie um segmento de todos os utilizadores que tenham um erro de mais de 100 vezes últimos 3 dias.
* As informações da aplicação: Crie um segmento que tenha como destino um informações de aplicação personalizada que ocorreram durante os últimos dias 25.
  
  ![segments4][38]

Para utilizar o segmento de uma forma ideal, tem de ter efetuado uma integração personalizada do SDK na sua aplicação com um plano de etiquetagem das etiquetas "As informações da aplicação".
Em seguida, vá para a home page da interface, selecione a aplicação que pretende e clique na secção "Segmentos".

1. Selecione a secção "Segmentos".
2. Clique em "novo segmento de" botão para criar um novo segmento.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Exemplo de vida real: Criar um segmento simple com base nas informações de "Sessão"
Crie um segmento de todos os utilizadores finais que tenham utilizado a aplicação pelo menos 50 vezes na última semana. A partir daí, localize apenas os utilizadores finais que tem gasto, pelo menos, 30 segundos na sua aplicação por sessão. Isto irá mostrar todos os utilizadores finais que tenham uma experiência positiva na sua aplicação. Em seguida, o segmento criado pode ser utilizado para enviar uma notificação para estes utilizadores finais a peça-lhes para classificar a aplicação na loja.

 ![segments5][39]

1. Dê um nome do segmento para encontrar rapidamente na lista "Segmento".
2. Clique no botão "Criar".
   
   ![segments6][40]

Selecione a sessão.

 ![segments7][41]

1. Selecione o período de "Última semana".
2. Clique em Seguinte.
   
   ![segments8][42]
3. Selecione o operador que é relevante entre a lista: =; ≥, ≤.
4. Introduza o número que pretende.
5. Selecione a ocorrência de que pretende. 
6. Clique em Seguinte.
   Este exemplo está definido para corresponder a que, ao longo da última semana, os utilizadores que foram efetuadas pelo menos 50 sessões.
   
   ![segments9][43]

Para a segmentação de sessão, pode escolher o comprimento por sessão como um critério.

1. Selecione o operador da lista.
2. Forneça o comprimento por sessão.
3. Clique em Seguinte.
   Neste exemplo, diz que através de todas as sessões que foi segmentado na secção de ocorrência, selecione apenas os utilizadores que tenham despendido a mais do que 30 segundos por sessão.
   
   ![segments10][44]

Nome do seu critério para obtê-lo no funil completado e clique em Concluir.

 ![segments11][45]

Quando tiver concluído a definição de cópia de segurança seu critério, será apresentada no funil segmento.
Porque um segmento é com base nos dados de análise, os segmentos são calculados uma vez por dia.
Neste exemplo, 47,7% dos utilizadores finais totais correspondência critério. Devem ser os utilizadores que têm tido uma boa experiência e serão provável fornecer uma classificação superior se enviar uma notificação a solicitar-lhes para classificar a aplicação na loja.

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


---
title: Do Azure Mobile Engagement Interface de utilizador - home page
description: "Saiba como gerir a sua aplicação existente e projetos, utilizando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0f15cb975f57f6f5cab12d5118ff50a6fab14388
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-your-existing-application-and-projects"></a>Como gerir a sua aplicação existente e projetos
Este artigo descreve o **home page** página do **Mobile Engagement** portal. Utilizar o **Mobile Engagement** portal para monitorizar e gerir as suas aplicações móveis. Tenha em atenção que para começar a utilizar o portal terá primeiro de criar um **Azure Mobile Engagement** conta. 

Para obter a página inicial, clique em **raiz** na parte superior esquerda da página. Contém a lista de todas as aplicações que fazem parte da coleção escolhida. Nesta página é apresentada apenas uma rápida descrição geral das suas aplicações.

A home page também contém todos os seus projetos que podem incluir qualquer aplicação que está na sua conta. Tenha em atenção que qualquer pessoa pode aceder a home page da IU através da criação de uma conta, mas tem de conceder permissão a outros utilizadores por ordem para os mesmos que têm acesso às suas aplicações personalizadas no **projetos My**.

Também pode ver o gráfico de comparação para as aplicações selecionadas. Em alternativa, optar por visualizar o gráfico de comparação para aplicações selecionadas num projeto.

![Home1][0]

## <a name="my-applications"></a>Minhas aplicações
Descrição geral rápida das suas aplicações permite-lhe selecionar as aplicações que pretende abrir para ver as opções de friso detalhadas. Pode clique no nome da sua aplicação para voltar para a localização de friso mais recentemente visitada na sua aplicação, ou clique no ícone de equipamento para ir diretamente para a página de "Definições" da sua aplicação. Pode pesquisar, filtrar ou ordenar as informações apresentadas nas tabelas de aplicações. Também pode arrastar e largar os cabeçalhos de coluna para alterar a ordem.

Entre outras coisas, a descrição geral das suas aplicações inclui:

* **Tendência de utilizadores novos**: evolução de novos utilizadores nas últimas duas semanas.
* **Utilizadores ativos**: número de utilizadores ativos nos últimos 30 dias.
* **Tendência de utilizadores ativos**: evolução de utilizadores ativos nas últimas duas semanas.
* **Sessões**: uma sessão corresponde a uma utilização da aplicação realizada por um utilizador, desde o momento em que o utilizador começa a utilizá-la, até que a termina.
* **As tendências de sessão**: evolução das sessões nas últimas duas semanas.

Assim que clicar numa aplicação, pode começar a monitorizar e gerir as suas aplicações através da IU. Por exemplo:    

* [Monitorizar dados em tempo real sobre a sua aplicação](mobile-engagement-user-interface-monitor.md)
* [Analisar dados históricos sobre a sua aplicação](mobile-engagement-user-interface-analytics.md)
* [Criar e gerir segmentos de utilizadores para identificar padrões de utilização](mobile-engagement-user-interface-segments.md)
* [Entrar em contacto com os utilizadores da sua aplicação com notificações push](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>A minha projetos
Pode utilizar projetos para agrupar as suas aplicações e atribua permissões a outros utilizadores para aceder às suas aplicações. Atribua permissões a outros utilizadores, fornecendo o endereço de correio eletrónico. O **novo projeto** botão permite-lhe criar um novo projeto introduzindo apenas um "nome" e "Descrição" do seu projeto novo. Depois de um projeto é criado, pode clicar no nome do projeto para editar o nome e descrição do seu produto e para selecionar todas as aplicações que pretende incluir neste projecto.

![Home6][60]

As funções incluem:

* **Visualizador**: um Visualizador é um utilizador que só pode ver as aplicações associadas a um projeto. Um Visualizador pode aceder a análise de monitorizar dados e ver resultados de alcance. Um visualizador não é possível alterar quaisquer informações nem gerir aplicações ou utilizadores. Um visualizador não é possível criar ou alterar o estado de campanhas de alcance.
* **Programador**: um programador é um utilizador que pode fazer tudo um Visualizador pode fazer, bem como gerir aplicações. Um programador pode ativar e desativar aplicações, alterar as informações de aplicações (por exemplo, o pacote de chaves e assinatura) e criar campanhas de alcance. Um programador não consegue gerir utilizadores.
* **Administrador**: um administrador é um utilizador que pode fazer tudo um programador pode fazer, bem como gerir utilizadores. Um administrador pode convidar os utilizadores a participar num projeto, pode alterar funções de utilizador e pode alterar as informações do projeto. Permissões ao nível da aplicação também podem ser definidas em "definições".

Clique num projeto para ver todas as aplicações que fazem parte deste projeto. A imagem seguinte mostra o gráfico de comparação para as aplicações selecionadas.

![Home2][3]

## <a name="see-also"></a>Consultar também
* [Conceitos][Link 6]
* [Serviço de guia de resolução de problemas][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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

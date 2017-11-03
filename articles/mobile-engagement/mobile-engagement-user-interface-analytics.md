---
title: "Interface de utilizador do Azure Mobile Engagement - análise"
description: "Saiba como analisar dados históricos sobre a sua aplicação com o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ad05676919d6c254d60fd010c3f589f663c4745d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-analyze-historical-data-about-your-application"></a>Como analisar dados históricos sobre a sua aplicação
Este artigo descreve o **análise** separador do **Mobile Engagement** portal. Utilizar o **Mobile Engagement** portal para monitorizar e gerir as suas aplicações móveis. Tenha em atenção que para começar a utilizar o portal terá primeiro de criar um **Azure Mobile Engagement** conta.

A secção de análise da IU fornece informação agregada sobre a sua aplicação com base nos dados históricos, que são atualizados a cada 24 horas. As informações são apresentadas em diferentes dashboards compostas de gráficos de barras/linha/circular, grelhas e mapas. Os dados também podem ser transferidos como ficheiros. csv. Na maioria desta mesma informação está disponível em tempo real na secção de Monitor da IU e também pode ser acedido a partir da API de análise.

> [!NOTE]
> Demasiadas secções do **Mobile Engagement** portal IU contêm o **Mostrar ajudar** botão. Prima este botão para obter mais informações contextuais sobre uma secção.

## <a name="standard-and-custom-analytics"></a>Análise de padrão e personalizadas
O Azure Mobile Engagement fornece um conjunto de informações de análise básicas, standard sobre as aplicações que podem ser graphed, assim como integrar a sua aplicação no SDK do. O Azure Mobile Engagement também fornece a capacidade para recolher informações de análise personalizada adicional que pretende sobre o comportamento dos utilizadores finais. Pode fazê-lo através da criação de um plano de etiquetas personalizadas "etiquetas (app-info)", criados a partir de **definições** para que o Azure Mobile Engagement pode recolher estes dados adicionais para si.

## <a name="analytics"></a>Análise
* Dashboard: Mostra informações gerais sobre os seus utilizadores novos e actives e as tendências.
* Os utilizadores: Os utilizadores são identificados pelo identificador do dispositivo: este identificador é exclusivo para cada dispositivo (um utilizador novo corresponde, na verdade, a um dispositivo novo). Um utilizador é considerados como estando novo um determinado intervalo de tempo caso tenham efetuado a sua primeira sessão durante esse intervalo de tempo. Utilizadores são considerados como estando retidos caso tenham efetuado, pelo menos, uma sessão durante os últimos 7 dias. Utilizadores ativos são os utilizadores que efetuou a, pelo menos, uma sessão durante um determinado período. Pode ordenar por, mensalmente, semanais, diária ou numa base horária períodos de tempo. Todos os gráficos semelhante, mas permitem-lhe filtrar por diferentes funcionalidades, tais como a versão da aplicação e, em seguida, para ordenar por um período de tempo. As informações padrão reunidas ao integrar o SDK incluem o seguinte: os utilizadores ativos, o novo utilizador, o número de sessões, o comprimento de cada sessão, informações técnicas sobre o país, locais, a localização, operadora de idioma, dispositivos, firmware, rede (Wi-Fi), as versões da aplicação e SDK, utilizados pelos clientes. Estas informações podem ser visualizadas em tempo real da secção de monitor.

> [!NOTE]
> O período de tempo baseia-se a data a partir das definições de dispositivo dos utilizadores, pelo que um utilizador cujo telefone com a data incorretamente foi apareçam no período de tempo errado.

* Retenção: Os utilizadores são considerados como estando retidos num determinado intervalo de tempo caso tenham efetuado a sua primeira sessão durante esse intervalo de tempo. Pode alterar os intervalos de tempo durante os quais os utilizadores retidos (e os novos utilizadores) são contados para horas, dias, semanas ou meses. A análise de retenção do utilizador é desenvolvida cohorts. Um coorte é o conjunto de novos utilizadores detetados para um determinado período (ou seja, o conjunto de utilizadores efetuar a primeira sessão durante este período). Utilizamos cohorts de 1 dia, dia 2, 4-dia, 7 dias ou 1 mês. Tendo em conta um coorte, cada 1 dia, dia 2, 4-dia, 7 dias, ou 1 mês, do Azure Mobile Engagement calcula o conjunto de todos os utilizadores que pertencem aos coorte e são ainda ativo (ou seja, o conjunto de utilizadores que executou, pelo menos, uma sessão durante o período de). Este conjunto de utilizadores é designado por uma versão de coorte. (O azure Mobile Engagement pode apresentar-lhe quantos dos seus utilizadores ainda estiver a utilizar a aplicação, mas apenas o arquivo específicas de plataforma pode indicar-lhe quantos dos seus utilizadores desinstalar a aplicação – por exemplo, GooglePlay Store do iTunes, Windows, etc.).
* Sessões: Uma utilização da aplicação por um utilizador. As sessões são geradas a partir da sequência das atividades realizadas pelos utilizadores (uma atividade é normalmente associada a utilização de um ecrã da aplicação, mas isto pode variar, dependendo da forma o SDK foi integrado na aplicação). Um utilizador apenas pode executar uma atividade num momento: uma sessão é iniciada assim que o utilizador começa a primeira atividade e termina quando este conclui a última atividade. Se um utilizador mantém-se mais do que alguns segundos sem efetuar qualquer atividade, em seguida, STA sequência de atividades está dividida em duas sessões distintas.
* Atividades: Os nomes de cada ecrã na sua aplicação e o período de tempo que os utilizadores passam cada ecrã. As atividades são uma opção de análise personalizada que será correspondem para as etiquetas "as informações da aplicação" que configurou para a sua própria aplicação:
* Caminho de utilizador: Mostra a forma como os utilizadores navegam nas atividades (ecrãs) da sua aplicação. Pode mover o controlo de deslize para ajustar o nível de detalhes. Nós azuis representam as atividades da aplicação. O tamanho é proporcional ao tempo que os utilizadores despendem no mesmo. Os nós brancos representam o início de sessão e fim. Os nós vermelhos representam as falhas. As ligações representam as transições entre atividades da aplicação (ou entre atividades e falhas). Clique num nó ou numa ligação para ver uma sugestão com mais informações sobre os dados: o tempo despendido num ecrã específico, a contagem de transições e a percentagem de transições entre a atividade de origem para a atividade de destino. (Um---60%---> B significa que os utilizadores na atividade A passam para a atividade B 60% das vezes.) -Pode reorganizar o gráfico esclarecer que; posição é guardada sempre que faz uma alteração. Pode mostrar ou ocultar as falhas para simplificar o gráfico.
* Eventos: Específicas as ações executadas por um utilizador na aplicação. A distribuição de eventos é mostrada como a contagem de eventos por utilizador por sessão. Um evento representa uma ação instantânea; por exemplo, um clique num botão ou a receção de uma notificação. (O significado dos eventos depende da forma como o SDK foi integrado na aplicação.) Um evento pode ocorrer durante uma sessão ou uma tarefa ou pode ser autónomos.
* Tarefas: Semelhante a eventos, exceto se concentrar-se ao comprimento da ação. Por exemplo, as tarefas foi indicam informações técnicas sobre quanto tempo os conteúdos de carga ou uma chamada para o serviço web. Foi também mostram quanto tempo um utilizador para preencher um formulário, crie uma conta ou fazerem uma compra. Uma tarefa representa a duração de uma tarefa, por exemplo, a duração de uma tarefa de transferência ou o tempo de uma faixa é apresentada no ecrã. (O significado das tarefas depende da forma como o SDK foi integrado na aplicação.) As tarefas são normalmente associadas a tarefas de segundo plano que são executadas fora do âmbito de uma sessão (ou seja, sem qualquer atividade de utilizador).
* Technicals: A informações técnicas sobre os dispositivos dos utilizadores da sua aplicação que pode acompanhar, tais como região, operadora, rede, dispositivos, Firmware e o tamanho dos dispositivos dos utilizadores e a versão da sua aplicação e a versão SDK utilizada na sua aplicação de ecrã.
* Erros: Informações sobre os erros técnicas dentro da aplicação que não causam a aplicação de falhas. Um erro representa um problema momentâneo; por exemplo, uma falha de rede ou manipulação incorreta. (O significado dos eventos depende da forma como o SDK foi integrado na aplicação.) Um erro pode ocorrer durante uma sessão ou uma tarefa ou pode ser autónomos.
* Falhas: Informações sobre os erros que fazer com que a aplicação de falhas. Uma falha é uma condição inesperada na qual a aplicação deixa de executar as funções esperadas e tem de ser parada. Uma falha é normalmente devido a um erro na aplicação.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Aceder a descrição geral de retenção
![Analytics3][12]

A descrição geral de retenção é apresentada no meio para várias placas, cada Mostrar a descrição geral para um determinado período de retenção. O período de retenção de 2 dias é mostrado no exemplo. As outras placas mostram os períodos de retenção de 4-dia e 7 dias.

## <a name="understanding-the-retention-overview-cards"></a>Noções sobre os cartões de descrição geral de retenção
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Cada cartão é composta por 3 partes principais:
1. 1: o coorte e o período de considerados
2. 2-4: o período de retenção para o período atual
3. 5: Gráfico Sparkline do histórico

### <a name="here-is-detailed-information-about-each-element"></a>Segue-se informações detalhadas sobre cada elemento:
1. Coorte e período: este título fornece o tipo de coorte. Aqui "período de 2 dias" significa que iremos abordar o comportamento dos utilizadores mais de 2 dias, os utilizadores que chegaram durante um período de 2 dias bem como se ligam nos seguintes blocos de 2 dias. O exemplo acima considera a atividade dos utilizadores entre as 21st e 22nd de Novembro.
2. Isto dá-a taxa de retenção de 21 e 22 de Novembro para os utilizadores que chegam numa 19 e 20 de Novembro. Aqui iremos tinha 1 utilizador Active Directory entre os 21st e 22nd, através de 3 foram novos utilizadores entre o 19th e 20th.
3. Este visual indicador fornece as mesmas informações que acima graficamente representado. (O terceiro do círculo é o número de 33%.) A cor dá-informações adicionais: verde indica que este número é a crescer de cálculo anterior. Amarelo significa estável e vermelha significa diminuir.
4. Isto indica que os valores utilizados para o cálculo.
5. Este é um gráfico Sparkline do histórico dos valores de retenção. Permite-lhe ver os valores no passado para uma vista abrangente de forma evoluiu e deu lugar.

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

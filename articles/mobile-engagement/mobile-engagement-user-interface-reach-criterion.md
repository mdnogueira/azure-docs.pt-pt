---
title: "Interface de utilizador do Azure Mobile Engagement - critério de alcance"
description: "Saiba como utilizar os critérios de filtragem para enviar as campanhas push para um subconjunto selecionado dos seus utilizadores utilizando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 803b44721d0ab1ac7b5a8074e18857fc57adb724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Como utilizar os critérios de filtragem para enviar as campanhas push para um subconjunto selecionado dos seus utilizadores
Segmentação do seu público-alvo por critérios específicos, com o botão "Novo critério" é um dos conceitos mais poderosos no Azure Mobile Engagement que ajuda a enviar relevantes notificações push que os clientes responderá em vez de spam todas as pessoas. Pode limitar o seu público-alvo com base nos critérios padrão e simular pushes para determinar quantas pessoas irão receber a notificação.

**Consulte também:**

* [IU documentação - alcance - nova a campanha Push][Link 27]

## <a name="audience-criteria-can-include"></a>Critérios de audiência podem incluir:
* * * Technicals: * * pode visar com base nas mesmas informações técnicas, pode ver secções de análise e Monitor. **Consulte também:** [IU documentação - análise][Link 15], [IU documentação - Monitor][Link 16]
* **Localização:** as aplicações que utilizam o "relatórios de localização em Tempo Real" com geométrico podem utilizar a Geolocalização como um critério para um público-alvo da localização GPS de destino. Chamada de "Relatórios de localização de área em diferido" também ser utilizado para um público-alvo da localização de telefone de célula de destino ("relatórios de localização em Tempo Real" e "Lento área Relatórios de localização" tem de ser ativado do SDK). **Consulte também:** [documentação do SDK - iOS - integração][Link 5], [integração de documentação do SDK - Android -][Link 5]
* **Comentários de alcance:** pode segmentar o público-alvo com base nos comentários de notificações de alcance anteriormente comentários do alcance de anúncios, inquéritos e Pushes de dados. Isto permite-lhe para melhor destino seu público-alvo após as duas ou três campanhas de alcance que foi a primeira vez. Também pode ser utilizado para filtrar os utilizadores que já receberam uma notificação com conteúdo semelhante, definindo uma campanha não sejam enviados para os utilizadores que já receberam uma campanha anterior específica. Ainda pode excluir os utilizadores que estão incluídos uma campanha específica que ainda está ativa de receber Pushes de novo. **Consulte também:** [IU documentação - alcance - Push conteúdo][Link 29]
* **Controlo da instalação:** pode controlar as informações com base no onde os utilizadores instalaram a aplicação. **Consulte também:** [IU documentação - definições][Link 20]
* **Perfil de utilizador:** pode destino com base nas informações de utilizador padrão e pode destino com base nas informações de aplicação personalizada que criou. Isto inclui os utilizadores que estão atualmente sessão iniciados e os utilizadores que têm respondeu às perguntas específicas que pediram-los para definir na aplicação em vez de apenas como ter respondeu ao campanhas anteriores. Todas as suas informações de aplicação definida para a aplicação que aparecem nesta lista.
* Segmentos: Também pode destino com base em segmentos que tenha criado com base no comportamento de utilizador específico que contém vários critérios. Todos os seus segmentos definidos para a sua aplicação mostram nesta lista. **Consulte também:** [IU documentação - segmentos][Link 18]
* **As informações da aplicação:** etiquetas de informações de aplicação personalizada pode ser criadas a partir de "Definições", para controlar o comportamento do utilizador. **Consulte também:** [IU documentação - definições][Link 20]

## <a name="example"></a>Exemplo:
Se pretender emitir um anúncio apenas para o conjunto secundárias dos utilizadores que executaram uma ação de compras via aplicação.

1. Aceda à sua página de definições de aplicação, selecione o menu de "As informações da aplicação" e selecione "Novo as informações da aplicação"
2. Registar uma nova booleano as informações da aplicação chamada "inAppPurchase"
3. Tornar a sua aplicação definido como "true" quando o utilizador com êxito efetua uma compra via aplicação nestas informações da aplicação (utilizando o sendAppInfo ("inAppPurchase",...) função)
4. Se não quiser fazê-lo a partir da sua aplicação, pode fazê-lo do seu back-end utilizando o API do dispositivo)
5. Em seguida, apenas terá de criar o anúncio, com um critério de limitar o seu público-alvo para os utilizadores terem "inAppPurchase" definida como "true")

> [!NOTE]
> Filtragem com base nos critérios que não sejam etiquetas de informações da aplicação requer o Azure Mobile Engagement recolher informações dos dispositivos dos utilizadores antes de push é enviada e, de modo pode fazer com que um atraso. Configuração de push complexas opções (por exemplo, atualizar destaques) também podem atrasar pushes. A utilização de uma campanha de "uma captura" a partir da API de Push é o método mais rápido de push absoluto no Azure Mobile Engagement. Utilizar etiquetas de informações de aplicação apenas como critério de push para uma campanha de alcance (a partir da API de alcance ou a IU) é o seguinte método mais rápido, uma vez que as etiquetas de informações de aplicação são armazenadas no lado do servidor. A utilização de outros critérios de filtragem para uma campanha de push é o método de push mais flexíveis, mas mais lenta, uma vez que o Azure Mobile Engagement tem de consultar os dispositivos para poder enviar a campanha.

![Criterion1 alcance][29] 

## <a name="criterion-options-apply-to"></a>Opções de critério aplicam-se a:
* **Technicals**     
* Nome do firmware: nome do Firmware
* Versão de firmware: versão de Firmware
* Modelo do dispositivo: modelo do dispositivo
* Fabricante do dispositivo: fabricante do dispositivo
* Versão da aplicação: versão de aplicação
* Nome da operadora: nome da operadora, não definida
* País operadora: país operadora, não definida
* O tipo de rede: o tipo de rede
* Região: região
* Tamanho do ecrã: tamanho do ecrã
* **Localização**      
* Última conhecido área: país, região, Localidade
* Geométrico em tempo real: lista de POIs (nome, ações), POI Circular (nome, Latitude, Longitude, Radius no medidores)
* **Alcançar os seus comentários**     
* Comentários do anúncio: anúncio, comentários
* Consultar comentários: inquérito, comentários
* Consultar a comentários de resposta: consultar os comentários de resposta, pergunta, escolha
* Comentários de Push de dados: enviar por Push dados, comentários
* **Controlo da instalação**     
* Arquivo: Armazenamento, não definido
* Origem: Origem, não definida
* **Perfil do utilizador**     
* Sexo: male ou female, não definida
* Data de nascimento: operador, data, não definida
* Optar ativamente por participar no: VERDADEIRO ou FALSO, indefinido
* **Informações da aplicação**      
* Cadeia: Cadeia, não definida
* Data: operador, data, não definida
* Número inteiro: operador, número, não definida
* Valor booleano: true ou false, não definida
* **Segmento**    
* Nome de segmentos (a partir da lista pendente), exclusão (segmente utilizadores que não fazem parte deste segmento).

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


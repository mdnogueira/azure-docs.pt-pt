---
title: Interface de utilizador do Azure Mobile Engagement - campanhas de alcance
description: "Laern como criar e gerir a notificação push campanhas utilizando o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fc88db8db11d1ed12fa95c2087c9a32b21bf4de5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Como criar e gerir campanhas de notificações push
Pode utilizar a secção de alcance da IU para criar uma nova campanha Push com uma fórmula complexa, fornecendo todas as informações que necessárias para enviar uma notificação push. As opções de uma campanha Push variam ligeiramente, consoante os tipos de quatro campanha: anúncios, inquéritos, Pushes de dados e os mosaicos (apenas Windows Phone).

### <a name="option-applies-to"></a>Opção aplica-se a:
* Idiomas: Todos os (anúncios, inquéritos, Pushes de dados, mosaicos)
* Campanha: Todos os (anúncios, inquéritos, Pushes de dados, mosaicos)
* Notificação: Anúncios, inquéritos
* Conteúdo: Exclusivo para cada tipo de campanha
* Audiência: Todos os (anúncios, inquéritos, Pushes de dados, mosaicos)
* Período de tempo: mosaicos de anúncios, inquéritos,
* Teste: Todos os (anúncios, inquéritos, Pushes de dados, mosaicos)

![Campaign1 alcance][20]

## <a name="languages"></a>Linguagens
Pode utilizar o menu de lista pendente de idiomas para enviar uma versão diferente do seu Push para dispositivos que estão definidos como utilizam idiomas diferentes. Por predefinição, todos os dispositivos irão receber o Push mesmo, independentemente da que idioma que estão definidas para utilizar. Os utilizadores com o respetivo dispositivo definido para um idioma diferente irão receber a versão de idioma predefinido do Push. Muitas das opções de campanha push permitem-lhe especificar conteúdo alternativo para cada um dos idiomas adicionais que selecionar. 

![Campaign2 alcance][21]

### <a name="language-differences-apply-to"></a>Diferenças de idioma aplicam-se a:
* Idiomas: É possível selecionar idiomas exclusivos para além do idioma predefinido
* Campanha: Mesmos para todos os idiomas
* Notificação: Exclusivo para cada idioma além o idioma predefinido
* Conteúdo: Exclusivo para cada idioma além o idioma predefinido
* Audiência: Podem ser filtrados por um critério de idioma individual
* Período de tempo: mesmo para todos os idiomas
* Teste: Poderão ser enviadas para cada idioma num momento

### <a name="supported-languages"></a>Idiomas suportados:
* Árabe (ar) 
* Búlgaro (bg) 
* Catalan (AC) 
* Chinês (zh) 
* Croata (RH) 
* Czech (CS) 
* Dinamarquês (da) 
* Neerlandês (nl) 
* Inglês (en) 
* Finlandês (fi) 
* Francês (fr) 
* Alemão (de) 
* Grego (el) 
* Hebraico (ele) 
* Hindi (Olá) 
* Húngaro (hu) 
* Indonesian (id) 
* Italiano () 
* Japonês (ja) 
* Coreano (ko) 
* Letão (lv) 
* Lituano (lt) 
* Malaio (macrolanguage) (ms) 
* Norueguês Bokmål (nb) 
* Polaco (LP) 
* Português (pt) 
* Romeno (ro) 
* Russo (ru) 
* Sérvio (sr) 
* Eslovaco (sk) 
* Esloveno (sl) 
* Espanhol (es) 
* Sueco (sv) 
* Tagalog (tl) 
* Tailandês (ésimo) 
* Turco (seg) 
* Ucraniano (RU) 
* Vietnamita (vi) 

## <a name="campaign"></a>Campanha
Pode utilizar a secção da campanha para definir o nome e a categoria da sua campanha, bem como se pretender ignorar a secção de público-alvo de uma campanha de emissão e enviar esta campanha através da API de alcance (e alguns elementos com o API de Push de nível baixo) em vez disso. Categorias podem ser utilizadas com um modelo de notificação personalizada para controlo nas notificações na aplicação com base nas definições predefinidas. Pode obter uma lista das suas existentes "categorias" através da API de alcance.

> [!WARNING]
> Se utilizar a opção "Ignorar audiência; push será enviado aos utilizadores através da API" na secção "Campanha" de uma campanha de alcance, a campanha não enviará automaticamente, terá de enviá-lo manualmente através da API de alcance.

![Campaign3 alcance][22]

### <a name="option-applies-to"></a>Opção aplica-se a:
* Nome: todos os
* Categoria: Anúncios, inquéritos
* Ignorar audiência; push será enviado aos utilizadores através da API: todos os

## <a name="notification"></a>Notificação
Pode utilizar a secção de notificação para definir definições básicas para a sua push, incluindo: O título do Push, a mensagem, uma imagem de na aplicação, ou se for dispensáveis. Muitas definições de notificação são específicas da plataforma do dispositivo. Pode selecionar se o push será enviado "na aplicação" ou "fora da aplicação" ou ambos. (Lembre-se de que os utilizadores podem "optar ativamente por participar" ou "ativamente" de "fora de aplicação" Pushes no sistema operativo nível nos respetivos dispositivos e o Azure Mobile Engagement não conseguirá substituir esta definição. Lembre-se também de que a API de alcance processa "na aplicação" e "fora da aplicação" Pushes. A API de Push pode ser utilizada para processar demasiado pushes "fora da aplicação".) Pushes podem ser personalizadas com imagens ou conteúdo HTML, incluindo ligações avançadas para ligar de fora da sua aplicação ou para outra localização na sua aplicação (Android SDK 2.1.0 ou posteriores categorias intenção necessárias). Pode alterar o destaque ícone ou iOS e enviar o conteúdo web ou de texto (um pop-up com ligação de conteúdo, URL html para outra localização dentro ou fora da aplicação). Também pode fazer com que os dispositivos Android toque ou Vibre com Push. (Lembre-se de que precisa do correto SDK as permissões no Android manifestam do ficheiro para anel ou Vibre um dispositivo.) Não é atualmente nenhum setor padrão para tamanhos de Android "panorama geral", uma vez que os tamanhos de ecrã são diferentes em todos os dispositivos, mas as imagens de 400 x 100 funcionam em praticamente qualquer tamanho do ecrã.

### <a name="delivery-types"></a>Tipos de entrega:
* Apenas fora da aplicação: a notificação será entregue quando o utilizador não utiliza a aplicação.
* Fora de notificação de aplicação apenas requer um certificado da Apple ou da Google (certificado APNS ou GCM).
* Na aplicação apenas: A notificação é apresentada apenas quando a aplicação está em execução.
* A notificação utiliza o sistema de entrega Capptain para contactar o utilizador. Pode personalizar completamente a esquema/apresentação visual do seu push.
* Sempre: Esta opção garante que enviar uma notificação que a aplicação está em execução ou não.

![Campaign4 alcance][23]

### <a name="option-applies-to"></a>Opção aplica-se a:
* Notificação: Anúncios, inquéritos

## <a name="content"></a>Conteúdo
Pode utilizar a secção de conteúdo para modificar o conteúdo do anúncios, inquéritos, Pushes de dados e os mosaicos (apenas Windows Phone). A definição de conteúdo das campanhas de Push é específica para o tipo da campanha. 

### <a name="see-also"></a>Consultar também
* [Documentação de IU - chegar - Push o conteúdo][Link 29]

![Campaign5 alcance][24]

## <a name="audience"></a>Audiência
Pode utilizar a secção de público-alvo para definir uma lista de itens para limitar a sua campanha ou os limites a sua campanha com base nos critérios personalizados padrão. O conjunto padrão de opções para limitar o seu público-alvo permite-lhe enviar para o novo ou antigos utilizadores ou apenas utilizadores de push nativo. Também pode definir uma quota para limitar o número de utilizadores que recebem push. Pode editar manualmente a expressão para como a sua campanha está filtrada para incluir um ou mais critério para os utilizadores de destino. Pode escrever manualmente uma expressão de audiência. Uma expressão deste tipo tem de definir explicitamente a relação entre critérios. Um critério é descrito por um identificador que tem de começar com uma letra em maiúsculas e não pode conter espaços. A relação entre critérios pode ser descrita através de 'e', 'ou', 'not' operadores, bem como '(',')'. Exemplo: "Criterion1 ou (Criterion1 e não Criterion2)".

> [!NOTE]
> Com um grande público-alvo incluído no campanhas, lado do servidor filtragem análise pode ser lento, especialmente se tentar iniciar várias campanhas ao mesmo tempo.

* Se for possível, inicie uma campanha cada vez.
* No máximo, inicie apenas quatro campanhas cada vez.
* Emitir apenas para os utilizadores do Active Directory (caixa de verificação "interagir apenas com utilizadores que podem ser contactados através de Push nativo" e "Interagir com apenas os utilizadores ativos") para que apenas os utilizadores que ainda tenham a aplicação instalada e utilização-lo, terá de ser analisados.
  Assim que o seu público-alvo estiver definida, pode utilizar o botão simular para determinar quantos utilizadores irão receber este Push. Isto irá calcule o número de utilizadores conhecidos potencialmente segmentados desta audiência (trata de uma estimativa baseada numa amostra aleatória de utilizadores). Lembre-se de que os utilizadores que desinstalaram a aplicação também fazem parte desta audiência, mas não não possível aceder.

### <a name="see-also"></a>Consultar também
* [IU documentação - alcance - novo critério de Push][Link 28]

![Campaign6 alcance][25]

### <a name="edit-expression"></a>Editar expressão
![Campaign7 alcance][26]

### <a name="limit-your-audience-option-applies-to"></a>Limite que a opção de audiência aplica-se a:
* Interagir apenas com um subconjunto de utilizadores: todos os (anúncios, inquéritos, Pushes de dados, mosaicos)
* Interagir apenas com antigos utilizadores: todos os (anúncios, inquéritos, Pushes de dados, mosaicos)
* Interagir apenas com novos utilizadores: todos os (anúncios, inquéritos, Pushes de dados, mosaicos)
* Interagir apenas com Inativos utilizadores: os mosaicos de anúncios, inquéritos,
* Interagir apenas com Active Directory utilizadores: todos os (anúncios, inquéritos, Pushes de dados, mosaicos)
* Interagir apenas com utilizadores que possam ser contactados através de Push nativo: anúncios, inquéritos

## <a name="time-frame"></a>Período de tempo
Pode utilizar a secção de período de tempo para definir quando o push será enviado ou pode deixar o intervalo de tempo em branco para iniciar imediatamente a campanha. Lembre-se de que utilizar fuso horário dos utilizadores finais pode começar a campanha por dia anterior de espera para os utilizadores finais na Ásia e enviar pequenos lotes de pushes cada vez até que todos os fusos horários no mundo corresponder o intervalo de tempo definido para a sua campanha. Utilizar fuso horário dos utilizadores finais pode também provocar atrasos em campanhas, uma vez que tem do tempo do pedido do telefone antes de iniciar o push.

> [!NOTE]
> As campanhas sem uma data de fim pode colocar em cache pushes localmente e ainda as pode apresentar depois de concluir manualmente campanhas. Para evitar este comportamento, específicos de uma hora de fim de campanhas.

### <a name="see-also"></a>Consultar também
* [Alcançar - como Tos – agendamento][Link 3] 

![Campaign8 alcance][27]

### <a name="settings-apply-to"></a>Aplicam as definições para:
* Período de tempo: mosaicos de anúncios, inquéritos,

## <a name="test"></a>Teste
Pode utilizar a secção de teste para enviar este push para o seu próprio dispositivo de teste antes de guardar a campanha. Se tiver configurado qualquer idiomas personalizados para esta campanha, pode testar o push em cada idioma. Pode configurar um dispositivo de teste de "A minha conta".

> [!NOTE]
> Pushes não do lado do servidor, os dados são registados quando utiliza o botão para "teste", os dados são registados apenas para as campanhas real push.

### <a name="see-also"></a>Consultar também
* [Documentação de IU - minha conta][Link 14]

![Campaign9 alcance][28]

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


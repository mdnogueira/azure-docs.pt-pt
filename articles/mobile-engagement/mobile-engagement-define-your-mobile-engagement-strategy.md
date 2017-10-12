---
title: "Definir a sua estratégia de Mobile Engagement | Microsoft Docs"
description: "Saiba como integrar e otimizar o seu Mobile Engagement com notificações push e de análise."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7533e318-81b9-4360-aace-b7be8225985b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: db68bd3ebf7efde1d23820445f07c83c8c27dc20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="define-your-mobile-engagement-strategy"></a>Definir a sua estratégia de Mobile Engagement
*Escreveu a aplicação por um motivo: para que os seus utilizadores a utilizem!*

Acreditamos que certamente dedicou um elevado grau de esforço para torná-la uma aplicação excelente que os utilizadores vão adorar. Provavelmente também investiu uma parte significativa do orçamento em marketing para obter utilizadores. Mas após o pico inicial entusiasmante de utilizadores, é possível que lentamente deixem de utilizar a aplicação. *A essência do Azure Mobile Engagement reside precisamente aqui!*: fazer com que os utilizadores continuem ativos e permitir que melhore gradualmente a aplicação através de testes e processos de aprendizagem.

A nossa abordagem para a melhoria da retenção e da utilização é baseada no envolvimento dos utilizadores da aplicação através de notificações push e mensagens na aplicação, mas de uma forma muito especial, com mensagens e comunicações adaptadas aos mesmos, cada uma de acordo com o comportamento dos utilizadores na sua aplicação. O nosso objetivo é permitir-lhe comunicar com o público-alvo correto, no momento certo e no local certo.

Mas para isso será necessário começar por *compreender os seus utilizadores* e então criar grupos com base naquilo que fizeram ou nas respetivas caraterísticas (aquilo a que chamamos segmentos) e, em seguida, criar comunicações relevantes para cada segmento.

## <a name="mobile-engagement-serves-your-objectives"></a>O Mobile Engagement serve os seus objetivos
*Falamos sobre retenção e utilização, mas para quê?*

A criação da sua estratégia de Mobile Engagement exige que comece por analisar os objetivos e os indicadores chave de desempenho (KPIs) da sua aplicação.

Comece por definir os objetivos e KPIs que o ajudam a definir os casos de utilização de envolvimento através da perspetiva ideal.

Os seus casos de utilização são uma lista simples de campanhas que gostaria de fazer para comunicar com os seus utilizadores, que vão desde umas simples boas-vindas à notificação de utilitários extremamente avançados acionada pelo seu sistema de TI. Um caso de utilização bem construído tem de incluir, pelo menos, o trio *o quê-quem-quando*:

1. Uma designação bastante breve (por exemplo, uma “Campanha de boas-vindas”).
2. **O quê**: um exemplo de mensagem (por exemplo, “Estamos satisfeitos por estar connosco! Não se esqueça de iniciar sessão para obter o 1.º mês gratuito!”). Esta mensagem não é de todo definitiva. Pode alterá-la sempre que pretender, mas normalmente é útil para começar a pensar naquilo que queremos dizer.
3. **Quem**: o segmento que irá receber esta mensagem (por exemplo, “Todos os utilizadores que iniciaram a aplicação pela primeira vez há 3 dias, visitaram a página de início de sessão, mas não iniciaram sessão”).
   * Sim, pode fazer isto muito facilmente com o Azure Mobile Engagement :)
   * Reiteramos que isto não tem de ser definitivo, pois pode definir os seus segmentos em qualquer altura, mas é importante definir antecipadamente os seus critérios de segmentação para garantir que recolhe os dados adequados.
4. **Quando**: o período de tempo da sua campanha. Poderá ser numa determinada data ou após uma ação específica, com base num acionador. O Mobile Engagement oferece uma quantidade importante de possibilidades para que a sua comunicação chegue no momento certo.

Assim que os casos de utilização e o segmento estejam definidos, fornece uma diretriz para definir os dados que devem ser recolhidos numa aplicação. Esta é a função de um *“plano de etiqueta”*. Um plano de etiqueta permite-lhe garantir que a recolha de dados é especificada para os programadores. Por conseguinte, os programadores são capazes de incorporar o Mobile Engagement com a configuração correta para que trabalhe as suas campanhas com os dados adequados. Também será muito importante executar testes para garantir que a integração está correta e que recolhe aquilo de que necessita.

Com base na integração, assim que as aplicações são publicadas, na qualidade de comerciante, conseguirá ver as suas análises em tempo real, segmentar o seu público-alvo e, em seguida, começar a enviar notificações push inteligentemente segmentadas para envolver os utilizadores finais dentro ou fora da aplicação.

### <a name="use-cases-to-get-started"></a>Casos de utilização para começar
1. Estratégia de boas-vindas: crie várias campanhas de notificações push baseadas no comportamento do utilizador final no momento da execução da aplicação para um novo envolvimento em D+2/5/10/15 após a primeira sessão e aumentar a retenção na primeira execução.
2. Promova um novo conteúdo (funcionalidade, artigo/vídeo ou produto) com base no comportamento do utilizador final para enviar as informações apenas aos utilizadores finais que têm maior probabilidade de interagir.
3. Classificar a aplicação: segmente menos de 1 por cento da sua base de utilizadores que tem maior probabilidade de classificar a avaliação com 5 estrelas na loja.
4. Impulsionar as subscrições: promova conteúdos importantes para os utilizadores finais que ainda não tenham contribuído para o aumento das subscrições.
5. Tutorial: acabaram os tutoriais obrigatórios para toda a gente. Porque não criar tutoriais excelentes na aplicação e, em seguida, acioná-los através de mensagens na aplicação apenas se o utilizador parecer não utilizar a aplicação ou tiver dificuldade em utilizar uma funcionalidade?

## <a name="why-do-you-need-analytics-to-engage"></a>Por que motivo precisa de análises para o envolvimento?
Como se pode ter apercebido neste momento, efetuar apenas uma notificação push de difusão não é suficiente. O conceito central do Mobile Engagement é ajudar os comerciantes e os programadores no envolvimento com o utilizador final certo, no momento certo e no local certo. Para conhecer estes três conceitos principais, é essencial recolher análises da sua aplicação e, em seguida, utilizá-las para segmentar o seu público-alvo. Isto também é ainda mais poderoso quando os segmentos de comportamento complementam os dados da sua outra base de dados ou CRM ou a partir de um canal cruzado. O Mobile Engagement permite a obtenção de dados a partir de qualquer lugar e utiliza-os para segmentar o público-alvo adequado.

Para ser o mais contextual possível no envolvimento com o seu público-alvo, é fundamental ter conhecimentos sobre o comportamento dos utilizadores finais e conhecer o respetivo estado em tempo real. A recolha de dados permite que os comerciantes se concentrem naquilo que realmente interessa para reproduzir casos de utilização e alcançar os seus objetivos de estratégia de Mobile Engagement. Alcançar os objetivos definidos anteriormente é também o motivo pelo qual a melhor prática não é na realidade recolher todos e quaisquer dados nas análises, mas apenas os dados que lhe permitem focar-se naquilo que pretende saber e nos casos de utilização. Este é a forma ideal para começar, experimentar, testar e aprender a utilizar a solução e endereçar a notificação push inteligente e aumentar a retenção de uma aplicação para colocá-la ao nível de uma história de sucesso.

> [!NOTE]
> Lembre-se: demasiados dados inutilizam os dados!
> 
> 

### <a name="use-cases-and-best-practices"></a>Casos de utilização e melhores práticas
Nas secções seguintes são brevemente discutidos alguns casos de utilização chave de clientes com os quais nos temos deparado para o ajudar a começar.

#### <a name="media"></a>Meios de comunicação social
Recolha o tipo de conteúdo que é consumido pelo utilizador final e, em seguida, segmente o público-alvo com base neste comportamento para visar tipos específicos de conteúdo apenas para um público-alvo que terá maior probabilidade de consumir. Assim, evita-se o envio de spam a uma base de utilizadores completa e garante-se uma melhor retenção.

#### <a name="m-commerce"></a>Comércio móvel
Recolha as categorias de produtos mais visitadas dentro da aplicação e segmente público-alvo para promover um desconto ou um novo produto da categoria que será mais provavelmente comprada pelo utilizador final. Procura aumentar as receitas. Mais uma vez, o objetivo é não enviar spam!

#### <a name="gaming"></a>Jogos
Recolha o nível de jogo para um utilizador final e o tempo despendido num determinado período para segmentar o público-alvo que poderia ficar parado e teria maior probabilidade de passar a um nível seguinte com uma oferta de bónus.

Comunique eventos específicos com um incentivo aos utilizadores que já não joguem há algum tempo para tentar encorajá-los a regressar.

#### <a name="retail"></a>Retalho
Recolha os produtos ou marcas com maior probabilidade de serem consumidos por um público-alvo com base nos favoritos ou no comportamento, e atraia o público-alvo para a sua loja de modo a aumentar as receitas de compras.

#### <a name="banking"></a>Banca
Recolha dados de utilizadores finais que tenham criado uma conta na primeira execução da aplicação. Procure implementar uma estratégia de boas-vindas com uma notificação push segmentada e aumentar o número de subscrições de conta.

### <a name="how-to-create-a-great-tag-plan"></a>Como criar um plano de etiquetas excelente?
Um plano de etiquetas tem de ser como uma descrição do caminho de utilizador ou um tipo de fluxo de trabalho da aplicação, que forneça todas as etiquetas (dados) necessárias que têm de ser recolhidas para ter análises suficientes para compreender o comportamento dos utilizadores e segmentar adequadamente a base de utilizadores. Este não é um processo técnico. Por conseguinte, os comerciantes são capazes de especificar os dados que pretendem recolher com base na respetiva estratégia de Mobile Engagement.

O mínimo é etiquetar todos os ecrãs (denominados *atividades* no Mobile Engagement) de uma aplicação. Isto ajuda a determinar o caminho do utilizador.

Uma atividade pode incorporar *eventos* que recolhem informações de ações como clicar num botão. Isto possibilita a recolha de interações dentro da aplicação. Por conseguinte, os comerciantes são capazes de saber o que os utilizadores de ecrã estão a visitar e o que estão a fazer.

`Jobs`são ações com uma duração. Isto é muito útil para os comerciantes compreenderem quanto tempo um utilizador demora a criar uma conta ou a iniciar sessão, por exemplo. Isto também pode ser útil para os programadores monitorizarem quanto tempo demora para ligar a um serviço Web.

`Errors`também podem ser monitorizados para saber se os utilizadores estão a ter problemas na aplicação. Por exemplo, problemas de ligação frequentes.

Todos estes tipos de dados podem ser aumentados com parâmetros (*informações adicionais* no Mobile Engagement), o que lhe permite recolher dados dinâmicos a partir da aplicação. Isto é importante para permitir uma segmentação detalhada. Por exemplo, os comerciantes podem segmentar um utilizador com base no tipo de conteúdo que tenha consumido. O tipo de conteúdo será as informações dinâmicas de uma atividade ou um evento.

*Informações da aplicações* são dados que lhe permitem confirmar o estado da aplicação ou do utilizador em tempo real. Isto também ajuda a categorizar uma base de público-alvo e a segmentá-la rapidamente. Por exemplo, pode utilizar um estado verdadeiro/falso se o utilizador está a iniciar sessão ou não, ou a data de expiração da respetiva subscrição.

#### <a name="example-of-tags"></a>Exemplo de etiquetas
*Caso de utilização: segmente o comportamento do público-alvo para visar o utilizador final adequada com o conteúdo de notificações push adequado*

1. Enviar notificações push para promover uma categoria de produto: recolha dados de comportamento para segmentar público-alvo com base na categoria do produto que visitou x vezes num determinado período ou um item específico que tenha adicionado num carrinho. Os dados recolhidos vão permitir-lhe segmentar e, em seguida, enviar uma notificação push ao público-alvo adequado.
2. Classificar a aplicação: recolha dados com base no conteúdo partilhado pelo público-alvo numa rede social. Procura segmentar o público-alvo ao determinar os *embaixadores* da sua aplicação. Ao utilizar uma notificação push na aplicação, os embaixadores são o melhor público-alvo da sua aplicação a quem pode pedir para classificar a aplicação com 5 estrelas na loja.
   
   ![][1]

*Casos de utilização: dados declarativos*

1. Segmentar notícias de alerta: recolha dados declarativos para segmentar público-alvo com base nas respetivas preferências. Permite o envio de notificações push de um tópico específico que realmente interessa a um público-alvo específico.
2. Segmente o público-alvo com base no estado de início de sessão. Recolha dados para saber se um utilizador está ligado ou se criou uma conta. Ajuda a segmentar os utilizadores finais que ainda não iniciaram sessão e envia uma notificação push para incentivar o utilizador final a converter.
   ![][2]

### <a name="next-steps"></a>Passos seguintes
* Visite [Conceitos do Mobile Engagement] para saber mais sobre os conceitos básicos do Mobile Engagement.
* Visite [Criar uma Aplicação de Mobile Engagement](mobile-engagement-create.md) para criar uma nova Coleção de Aplicações de Mobile Engagement no Azure e começar a gerir as suas aplicações com o portal de Mobile Engagement.
* Visite [Melhores práticas](mobile-engagement-getting-started-best-practices.md) para entrar em detalhes.
* Visite [Cenário de Aplicação de Jogos](mobile-engagement-gaming-scenario.md) para saber mais sobre a implementação do Mobile Engagement com uma aplicação de jogos de exemplo. 
* Visite [Cenário de Aplicação de Comunicação Social](mobile-engagement-media-scenario.md) para saber mais sobre a implementação do Mobile Engagement com uma aplicação de comunicação social de exemplo. 
* Visite [Tutoriais] para saber mais sobre a implementação.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Conceitos do Mobile Engagement]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Tutoriais]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/


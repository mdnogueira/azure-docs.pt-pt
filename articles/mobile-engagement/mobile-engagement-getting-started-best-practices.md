---
title: "Guia de Introdução do Azure Mobile Engagement com Melhores Práticas"
description: "Guia de Introdução para o Azure Mobile Engagement e Melhores práticas para a integração"
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: dfce1183-6398-466e-aa7e-ed702fb52818
ms.service: mobile-engagement
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 0cb54f6c4b84597fbc21fa691f88f6f7a37beedd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure Mobile Engagement – Guia de Introdução com Melhores Práticas
## <a name="overview"></a>Descrição geral
**O ecrã de um dispositivo móvel é um espaço muito movimentado:** em 2013, um estudo concluiu que um dispositivo móvel tinha em média 27 aplicações instaladas. Em geral, os utilizadores gastaram 30 horas por mês nas suas aplicações. A maior parte deste período de tempo foi despendida nas redes sociais e em jogos (cerca de 20 horas). Em 2014, o mercado Android tinha cerca de 1,5 milhões de aplicações ao dispor dos utilizadores. A Apple Store continha aproximadamente 1,2 milhões de aplicações. A utilização de aplicações móveis continua a aumentar, à medida que os programadores competem neste mercado em crescimento. 

O utilizador médio de dispositivos móveis irá instalar e desinstalar aplicações frequentemente, consoante as mudanças de interesses e as experiências na aplicação. Para determinar o êxito de uma aplicação, é essencial saber mais do que apenas o número de utilizadores que instalam a aplicação. É importante saber quão útil é a sua aplicação e se a tendência de utilização está em mudança. As seguintes perguntas tornam-se importantes:

* Os seus utilizadores estão a começar a achar a sua aplicação desinteressante ou obsoleta? 
* Quantos utilizadores deixaram de utilizar a aplicação totalmente? 
* A tendência das compras na aplicação está em ascendência ou em decadência?
* Os utilizadores não estão a conseguir cumprir os fluxos de trabalho devido a problemas com a aplicação ou à falta de interesse? 
* Conseguiria manter a sua aplicação útil e relevante ao enviar conteúdos novos para a sua base de utilizadores? 
* Este conteúdo novo seria o mesmo para todos os utilizadores ou seria focado em segmentos de utilizadores com base no comportamento na sua aplicação? 

As respostas a perguntas como estas podem ajudar a aumentar a vida e as receitas provenientes da sua aplicação. Também podem ajudar a definir e a reter a sua base de utilizadores. 

As aplicações relacionadas com os meios de comunicação social tendem a ter das taxas de retenção mais elevadas entre os utilizadores. Um dos motivos para tal é que proporcionam constantemente conteúdos novos aos utilizadores. A adoção precoce de notificações push úteis direcionadas para um segmento de utilizadores tende a ter um impacto elevado na retenção da aplicação. 

O programa Azure Mobile Engagement foi concebido para o ajudar a aumentar a vida e a retenção da sua aplicação, ao proporcionar um método para recolher e analisar informações detalhadas sobre a utilização da sua aplicação. Irá ajudá-lo a classificar a sua base de utilizadores de acordo com o comportamento e a criar campanhas direcionadas na entrega de notificações push e de mensagens na aplicação a segmentos de utilizadores identificados. Os indicadores chave de desempenho (KPI) medem quão ativos são os seus utilizadores com os diferentes aspetos da sua aplicação. O Azure Mobile Engagement fornece os métodos de que necessita para determinar estes KPIs. Ajuda a aumentar a retorno do investimento (ROI) ao facultar a infraestrutura de que necessita para aumentar o envolvimento com a sua aplicação móvel. 

Para tirar o máximo partido do Azure Mobile Engagement, terá de começar com um plano de envolvimento bem estruturado. O plano irá ajudá-lo a identificar os dados detalhados de que irá necessitar para poder segmentar a sua base de utilizadores. Isto pode ser baseado no comportamento e nas experiências na aplicação. Para que o plano para ser concluído com êxito, existe a melhor prática de definir claramente o KPI que irá medir os objetivos da sua aplicação. Com indicadores de desempenho claramente definidos, pode incorporar facilmente a lógica necessária na sua aplicação para recolher dados ajustados em detalhe que irá utilizar para analisar e avaliar os KPIs. Este tópico é um guia de melhores práticas para definir os KPIs que irá utilizar com o seu plano de envolvimento. 

## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Passo 1: definir os KPIs para se ajustarem ao modelo BET
Definir corretamente KPIs pode ser uma tarefa difícil de realizar. As aplicações concebidas para as diferentes indústrias têm as suas próprias informações específicas e objetivos. Isto poderá confundir sua abordagem. Para ajudar a evitar esta situação, devem ser classificados em três categorias principais os objetivos e os KPIs: **Empresas**, **Envolvimento** e **Técnicos** (Business, Engagement e Technical). Este é o que chamamos **modelo BET**.

Um bom plano geralmente terá objetivos com os KPIs que medem os sucessos em cada uma das seguintes categorias do modelo BET.

#### <a name="business-kpis"></a>KPIs de Empresas
Os KPIs de Empresas devem ser a parte mais fácil de criar. Provavelmente já os definiu de alguma forma quando planeou a sua aplicação móvel. Geralmente, estes KPIs ajudam a medir as receitas e o ROI da sua aplicação. A lista seguinte fornece alguns exemplos de KPIs de Empresas que podem ajudar a orientá-lo na definição dos seus indicadores de desempenho:

* KPIs de Empresas de Comunicação Social
  * Número de Anúncios clicados
  * Número de visitas de página por utilizador
  * Número de subscrições atuais
* KPIs de Empresas de Jogos
  * Número de compras na aplicação
  * Receita média por utilizador (ARPU)
  * Tempo despendido por sessão
  * Dias jogados e nível atual no jogo
* KPIs de Empresas de Comércio Eletrónico
  * Dias de utilização da aplicação
  * Receita média por utilizador (ARPU)
  * Montante médio no carrinho durante a finalização da compra
  * Categoria de produto para a maioria das vistas e compras
* KPIs de Empresas da Banca e de Seguros
  * Número de contas
  * Funcionalidades ativadas
  * Páginas de oferta visitadas
  * Alertas clicados ou ativados       

#### <a name="engagement-kpis"></a>KPIs de Envolvimento
Um KPI de Envolvimento é um indicador de desempenho que mede o envolvimento dos utilizadores. As tendências nesta área ajudam a determinar a retenção da sua aplicação. Seguem-se alguns indicadores de desempenho de exemplo para este tipo de KPI:

* Utilizadores ativos nos últimos 7 dias
* Contagem de utilizadores inativos nos últimos 7 dias
* Contagem de utilizadores que não utilizam a aplicação há 30 dias  

Alguns fatores externos óbvios podem influenciar os indicadores nesta área. Por exemplo, pode considerar que um dispositivo móvel está com um utilizador em qualquer altura. Isto pode ou não ser verdade. Uma aplicação de jogos poderá ter tendência para uma utilização superior nos períodos de férias, quando um jogador pode jogar mais quando não está no trabalho ou na escola.   

Os KPIs bem definidos nesta categoria devem ajudá-lo a medir a relação entre a sua aplicação e os seus clientes.

#### <a name="technical-kpis"></a>KPIs Técnicos
Os indicadores de desempenho nesta categoria ajudam a determinar se a sua aplicação está a ter um comportamento correto, se está a bloquear ou a falhar. Estes indicadores podem medir o estado de funcionamento da sua aplicação e determinar os problemas de utilização que podem impedir os utilizadores de utilizar a aplicação. As informações recolhidas para esta categoria também podem conter informações de desempenho que seriam relevantes para as equipas de marketing. Os dados também podem ser úteis para resolução de problemas por equipas de TI e de apoio técnico para ajudar a identificar erros não relatados. 

Seguem-se alguns exemplos de KPIs Técnicos:

* Informações e contagens de exceções não processadas ou processadas 
* Carimbo de data/hora da última falha
* Último botão clicado ou última página visitada
* Utilização da memória da aplicação
* Velocidade de fotogramas da aplicação
* Versão do SO em que a aplicação está a ser executada
* Versão da aplicação

Defina estes KPIs para ajudar a medir o desempenho da aplicação medidas e identificar potenciais erros. Estes indicadores devem ajudar a reduzir o tempo de que necessita para proporcionar uma correção aos seus clientes. Também o podem ajudar a definir um segmento de utilizadores que tenha encontrado um determinado problema. Pode utilizar essa segmentação de utilizadores para criar campanhas para proporcionar notificações relativas a correções disponíveis e potenciais promoções para o ajudar a recuperar a satisfação do cliente. 

#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Exercício 1 do Manual: Criar o dashboard de KPI
Quando definir a sua estratégia de marketing, os KPIs devem apresentar uma perspetiva para cada um dos seus objetivos principais. Devem ser pontos de dados claramente definidos que lhe permitirão recolher informações essenciais para monitorizar a sua aplicação e o comportamento do utilizador final.

Criar um dashboard de KPI que contém as informações indicadas abaixo

1. Quais são os KPIs para a aplicação?
2. Que pontos de dados irei utilizar para representar cada KPI?
3. Onde estão localizados estes dados para a minha aplicação (ou seja, ecrã, definições, sistema...)?
4. Posso reproduzir uma sequência de Envolvimento para este KPI?

Pode utilizar a folha de cálculo **Criador de KPI** no nosso [Modelo do Manual de Comunicação Social][Media Playbook link] para obter exemplos e orientação.

## <a name="step-2-your-engagement-program"></a>Passo 2: o Seu Programa de Envolvimento
Um programa Mobile Engagement excelente deve ser considerado um componente essencial da sua aplicação. Isto deve sem dúvida incluir um programa de boas-vindas excelente que seja executado para um utilizador durante os primeiros dias de utilização da aplicação. Isto tende a ter um efeito muito positivo no envolvimento e na retenção da sua aplicação. Existem estudos que demonstraram que a maioria dos utilizadores deixa de utilizar uma aplicação nos primeiros dias após a instalação. Deve esforçar-se para atingir ou exceder as expetativas dos clientes ao despertar o interesse numa fase inicial enquanto o utilizador ainda está concentrado na sua aplicação. Certifique-se de que apresenta o valor e as vantagens principais da sua aplicação aos seus clientes. 

![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

As notificações push são a melhor abordagem para envolvimentos numa fase inicial com os utilizadores de dispositivos móveis. No entanto, deve ter muito cuidado quando segmentar os utilizadores para notificações push. Isto acontece porque se um utilizador sentir que está a receber spam ou notificações desinteressantes, pode ter um efeito grave. Com poucos cliques, um utilizador pode eliminar a sua aplicação e nunca regressar. O utilizador deve receber conteúdo de valor altamente personalizado na aplicação em vez de spam genérico.

Assim que os utilizadores estejam ativamente envolvidos, o seu programa de envolvimento pode ajudar a potenciar outros aspetos da aplicação.

Por exemplo, pode configurar uma campanha que peça aos utilizadores ativos para classificarem a aplicação. Uma vez que este segmento de utilizadores é o mais ativo e é o que tem mais experiência com a aplicação, é expectável que lhe atribuam a classificação mais precisa. Ao ter uma classificação elevada da aplicação, tal pode ajudar a impulsionar as transferências orgânicas da sua aplicação, bem como a reduzir os custos de aquisição dos novos clientes.

#### <a name="engagement-sequence"></a>Sequência de Envolvimento
Um Programa de Envolvimento global inclui diversas sequências de envolvimento. Cada sequência visa alcançar vários objetivos.

###### <a name="life-push-sequence"></a>Sequência de push de vida
Os objetivos de uma sequência de push de vida são diferentes, consoante o ciclo de vida do envolvimento do utilizador com a aplicação. Um utilizador específico pode ser novo, inativo ou muito ativo. Em diferentes fases do ciclo de vida de um compromisso, os utilizadores podem tirar partido do seu conteúdo novo sob a forma de sugestões ou ligações para documentação. 

Por exemplo, um novo utilizador poderá necessitar de ajuda e receber orientação numa aplicação ou tirar partido de um incentivo de novo utilizador semelhante ao que se segue na primeira vez que executar a aplicação...

*“Estamos satisfeitos por estar connosco! Não se esqueça de iniciar sessão para obter o 1.º mês gratuito!”*

###### <a name="behavioral-push-sequence"></a>Sequência push comportamental
A sequência push comportamental visa aumentar a utilização com base no comportamento do utilizador recolhido para a aplicação.  

Por exemplo, um utilizador muito ativo de uma aplicação de gestão de futebol americano pode beneficiar do facto de estar envolvido com a seguinte notificação push...

*“Jorge, é um verdadeiro adepto de futebol americano! Inicie sessão na nossa secção da NFL e tenha acesso gratuito ao SuperBowl!”*

###### <a name="alerting-push-sequence"></a>Sequência de push de alerta
Os utilizadores vão gostar de notícias relevantes focadas nos seus interesses. Uma sequência push de alerta melhora o envolvimento ao enviar alertas com base nos interesses que um utilizador demonstrou claramente. Isto pode ser explícito quando um utilizador seleciona os seus próprios interesses na aplicação. Também poderia ser possível determinar implicitamente com base nos dados recolhidos durante a interação do utilizador com a aplicação.

Por exemplo, o utilizador de uma aplicação de comércio eletrónico pode comprar regularmente uma marca específica de café que captou com um KPI de empresas. O alerta seguinte pode melhorar o envolvimento deste utilizador com a aplicação.

*“Olá Vitor, uma das suas marcas favoritas de café estará à venda com 25% de desconto na primeira semana de setembro de 2015. Valorizamo-lo como cliente e queríamos ter a certeza de que tinha conhecimento.”*

###### <a name="rentention-push-sequence"></a>Sequência push de retenção
Esta sequência visa reter utilizadores ao utilizar campanhas de notificações push repetitivas para ajudar a potenciar um hábito regular de envolvimento com a aplicação. Isto pode ajudar a aumentar a retenção da aplicação se o utilizador gostar das interações. 

Por exemplo, o utilizador de uma aplicação relacionada com o desporto pode receber a seguinte notificação push semanal com base nas respetivas equipas favoritas:

*“Para ter a oportunidade de ganhar 200 pontos, vote se os New York Yankees vencem o jogo desta semana contra os Toronto Blue Jays!”*

#### <a name="the-3w-approach"></a>A abordagem dos 3Q
O domínio das diferentes sequências de push irá ajudá-lo a envolver-se com os utilizadores finais. No entanto, ainda precisa de utilizar a abordagem dos 3Q para personalizar as notificações. A abordagem dos 3Q deverá ter em consideração Quem, o Quê e Quando em cada notificação. Se satisfizer claramente estas três perguntas, as suas notificações devem estar adequadamente direcionadas para o envolvimento.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)

###### <a name="who-the-user-segment-that-will-receive-messages"></a>Quem: o segmento de utilizadores que irá receber as mensagens
Enviar notificações push aos seus utilizadores deve ser considerado um canal de comunicação muito sensível. Certifique-se de que as notificações que pretende enviar para um segmento de utilizadores estão bem inseridas no âmbito de interesses desse segmento de utilizadores. Uma notificação incorretamente encaminhada tem grandes probabilidades de ter um efeito negativo num utilizador. O utilizador pode considerá-la spam, o que leva à desinstalação da aplicação. 

Utilize uma combinação de critérios específicos técnicos e comportamentais quando definir os segmentos de utilizadores que irão receber notificações. Um exemplo simples que define um segmento de utilizador pode ser semelhante à instrução seguinte:

“Todos os utilizadores que executaram a aplicação móvel pela primeira vez há 3 dias e visitaram a página de início de sessão duas vezes sem de facto concluir um início de sessão.”

Essa instrução ajuda a identificar os dados que necessita de recolher para suportar um cenário específico.

###### <a name="what-the-message-that-you-will-send"></a>O Quê: a mensagem que vai enviar
**Tom**

Nas ações de envolvimento utilize um tom que se adeque aos seus utilizadores segmentados. Esta é sem dúvida uma boa forma de se ligar aos seus utilizadores finais e promover o interesse de um utilizador na sua aplicação. 

**Redirecionamento**

Uma notificação push pode ser utilizada para mais do que abrir a aplicação. Se a mensagem de notificação incluir um contexto, tal como notícias de difusão ou uma promoção de um produto, esta notificação poderá estabelecer uma ligação avançada diretamente para o conteúdo adequado na aplicação. Para suportar isto, tem de criar um esquema de URL para permitir que a aplicação controle o redirecionamento. Quando estiver a trabalhar nas suas sequências de envolvimento, este é um passo importante que não pode ser esquecido.

O redirecionamento também pode ser gerido noutros sistemas. Por exemplo, com um URL de Ação é possível redirecionar os utilizadores finais para muitos outros sistemas, incluindo os seguintes:

* Um site
* Uma caixa de correio com e-mail já configurado
* Uma caixa de SMS
* Um serviço de marcação
* Diretamente para a loja de aplicações para classificar a aplicação. 

Isto proporciona muitas oportunidades para iniciar os utilizadores finais e criar regras automáticas para melhorar os desempenhos.

**Formato/Conteúdo**

Diferentes tipos e formatos de Notificação push:

1. **Anúncios** : permite-lhe enviar mensagens de publicidade aos utilizadores em diferentes momentos (fora da aplicação, na aplicação ou em qualquer altura).
2. **Inquéritos** : permitem-lhe recolher informações dos utilizadores finais ao colocar-lhes perguntas. As respostas ficam então disponíveis quando criar critérios para segmentar utilizadores finais.
3. **Pushes de Dados** : permite-lhe enviar um ficheiro de dados binários ou base64 para atualizar a aplicação. As informações contidas num push de dados são enviadas para a sua aplicação para personalizar a experiência dos utilizadores na sua aplicação. A aplicação tem de ser concebida para suportar os dados num push de dados.
4. **Mosaicos (apenas no Windows Phone)**: permite-lhe utilizar o Serviço de Notificações Push da Microsoft (MPNS) para enviar Notificações Push Nativas que contêm Dados XML (suportados a partir da versão 0.9.0. do SDK O payload final para mosaicos não pode exceder os 32 kilobytes.). A mensagem aparece diretamente no mosaico do seu painel.
5. **Vista Web** : uma vista Web é um pop-up que contém conteúdo Web. Este pop-up aparece quando o utilizador final tiver clicado na notificação push. Uma vista Web permite-lhe ter mais interação com o utilizador final.

> [!NOTE]
> Certifique-se de que o conteúdo que está a enviar como notificações push está em conformidade com as diretrizes da respetiva plataforma (iOS, Android, Windows) no que diz respeito à programação de aplicações e ao envio de notificações push.
> 
> 

###### <a name="when-the-timing-of-your-campaign"></a>Quando: o período de tempo da sua campanha
Quando é o melhor momento para ativar uma campanha ao acionar notificações push? Deverá ser manual ou automática? Deverá ser recorrente? Determinar o momento ou a frequência ideal é essencial para envolver os utilizadores com os melhores resultados. Para cada sequência e cenário de envolvimento, tem de especificar quando será o melhor momento para enviar notificações push. Seguem-se alguns exemplos possíveis:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Se vai enviar muitas notificações diariamente, tem de ter em consideração que os utilizadores podem interpretar as suas comunicações como spam. 

O Azure Mobile Engagement proporciona duas formas de ajudar a evitar que as suas comunicações sejam interpretadas como spam. Em primeiro lugar, utilize uma segmentação detalhada para garantir que não segmenta os mesmos utilizadores. Além disso, o Azure Mobile Engagement oferece uma funcionalidade de “quota”. Esta funcionalidade pode limitar as notificações enviadas para uma campanha. Por exemplo, definir uma quota predefinida para 5 por semana irá garantir que um utilizador incluído como parte do segmento de utilizadores da campanha não recebe mais de 5 notificações nessa semana.

#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Exercício 2 do Manual: criar o seu programa de envolvimento
Passe algum tempo a resumir os seus objetivos e a definir as campanhas que espera realizar ao utilizar sequências específicas. Certifique-se de que aplica a abordagem dos 3Q às notificações nas suas campanhas. 

Utilize a folha de cálculo **Programa de Envolvimento** no [Modelo do Manual de Comunicação Social][Media Playbook link] para obter exemplos e orientação.

## <a name="step-3-app-integration"></a>Passo 3: integração de aplicações
#### <a name="create-a-tag-plan"></a>Criar um plano de etiquetas
Para integrar o Azure Mobile Engagement na sua aplicação, terá de criar um plano de etiquetas. O plano de etiquetas é a pedra angular do projeto. Define a relação entre as especificações de marketing, o fluxo de trabalho da aplicação e os dados de etiquetas reais recolhidos na aplicação para medir os KPIs. Indica que análises poderá ver no portal. Também o ajuda a definir segmentos de utilizadores e a enviar notificações push direcionadas para o envolvimento dos utilizadores finais. Assim que tiver o plano de etiquetas definido, é simples adicionar o código para integrá-lo na aplicação através do SDK do Azure Mobile Engagement.

Um plano de etiquetas não deve etiquetar tudo numa aplicação. Deve incluir apenas dados de etiquetas que façam parte da sua estratégia Mobile Engagement. Provavelmente, isto será diferente consoante as aplicações. O [Modelo do Manual de Comunicação Social][Media Playbook link] fornecido pelo Azure Mobile Engagement ajuda-o a criar um plano de etiquetas com um determinado método. Utilize a folha de cálculo **Plano de Etiquetas** como um guia para a criação do seu plano de etiquetas.

Quando definir uma secção de etiquetas na folha de cálculo, seja muito específico. Isto é muito importante para evitar confusões. Detalhe cada cenário esperado no qual cada etiqueta será enviada. Inclua o nome da atividade em que cada etiqueta é incorporada. Tudo isto deve constar da parte **Informativo** da folha de cálculo. A folha de cálculo do plano de etiquetas deve ser a referência principal para a verificação de teste. 

Na secção **Dados a recolher**, a sua equipa de programação deve encontrar os tipos, nomes, valores e pares chave/valor de informações adicionais necessários para cada etiqueta que será incorporada na aplicação.

Recomendamos que reveja o Plano de etiquetas com todas as equipas associadas ao projeto. Efetue as correções necessárias e certifique-se de que tudo está preparado para as equipas de marketing e de programação.

A folha de cálculo **Descrição do trabalho** pode ser utilizada para ajudar a orientar todas as pessoas envolvidas no projeto.

#### <a name="data-types"></a>Tipos de Dados
Estes são tipos comuns de suporte de dados pelo Azure Mobile Engagement.

###### <a name="devices-and-users"></a>Dispositivos e utilizadores
O Azure Mobile Engagement identifica os utilizadores através da geração de um identificador exclusivo para cada dispositivo. Este identificador é designado identificador do dispositivo (ou deviceid). Este é gerado de tal forma que todas as aplicações em execução nesse mesmo dispositivo partilham o mesmo identificador de dispositivo.

###### <a name="sessions-and-activities"></a>Sessões e atividades
Uma sessão é uma instância da aplicação que está a ser executada por um utilizador. A sessão abrange o momento em que o utilizador inicia a aplicação até ao momento em que para.

Uma atividade é um agrupamento lógico de um conjunto de ações que a aplicação pode fazer durante uma sessão. Normalmente, é um ecrã específico na aplicação, mas pode ser qualquer coisa definida pela lógica da aplicação. No mínimo, deve etiquetar cada ecrã ou Atividade para a sua aplicação. Isto permitir-lhe-á compreender o caminho do utilizador.

###### <a name="events"></a>Eventos
Os eventos são utilizados para comunicar a interação do utilizador com a aplicação. Podem ser ações instantâneas, como a partilha de conteúdo ou a execução de um vídeo. Se etiquetar eventos poderá recolher dados que lhe mostram como os utilizadores interagem com a aplicação. 

###### <a name="jobs"></a>Tarefas
As tarefas são utilizadas para comunicar as ações que têm uma duração. Alguns exemplos incluem:

* Execução de chamadas de API
* Apresentar a hora dos anúncios
* Duração das tarefas em segundo plano 
* Duração do processo de compra
* Visualizar um vídeo

###### <a name="errors"></a>Erros
Os erros são utilizados para comunicar problemas detetados pela aplicação. Por exemplo, ações incorretas do utilizador ou falhas de chamadas de API.

###### <a name="application-information"></a>Informações da aplicação
As informações da aplicação (App-Info) são utilizadas para etiquetar dados relacionados com a experiência de um utilizador com uma aplicação. São geradas pela interação de um utilizador com a aplicação. 

Numa determinada chave de app-info, o Azure Mobile Engagement mantém apenas um registo do valor mais recente (nenhum histórico). As informações da aplicação revelam o estado da sua aplicação ou dos utilizadores finais. Por exemplo, o estado do início de sessão ou o grupo de produtos favorito de um utilizador.

###### <a name="crash-data"></a>Dados de falhas
Os dados de falhas recolhidos automaticamente pelo SDK do Mobile Engagement comunicam as falhas na aplicação não processadas pela aplicação. Por exemplo, uma exceção não processada que ocorra.

###### <a name="extra-data"></a>Dados adicionais
É possível melhorar com parâmetros eventos, erros, atividades e tarefas. Trata-se de informações adicionais que um programador pode fornecer como dados específicos da aplicação. Isto é importante para definir uma segmentação detalhada. 

Por exemplo, o valor de uma etiqueta de “artigo” permitir-lhe-á segmentar utilizadores finais com base em quem visualizou esse artigo específico. No entanto, isso pode não ser suficiente. Poderá ser melhor se essa mesma etiqueta de “artigo” também incluir informações adicionais, tais como “notícias_categoria” dentro de uma atividade. Isto seria útil para determinar dinamicamente as categorias favoritas do utilizador. 

As informações adicionais são comunicadas como um par chave/valor. No exemplo para esta aplicação de meios de comunicação social, as informações adicionais para “notícias_categoria” seriam o valor dessa categoria. Por exemplo, “desporto”, “economia” ou “política”.

#### <a name="tag-and-sdk-integration"></a>Integração de etiquetas e do SDK
Para obter instruções passo-a-passo para integrar o SDK do Azure Mobile Engagement na sua aplicação, siga a documentação de [Integração do SDK do Engagement](mobile-engagement-windows-store-integrate-engagement.md) no site do Azure. Selecione a plataforma de destino a partir das ligações no topo dessa página.

Recomendamos que crie projetos para duas aplicações assentes no Azure Mobile Engagement. Uma para programação e testes e a outra para testes de produção. A sua equipa de TI pode então transitar dos testes para a produção quando os testes de aceitação de utilizadores tiverem sido bem-sucedidos.

#### <a name="user-acceptance-testing-uat"></a>Testes de aceitação de utilizadores (UAT)
Os testes de aceitação de utilizadores (UAT) servem para se certificar de que tudo está a funcionar devidamente. Os fluxos de trabalho podem ser concluídos e reunir todos os dados necessários com base no seu plano de etiquetas:

* As informações de etiquetagem devem estar aplicadas de acordo com os conceitos de AZME documentados
* Todas as informações de que necessita estão recolhidas (incluindo Valor de informações adicionais, Valor de informações da aplicação)
* A nomenclatura coincide de acordo com o seu Plano de Etiquetas
* Não existem etiquetas enviadas em duplicado

Teste rigorosamente todos os tipos de comportamentos de notificações que incorporou na sua aplicação

* Anúncios, Inquéritos, Pushes de dados fora da aplicação e dentro da aplicação
* Vistas de texto/Web
* Atualização do destaque, Categorias

#### <a name="setup"></a>Configurar
Configurar o Azure Mobile Engagement é muito simples. Toda a documentação relacionada com a interface de utilizador está disponível no site do Azure Mobile Engagement, [Como navegar na interface de utilizador](mobile-engagement-user-interface-home.md).

Recomendamos que comece por configurar as funções corretas e as subscrições de funções para os utilizadores do seu projeto. Isto ajuda-o a gerir o acesso adequado à plataforma para todos os utilizadores. As suas funções podem incluir:

* Administradores
* Programadores
* Visualizadores 

Posteriormente:

* Registe o seu deviceID para testar o seu dispositivo.
* Aceda às definições da sua conta e configure o fuso horário para que a hora de entrega de gráficos e de notificações esteja definida no seu fuso horário.
* Aceda às definições da sua aplicação e registe as “Informações de aplicações” de que necessita para segmentar o utilizador final no Alcance.

Para obter mais informações sobre como executar a sua primeira campanha de notificações push, reveja [Como começar a utilizar e gerir pushes para entrar em contacto com os seus utilizadores finais](mobile-engagement-how-tos.md).

## <a name="conclusion"></a>Conclusão
Os Programas de Envolvimento são iterativos e deve melhorar continuamente os seus à medida que experimenta o que funciona melhor com a sua aplicação. 

Inicialmente, enquanto programar experiências com estratégias de envolvimento, não tente criar uma estratégia de envolvimento global completa. Siga uma abordagem de passo-a-passo ao identificar os seus KPIs e como tirar partido dos mesmos. A estratégia de envolvimento será exclusiva para cada aplicação.

Depois de ter programado alguma experiência, pode considerar adicionar o seguinte aos seus programas de envolvimento:

* Controlo: adquire utilizadores e provavelmente define origens de recolha de dados. O Azure Mobile Engagement pode ser ligado a origens de recolha de dados. Permite-lhe monitorizar os desempenhos de cada origem. Estas informações serão interessantes para maximizar o investimento em aquisições. 
* Teste A/B: trata-se de uma parte essencial do programa de Envolvimento. Cada aplicação tem as suas próprias especificações. Com o teste A/B, pode melhorar o seu programa de envolvimento.
* Geolocalização: esta é uma grande oportunidade para as marcas. Graças esta funcionalidade pode entrar em contacto no momento e no local ideais. Antes de começar a utilizar a geolocalização, recomendamos que confirme se recolheu dados suficientes de comportamento do utilizador final.
* Push de dados: o push de dados é um push invisível. O push de dados permite personalizar a sua aplicação com base no comportamento do utilizador final. Por exemplo, se um segmento de utilizadores consulta frequentemente produtos de alta tecnologia, o proprietário da aplicação pode enviar um push de dados que irá personalizar a respetiva home page com conteúdo de alta tecnologia.

## <a name="next-steps"></a>Passos Seguintes
* [Criar uma conta do Azure Mobile Engagement](mobile-engagement-create.md).
* Visite [definir a sua estratégia de Mobile Engagement](mobile-engagement-define-your-mobile-engagement-strategy.md) para saber mais sobre como definir a estratégia de Mobile Engagement.

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

---
title: "Azure Mobile Engagement implementação de aplicação de jogos"
description: "Cenário de aplicação de jogos para implementar o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0ca35a3d634db8eb5c63afacba046a35b8a3e7ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>Implementar o Mobile Engagement com a aplicação de jogos
## <a name="overview"></a>Descrição geral
Um arranque de jogos tem de iniciar uma aplicação de jogos de role-play/estratégia de fishing com base. No jogo foi configurado e em execução durante 6 meses. Este jogo enorme concluído com êxito e tem milhões de transferências e a retenção é muito elevada em comparação com outras aplicações de jogos de arranque. A reunião revisão trimestral, intervenientes concordarem que precisam para aumentar a média de receitas por utilizador (ARPU). Pacotes de no jogo Premium estão disponíveis como ofertas especiais. Estes pacotes de jogos permitem aos utilizadores atualizar o aspeto e o desempenho do respetivo linhas fishing e lures ou tackles no jogo. No entanto, as vendas do pacote são muito baixos. Assim decidirem primeiro analisar a experiência do cliente com uma ferramenta de análise e, em seguida, para desenvolver um compromisso programa para aumentar a utilização de vendas avançadas de segmentação.

Com base no [Azure Mobile Engagement – guia de introdução com melhores práticas](mobile-engagement-getting-started-best-practices.md) criem uma estratégia de envolvimento.

## <a name="objectives-and-kpis"></a>Objetivos e KPIs
Principais intervenientes no jogo cumpram. Todos os concorda com o objetivo principal um - para aumentar as vendas de pacote de premium por 15%. Criarem indicadores de desempenho de chave de negócio (KPIs) para a medição e unidade neste objetivo

* O nível no jogo estes pacotes adquiridos?
* O que é as receitas por utilizador, por sessão, por semana e por mês?
* Quais são os tipos de compra favorito?

Parte 1 de [guia de introdução](mobile-engagement-getting-started-best-practices.md) explica como definir os objetivos e KPIs. 

Com os KPIs de empresas agora definida, o Gestor de produto Mobile cria KPIs de envolvimento para determinar as tendências de utilizador novo e a retenção.

* Monitorizar a retenção e utilizar para os seguintes intervalos: diariamente, a cada 2 dias, semanalmente, mensalmente e todos os 3 meses
* Contagens de utilizadores do Active Directory
* A classificação da aplicação na loja

Com base na recomendações da equipa de TI, foram adicionados os seguintes KPIs técnicos responder às seguintes perguntas:

* O que é o meu caminho de utilizador (visitada, a qual página quanto tempo que o utilizador dedica nela)
* Número de falhas ou erros que encontrou por sessão
* Quais as versões de SO executam os meus utilizadores?
* O que é o tamanho médio do ecrã para os meus utilizadores?
* Que tipo de ligação à internet dispõe os meus utilizadores?

Para cada KPI o Gestor de produto Mobile Especifica os dados que precisa e onde está localizado no respetivo manual de comunicação social.

## <a name="engagement-program-and-integration"></a>Programa de envolvimento e a integração
Antes de criar um programa de envolvimento avançadas, o diretório de projeto de Mobile responsável pelo projeto deve ter uma compreensão profunda sobre como e quando os produtos que são consumidos pelos utilizadores.

Depois de 3 meses, o diretório de projeto de Mobile recolheu dados suficientes para melhorar as vendas de notificação push na aplicação. Ele aprende que:

* A primeira compra geralmente ocorre ao nível do 14. Para 90% nesses casos, a compra destina-se a nova weapons legendary $3.
* No 80% nesses casos, os utilizadores que efetuou uma compra, continuar com o produto e efetuar mais adquire.
* Iniciar utilizadores passem o nível de 20, gaste mais de 10 $/ semana.
* Os utilizadores tendem a comprar pacotes premium nível 16, 24 e 32.

Graças nesta análise o diretório de projeto de Mobile decide criar push específico sequências de notificação ao aumento das vendas da aplicação. Ele cria três sequências de push que ele chama: bem-vindo programa, o programa de vendas e o programa inativa. Para obter mais informações, consulte o [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->

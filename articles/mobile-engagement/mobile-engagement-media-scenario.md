---
title: "Azure Mobile Engagement implementação de aplicação de comunicação social"
description: "Cenário de aplicação do suporte de dados para implementar o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c1591c3e436981e621830916cf0cdc4b7f395d7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="implement-mobile-engagement-with-media-app"></a>Implementar o Engagement móvel com suporte de dados de aplicação
## <a name="overview"></a>Descrição geral
O João é um Gestor de projeto móveis para uma empresa de grande de suporte de dados. Ele executada recentemente uma nova aplicação que tem uma contagem de transferência muito elevada. Ele atingiu os objetivos para transferência no entanto, ainda a devolver em Investment(ROI) por utilizador não cumpre os requisitos. 

O João já identificou a razão pela qual o ROI está demasiado baixo. Os utilizadores deixar de utilizar a aplicação após apenas 2 semanas com frequência e a maior parte dos mesmos nunca voltar atrás. Ele pretende aumentar a retenção da sua aplicação.

Depois de alguns inicial dos testes, ele tem aprendidas quando ele envolva os seus utilizadores com notificações push, tendem continuar a utilizar a aplicação. Também os utilizadores que foram inativos, muitas vezes, irão devolver para a aplicação, dependendo de notificações, envia-os. O João decide investir em algum tipo de programa de envolvimento para a aplicação que utiliza a filtragem avançados com notificações push.

O João tem de leitura recentemente o [Azure Mobile Engagement – guia de introdução com melhores práticas](mobile-engagement-getting-started-best-practices.md) e decidiu implementar as recomendações do guia de.

## <a name="objectives-and-kpis"></a>Objetivos e KPIs
Principais intervenientes para a aplicação de João cumpram. Empresas é gerada a partir de anúncios como utilizadores consumam o suporte de dados. Aumento conteúdo consumido por utilizador, o João aumenta as receitas. Todos os concordarem num objetivo principal: para aumentar as vendas de anúncios em 25%. Criarem indicadores de desempenho de chave de negócio (KPIs) para a medição e unidade neste objetivo

* Número de anúncios clicado por utilizador
* Quantas páginas de artigo visitada (por utilizador / por sessão / por semana / por mês...)
* Quais são as categorias favoritas

Com base na reunião de João com principais intervenientes ele definiu os KPIs de empresas. Ele segue parte 1 do [Azure Mobile Engagement – guia de introdução com melhores práticas](mobile-engagement-getting-started-best-practices.md). 

Em seguida, ele cria os KPIs de envolvimento seguintes para se certificar de que são atingidos os objetivos:

* Monitorizar retenção entre os seguintes intervalos: diárias, semanais, bissemanais e mensais.
* Contagens de utilizadores ativos
* Armazena a classificação da aplicação na aplicação

Com base na recomendações da equipa de TI, foram adicionados os seguintes KPIs técnicos responder às seguintes perguntas:

* O que é o meu caminho de utilizador (visitada, a página de que quantidade de tempo de que os utilizadores passam no mesmo)
* Número de falhas ou erros que encontrou por sessão?
* Quais as versões de SO executam os meus utilizadores?
* O que é o tamanho médio do ecrã para os meus utilizadores?
* Que tipo de ligações à internet dispõe os meus utilizadores?

Para cada KPI, ele classifica os dados necessários e ele regista na localização correta dos seu manual de comunicação social.

## <a name="engagement-program-and-integration"></a>Programa de envolvimento e a integração
Agora que o João concluiu a definir os KPIs, ele começa a fase de estratégia de envolvimento por definir os seus objetivos e 4 programas de envolvimento:![][1]

Em seguida, João fica mais profundo por com detalhes sobre as notificações push para cada programa. Notificação push são definidos por cinco elementos:

1. Objetivo: o que é o objetivo da notificação
2. Como será possível alcançar o objetivo
3. Destino: que irá receber a notificação?
4. Conteúdo: Qual é a utilização de expressões e o formato da notificação (na App/Out de aplicação)
5. Quando: o que é o melhor momento para enviar esta notificação push
   
    ![][2]

Para obter mais informações, consulte o [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

De acordo com a parte 2 do documento técnico João utiliza a secção de destino para definir os dados que ele tem de recolher e escreve o seu plano de etiquetas jointly com a equipa de TI para implementar a solução. Depois de 1 semana de implementação e testes de aceitação de utilizadores, João por fim, pode iniciar os programas.

## <a name="program-results"></a>Resultados de programa
4 meses mais tarde, o João consulta os desempenhos de programas. O programa de boas-vindas e o programa semanal estão a cumprir os objetivos. Diminui o número de utilizadores com apenas uma sessão, estão a ser utilizadas mais funcionalidades da aplicação e o número de ligações por semana tem duplicada.

O **programa inativa** está a ajudar João compreender tendencies de utilizador. Parece que 15% dos utilizadores Inativos voltar para a aplicação. No entanto a maior parte dos mesmos não permanecem ativos mais de 1 mês. O João foresees uma potencial otimização desta sequência com notificações adicionais e expandir as opções de conteúdo.

O **detetar programa** não funciona corretamente. Aumenta cruzada vender mas insuficientes aceder aos seus objetivos. O João identifica que ele não tem dados suficientes para efetuar relevantes de filtragem e propor conteúdo apropriado. Ele para este programa e centra-se no envio de "notificações editorial push" com o Azure Mobile Engagement. As journalists já tem uma solução CMS para enviar notificações push e não pretende alterar.

O João decide utilizar a API de alcance que é uma API de REST de HTTP que lhe permite gerir campanhas de alcance sem ter de utilizar a interface AZME Web. Com esta abordagem João pode recolher os dados que ele precisa e permitir que os escritores continuar a utilizar a solução CMS.

Para garantir que essa funcionalidade funciona corretamente, o João pede-lhe equipa de TI para ser vigilant nos seguintes pontos:

1. **Sistemas operativos** : têm as suas próprias regras para administrate as notificações push, para que o João decide listar todos os casos e verifica se as APIs processam-lo.
   Por exemplo: O sistema de Android push permite visão geral que não é o caso de iOS.
2. **Período de tempo**: João pretende uma API, o que definir o intervalo de tempo e uma extremidade para as campanhas. Ele pretende manter os utilizadores a partir de qualquer notificação acontece bombing.
3. **Categorias**: equipa de Marketing prepara o modelo para cada tipo de alerta. O João pede-lhe a equipa de TI definir categorias dentro da API.

Após alguns testes João é satisfeito. Graças a esta API, journalists podem ainda enviar notificações push com os respetivos CMS e o Azure Mobile Engagement recolhe todos os dados comportamentais para os mesmos

Depois destes 4 primeiro meses, resultados refletem uma confiança de desempenho e fornecem geral boa para João e a placa, ROI por utilizador aumenta por 15% e vendas móveis representam 17.5% do totais vendas, um aumento de 7.5% em apenas quatro meses.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks

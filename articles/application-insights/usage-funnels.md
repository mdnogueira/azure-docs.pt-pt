---
title: "Aplicação Azure Insights Funnels"
description: "Saiba como pode utilizar Funnels para detetar a forma como os clientes são interagir com a sua aplicação."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 5d55207784390a68a3b4d87f7a3d4773491d08b8
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Detetar como os clientes estão a utilizar a aplicação com o Funnels de informações de aplicação

Experiência do cliente de compreender é a importância utmost ao seu negócio. Se a sua aplicação envolve várias fases, terá de saber se a maioria dos clientes estão a evoluir através do processo completo ou se está a terminar o processo, a determinada altura. A evolução através de uma série de passos numa aplicação web é conhecida como "funil". Pode utilizar o Funnels de informações de aplicação para obter informações acerca dos seus utilizadores e taxas de conversão passo a passo do monitor. 

## <a name="get-started-with-the-funnels-blade"></a>Introdução ao painel Funnels
A forma mais fácil para saber mais sobre Funnels é explicar-lhe um exemplo. As ilustrações seguintes demonstram que os proprietários de passos de uma empresa de comércio eletrónico demoraria para saber como os clientes interagem com a sua aplicação web.  

### <a name="create-your-funnel"></a>Criar a sua funil
Antes de criar o funil, terá de decidir a pergunta que pretende responder. Por exemplo, pode querer saber como muitos clientes visualizar o página inicial, clique num anúncio. Neste exemplo, os proprietários da empresa Fabrikam fibra querem saber a percentagem de clientes que efetuam uma compra após adicionar itens à respetiva carrinho de compras durante o último mês.

Eis os passos que efetuar para criar os seus funil.

1. Clique no botão novo, no painel Funnels.
1. Selecione o intervalo de tempo de "Mês" o **intervalo de tempo** pendente. 
1. Selecione o **página de produto** evento o **passo 1** na lista pendente. 
1. Selecione o **adicionar ao carrinho de compras** evento o **passo 2** na lista pendente.
1. Selecione o **clique Compra** evento o **passo 3** na lista pendente.
1. Adicionar um nome ao funil e clique em **guardar**.

A ilustração seguinte demonstra que os dados, o painel Funnels gera. A partir daqui a Fabrikam proprietários podem ver que, durante a última semana, 22.7% dos clientes que adicionou um item para as respetivas carrinho de compras concluir a compra. Pode também ver 1 de % de clientes clicado um anúncio, antes de visitando a página de produto e 20% dos clientes terminada depois de concluir as respetivas compra.


![Painel funnels com dados](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>Passos seguintes
  * [Descrição geral da utilização](app-insights-usage-overview.md)
  * [Os utilizadores, sessões e os eventos](app-insights-usage-segmentation.md)
  * [Retenção](app-insights-usage-retention.md)
  * [Livros](app-insights-usage-workbooks.md)
  * [Adicionar o contexto de utilizador](app-insights-usage-send-user-context.md)
  * [Exportar para o Power BI](app-insights-export-power-bi.md)


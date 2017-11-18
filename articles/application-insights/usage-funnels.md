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
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Detetar como os clientes estão a utilizar a aplicação com o Funnels de informações de aplicação

Experiência do cliente de compreender é a importância utmost ao seu negócio. Se a sua aplicação envolve várias fases, terá de saber se a maioria dos clientes estão a evoluir através do processo completo ou se está a terminar o processo, a determinada altura. A evolução através de uma série de passos numa aplicação web é conhecida como "funil". Pode utilizar o Funnels de informações de aplicação para obter informações acerca dos seus utilizadores e taxas de conversão passo a passo do monitor. 

## <a name="create-your-funnel"></a>Criar a sua funil
Antes de criar o funil, terá de decidir a pergunta que pretende responder. Por exemplo, pode querer saber o número de utilizadores que está a visualizar a página inicial, ver um perfil de cliente e criar um pedido. Neste exemplo, os proprietários da empresa Fabrikam fibra querem saber a percentagem de clientes que criar com êxito um pedido de cliente.

Eis os passos que efetuar para criar os seus funil.

1. Clique no botão novo, a ferramenta de Funnels.
1. Selecione o intervalo de tempo de "Últimos 90 dias" o **intervalo de tempo** pendente. Selecione "O meu funnels" ou "Partilhado funnels"
1. Selecione o **índice** evento o **passo 1** na lista pendente. 
1. Selecione o **cliente** evento o **passo 2** na lista pendente.
1. Selecione o **criar** evento o **passo 3** na lista pendente.
1. Adicionar um nome ao funil e clique em **guardar**.

A ilustração seguinte demonstra que os dados a ferramenta de Funnels gera. A partir daqui a Fabrikam proprietários podem ver que, durante os últimos 90 dias, 54.3% dos clientes que visitou a página inicial de criar um pedido de cliente. Também pode ver que 2.7k dos clientes transmitida para o índice na home page, isto pode indicar um problema de atualização.


![Ferramenta funnels com a dados](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>Funcionalidades de funil
1. Se a aplicação é o objeto de amostragem, verá uma faixa de amostragem. Ao clicar na faixa será aberto um painel de contexto que dá instruções como desativar a amostragem. 
2. Pode exportar o funil para [Power BI](app-insights-export-power-bi.md).
3. Clique num passo para obter informações mais aprofundadas à direita. 
4. Conversão histórico mostra a conversão nos últimos 90 dias. 
5. Compreenda os seus utilizadores melhor acedendo a ferramenta de utilizadores do Funnels. Cada passo irá dar-lhe organizado utilizadores filtros. 

## <a name="next-steps"></a>Passos seguintes
  * [Descrição geral da utilização](app-insights-usage-overview.md)
  * [Os utilizadores, sessões e os eventos](app-insights-usage-segmentation.md)
  * [Retenção](app-insights-usage-retention.md)
  * [Livros](app-insights-usage-workbooks.md)
  * [Adicionar o contexto de utilizador](app-insights-usage-send-user-context.md)
  * [Exportar para o Power BI](app-insights-export-power-bi.md)


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
ms.openlocfilehash: bbb25af888f34737f6a61cf43890ff248c4cc4de
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Detetar como os clientes estão a utilizar a aplicação com o Application Insights Funnels

Compreender a experiência do cliente é a importância utmost ao seu negócio. Se a aplicação envolve várias fases, terá de saber se a maioria dos clientes estão a evoluir através do processo completo ou se está a terminar o processo, a determinada altura. A evolução através de uma série de passos numa aplicação web é conhecida como um *funil*. Pode utilizar o Azure Application Insights Funnels para obter informações sobre os seus utilizadores e monitorizar as taxas de conversão passo a passo. 

## <a name="create-your-funnel"></a>Criar a sua funil
Antes de criar o funil, opte por utilizar a pergunta que pretende responder. Por exemplo, pode querer saber o número de utilizadores que está a visualizar a página inicial, visualização de um perfil de cliente e criar um pedido. Neste exemplo, os proprietários da empresa Fabrikam fibra querem saber a percentagem de clientes que criar com êxito um pedido de cliente.

Eis os passos que efetuar para criar os seus funil.

1. Na ferramenta Application Insights Funnels, selecione **novo**.
1. Do **intervalo de tempo** menu pendente, selecione **últimos 90 dias**. Selecione **meu funnels** ou **partilhado funnels**.
1. Do **passo 1** na lista pendente, selecione **índice**. 
1. Do **passo 2** lista, selecione **cliente**.
1. Do **passo 3** lista, selecione **criar**.
1. Adicionar um nome ao funil e selecione **guardar**.

A seguinte captura de ecrã mostra que um exemplo de um tipo de dados a ferramenta de Funnels gera. Os proprietários de Fabrikam podem ver que durante os últimos 90 dias, por cento de 54.3 dos clientes que visitou a página inicial de criar um pedido de cliente. Pode também ver que 2,700 dos clientes é fornecido para o índice na home page. Isto pode indicar um problema de atualização.


![Ferramenta de captura de ecrã de Funnels com a dados](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnels-features"></a>Funcionalidades de funnels
A captura de ecrã anterior inclui cinco áreas realçadas. Estes são funcionalidades de Funnels. A lista seguinte explica mais sobre cada área correspondente na captura de ecrã:
1. Se a aplicação é o objeto de amostragem, verá uma faixa de amostragem. Selecionar a faixa é aberto um painel de contexto, explicar como desativar a amostragem. 
2. Pode exportar o funil para [Power BI](app-insights-export-power-bi.md).
3. Selecione um passo para ver mais detalhes no lado direito. 
4. O gráfico de conversão histórico mostra as taxas de conversão nos últimos 90 dias. 
5. Compreenda os seus utilizadores melhor acedendo a ferramenta de utilizadores. Pode utilizar filtros de cada passo. 

## <a name="next-steps"></a>Passos seguintes
  * [Descrição geral da utilização](app-insights-usage-overview.md)
  * [Os utilizadores, sessões e os eventos](app-insights-usage-segmentation.md)
  * [Retenção](app-insights-usage-retention.md)
  * [Livros](app-insights-usage-workbooks.md)
  * [Adicionar o contexto de utilizador](app-insights-usage-send-user-context.md)
  * [Exportar para o Power BI](app-insights-export-power-bi.md)


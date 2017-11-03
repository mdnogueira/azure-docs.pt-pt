---
title: "Análise de eventos, de sessão e de utilizador no Azure Application Insights | Documentos da Microsoft"
description: "Análise demográficas dos utilizadores da sua aplicação web."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: df707f2c54ce6d4fd48490085b182ad16d7cfaed
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Análise de utilizadores, sessões e os eventos no Application Insights

Determinar quando pessoas utilizam a sua aplicação web, as páginas que está a mais interessados, onde estão localizados os seus utilizadores, que sistemas operativos e browsers utilizam. Analisar o negócio e telemetria de utilização através da utilização de [Azure Application Insights](app-insights-overview.md).

## <a name="get-started"></a>Introdução

Se não vir ainda dados a utilizadores, sessões ou painéis de eventos no portal do Application Insights, [aprender a começar a utilizar as ferramentas de utilização](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A ferramenta de segmentação de utilizadores, sessões e os eventos

Três dos painéis de utilização utilizam a mesma ferramenta para dividem e organizam a telemetria da sua aplicação web de três perspetivas. Ao filtrar e dividir os dados, pode descobrir informações sobre a utilização de relativa de páginas diferentes e funcionalidades.

* **Ferramenta de utilizadores**: como muitas pessoas utilizado a aplicação e as respetivas funcionalidades.  Os utilizadores são contados utilizando IDs anónimos armazenados em cookies do browser. Uma única pessoa através de browsers diferentes ou máquinas será contada como mais do que um utilizador.
* **Ferramenta de sessões**: O número de sessões de atividade do utilizador tem incluído determinadas páginas e funcionalidades da sua aplicação. Uma sessão é contabilizada após a meia hora de inatividade do utilizador, ou após contínua 24 horas de utilização.
* **Ferramenta de eventos**: com que frequência são utilizadas determinadas páginas e funcionalidades da sua aplicação. Uma vista de página é contabilizada quando um browser carrega uma página da sua aplicação fornecida tem [instrumentadas-](app-insights-javascript.md). 

    Um evento personalizado representa uma ocorrência de algo a acontecer na aplicação, muitas vezes, uma interação do utilizador com o seguinte clique num botão ou a conclusão de algumas tarefas. Inserir o código na sua aplicação para [gerar eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent).

![Ferramenta de utilização](./media/app-insights-usage-segmentation/users.png)

## <a name="querying-for-certain-users"></a>Consultar determinados utilizadores 

Explore a diferentes grupos de utilizadores ao ajustar as opções de consulta na parte superior da ferramenta de utilizadores: 

* Quem utilizada: escolha eventos personalizados e vistas de página. 
* Durante a: Escolha um intervalo de tempo. 
* Por: Escolha como bucket os dados, por um período de tempo ou por outra propriedade como browser ou uma localidade. 
* Dividir por: Escolha uma propriedade através do qual a divisão ou segmento os dados. 
* Adicionar filtros: Limite a consulta para determinados utilizadores, sessões ou eventos com base nas respetivas propriedades, tais como o browser ou uma localidade. 
 
## <a name="saving-and-sharing-reports"></a>Guardar e partilhar relatórios 
Pode guardar relatórios de utilizadores, privados apenas para si na secção os meus relatórios ou partilhados com todas as pessoas pessoa com acesso a este recurso do Application Insights na secção dos relatórios partilhado.  
 
Ao guardar um relatório ou editar as respetivas propriedades, escolha "Atual intervalo de tempo relativo" para guardar um relatório irá dados continuamente atualizados, retroceder alguma quantidade de tempo fixa.  
 
Escolha "Atual intervalo de tempo absoluto" para guardar um relatório com um conjunto de dados fixo. Tenha em atenção que os dados no Application Insights apenas são armazenados durante 90 dias, pelo que o se passem mais do que 90 dias desde um relatório com um intervalo de tempo absoluto foi guardada, o relatório será apresentada em branco. 
 
## <a name="example-instances"></a>Instâncias de exemplo

A secção de instâncias de exemplo mostra informações sobre alguns a utilizadores individuais, sessões ou eventos que são correspondidos por consulta atual. Considerar e explorar os comportamentos de indivíduos, além de agregados, podem fornecer informações sobre como as pessoas utilizam, na verdade, a sua aplicação. 
 
## <a name="insights"></a>Informações 

A barra lateral Insights mostra grandes clusters de utilizadores que partilham propriedades comuns. Esses clusters podem descobrir tendências surprising na forma como as pessoas utilizam a sua aplicação. Por exemplo, se 40% de utilização da sua aplicação todo provenientes de pessoas através de uma funcionalidade único.  


## <a name="next-steps"></a>Passos seguintes
- Para ativar as experiências de utilização, começar a enviar [eventos personalizados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [vistas de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se já enviar eventos personalizados ou vistas de página, explore as ferramentas de utilização para saber como os utilizadores utilizam o serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](app-insights-usage-retention.md)
    - [Fluxos do Utilizador](app-insights-usage-flows.md)
    - [Livros](app-insights-usage-workbooks.md)
    - [Adicionar o contexto de utilizador](app-insights-usage-send-user-context.md)


---
title: "O que é o Log Analytics? | Microsoft Docs"
description: "O Log Analytics é um serviço do Operations Management Suite (OMS) que o ajuda a recolher e a analisar dados gerados operacionais pelos recursos dos seus ambientes na nuvem e no local.  Este artigo disponibiliza uma descrição geral breve dos diferentes componentes do Log Analytics e ligações para conteúdos detalhados."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: bf1455cf3975b925d114acc3e1d4cba55f2a17a9
ms.openlocfilehash: 3e7e42e2ac2deedf936ffc4d246553f72ee4dcd3


---
# <a name="what-is-log-analytics"></a>O que é o Log Analytics?
O Log Analytics é um serviço do [Operations Management Suite\(OMS\)](../operations-management-suite/operations-management-suite-overview.md) que o ajuda a recolher e a analisar dados gerados pelos recursos dos seus ambientes na nuvem e no local. Oferece informações em tempo real através da pesquisa integrada e dos dashboards personalizáveis para analisar, prontamente, milhões de registos de todas as cargas de trabalho e servidores, independentemente da localização física dos mesmos.

## <a name="log-analytics-components"></a>Componentes do Log Analytics
No centro do Log Analytics, está o repositório do OMS, que está alojado na nuvem do Azure.  Os dados são recolhidos no repositório a partir de origens ligadas mediante a configuração das origens de dados e da adição de soluções à sua subscrição.  As origens de dados e as soluções criarão, cada uma, diferentes tipos de registos que têm os seus próprios conjuntos de propriedades, mas que podem ser analisados em conjunto em consultas feitas no repositório.  Desta forma, pode utilizar as mesmas ferramentas e métodos para trabalhar com diversos tipos de dados recolhidos por diferentes origens.

![Repositório do OMS](media/log-analytics-overview/overview.png)

As origens ligadas são os computadores e outros recursos que geram os dados recolhidos pelo Log Analytics.  Estes recursos podem incluir os agentes instalados nos computadores [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md) que se ligam diretamente ou os agentes num [grupo de gestão ligado do System Center Operations Manager](log-analytics-om-agents.md).  O Log Analytics também pode recolher dados a partir do [armazenamento do Azure](log-analytics-azure-storage.md).

As [origens de dados](log-analytics-data-sources.md) são os diferentes tipos de dados recolhidos a partir de cada origem ligada.  Incluem eventos e [dados de desempenho](log-analytics-data-sources-performance-counters.md) de agentes do [Windows](log-analytics-data-sources-windows-events.md) e do Linux, além de origens como [registos do IIS](log-analytics-data-sources-iis-logs.md) e [registos de texto personalizado](log-analytics-data-sources-custom-logs.md).  Pode configurar cada origem de dados que pretende recolher e a configuração é entregue automaticamente a cada origem ligada.

## <a name="analyzing-log-analytics-data"></a>Analisar os dados do Log Analytics
A maior parte da interação com o Log Analytics é feita no portal do OMS, que pode ser executado em qualquer browser e lhe proporciona acesso a definições de configuração e a várias ferramentas para analisar e atuar sobre os dados reolhidos.  No portal, pode tirar partido das [pesquisas de registos](log-analytics-log-searches.md), nas quais constrói as consultas para analisar os dados recolhidos, dos [dashboards](log-analytics-dashboards.md), que pode personalizar com vistas de gráficas das suas pesquisas mais valiosas, e das [soluções](log-analytics-add-solutions.md), que disponibilizam funcionalidades e ferramentas de análise adicionais.

![Portal do OMS](media/log-analytics-overview/portal.png)

O Log Analytics oferece uma sintaxe de consultas para obter e consolidar rapidamente os dados no repositório.  Pode criar e guardar [Pesquisas de Registos](log-analytics-log-searches.md) para analisar dados diretamente no portal do OMS ou pode executá-las automaticamente para criar um alerta se os resultados das consultas indicarem uma condição importante.

![Pesquisas de registos](media/log-analytics-overview/log-search.png)

Para ter um vislumbre gráfico rápido do estado de funcionamento do seu ambiente geral, pode adicionar visualizações para as pesquisas de registos guardadas ao seu [dashboard](log-analytics-dashboards.md).   

![Dashboard](media/log-analytics-overview/dashboard.png)

Para analisar dados fora do Log Analytics, pode exportá-los do repositório do OMS para outras ferramentas, como [o Power BI](log-analytics-powerbi.md) ou o Excel.  Também pode tirar partido da [API de Pesquisas de Registos](log-analytics-log-search-api.md) para criar soluções personalizadas que se baseiem nos dados do Log Analytics ou para integrar noutros sistemas.

## <a name="solutions"></a>Soluções
As soluções acrescentam funcionalidades ao Log Analytics.  São fundamentalmente executadas na nuvem e proporcionam a análise dos dados recolhidos no repositório do OMS. Podem também definir novos tipos de registos a serem recolhidos e que podem ser analisados com as Pesquisas de Registos ou através de uma interface de utilizador adicional disponibilizada pela solução no dashboard do OMS.  

![Solução de Controlo de Alterações](media/log-analytics-overview/change-tracking.png)

As soluções estão disponíveis para uma variedade de funções e pode procurar facilmente as soluções disponíveis e [adicioná-las à sua área de trabalho do OMS](log-analytics-add-solutions.md), a partir da Galeria de Soluções.  Muitas serão implementadas automaticamente e começarão a funcionar de imediato, ao passo que outras poderão precisar de alguma configuração.

![Galeria de Soluções](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-architecture"></a>Arquitetura do Log Analytics
Os requisitos de implementação do Log Analytics são mínimos, uma vez que os componentes centrais estão alojados na nuvem do Azure.  Estes componentes incluem o repositório, para além dos serviços que lhe permitem correlacionar e analisar os dados recolhidos.  O portal pode ser acedido a partir de qualquer browser, pelo que não é necessário nenhum software cliente.

Tem de instalar agentes nos computadores [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md), mas não são precisos agentes adicionados para os computadores que já sejam membros de um [grupo de gestão ligado do SCOM](log-analytics-om-agents.md).  Os agentes do SCOM continuarão a comunicar com os servidores de gestão, que reencaminharão os respetivos dados para o Log Analytics.  Contudo, algumas soluções vão precisar de agentes que comuniquem diretamente com o Log Analytics.  Os requisitos de comunicação de cada solução estão especificados nas respetivas documentações.

Quando se [inscrever no Log Analytics](log-analytics-get-started.md), vai criar uma área de trabalho do OMS.  Pode encarar a área de trabalho como um ambiente do OMS exclusivo com os respetivos repositórios de dados, origens de dados e soluções. Pode criar várias origens de dados na sua subscrição, de modo a suportar múltiplos ambientes, como produção e teste.

![Arquitetura do Log Analytics](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>Passos seguintes
* [Inscrever-se numa conta do Log Analytics gratuita](log-analytics-get-started.md) para testar no seu ambiente.
* Ver as diferentes [Origens de Dados](log-analytics-data-sources.md) disponíveis para recolher dados para o repositório do OMS.
* [Procurar as soluções disponíveis na Galeria de Soluções](log-analytics-add-solutions.md) para acrescentar funcionalidades ao Log Analytics.




<!--HONumber=Dec16_HO2-->



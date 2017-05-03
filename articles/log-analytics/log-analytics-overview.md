---
title: "O que é o Log Analytics no Operations Management Suite (OMS)? | Microsoft Docs"
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
ms.date: 03/29/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c2e78e0191be6ac7d828402859c04c486e8df594
ms.lasthandoff: 04/20/2017


---
# <a name="what-is-log-analytics"></a>O que é o Log Analytics?
O Log Analytics é um serviço do [Operations Management Suite \(OMS\)](../operations-management-suite/operations-management-suite-overview.md) que monitoriza os seus ambientes na cloud e no local, para manter a disponibilidade e o desempenho dos mesmos.  Recolhe dados gerados por recursos nos seus ambientes na cloud e no local e de outras ferramentas de monitorização, para disponibilizar análises relativas a várias origens.  Este artigo mostra uma breve discussão sobre o valor proporcionado pelo Log Analytics, uma descrição geral de como funciona e contém ligações a conteúdos mais detalhados, para que possa fazer ainda mais.

## <a name="is-log-analytics-for-you"></a>O Log Analytics é adequado para si?
Se ainda não tiver monitorização atual aplicada ao seu ambiente do Azure, deve começar com o [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md), que recolhe e analisa dados de monitorização dos seus recursos do Azure.  O Log Analytics pode recolher dados do Azure Monitor para correlacioná-los com outros dados e proporcionar mais análises.

Se quiser monitorizar o seu ambiente no local ou tiver monitorização existente que utilize serviços como o Azure Monitor ou o System Center Operations Manager, o Log Analytics pode acrescentar valor significativo.  Pode recolher dados diretamente dos seus agentes e também dessas outras ferramentas e reuni-los num único repositório.  As ferramentas de análise do Log Analytics, como as pesquisas de registos, as vistas e as soluções funcionam face a todos os dados recolhidos, dando-lhe análises centralizadas de todo o seu ambiente.


## <a name="using-log-analytics"></a>Utilizar o Log Analytics
Pode aceder ao Log Analytics através do portal do OMS ou do portal do Azure, que são executados em qualquer browser e lhe proporcionam acesso a definições de configuração e a várias ferramentas para analisar e atuar sobre os dados recolhidos.  No portal, pode tirar partido das [pesquisas de registos](log-analytics-log-searches.md), nas quais constrói as consultas para analisar os dados recolhidos, dos [dashboards](log-analytics-dashboards.md), que pode personalizar com vistas de gráficas das suas pesquisas mais valiosas, e das [soluções](log-analytics-add-solutions.md), que disponibilizam funcionalidades e ferramentas de análise adicionais.

A imagem abaixo é do portal do OMS, que mostra o dashboard que apresenta informações de resumo relativas às [soluções](#add-functionality-with-management-solutions) que estão instaladas na área de trabalho.  Pode clicar em qualquer mosaico para analisar mais detalhadamente os dados dessas soluções.

![Portal do OMS](media/log-analytics-overview/portal.png)

O Log Analytics inclui uma linguagem de consulta para obter e consolidar rapidamente dados no repositório.  Pode criar e guardar [Pesquisas de Registos](log-analytics-log-searches.md) para analisar dados diretamente no portal ou pode executá-las automaticamente para criar um alerta se os resultados das consultas indicarem uma condição importante.

![Pesquisas de registos](media/log-analytics-overview/log-search.png)

Para obter um vislumbre gráfico rápido do estado de funcionamento do seu ambiente geral, pode adicionar visualizações para as pesquisas de registos guardadas ao seu [dashboard](log-analytics-dashboards.md).   

![Dashboard](media/log-analytics-overview/dashboard.png)

Para analisar dados fora do Log Analytics, pode exportá-los do repositório do OMS para outras ferramentas, como [o Power BI](log-analytics-powerbi.md) ou o Excel.  Também pode tirar partido da [API de Pesquisas de Registos](log-analytics-log-search-api.md) para criar soluções personalizadas que se baseiem nos dados do Log Analytics ou para integrar noutros sistemas.

## <a name="add-functionality-with-management-solutions"></a>Adicionar funcionalidade com soluções de gestão
As [soluções de gestão](log-analytics-add-solutions.md) acrescentam funcionalidades ao OMS, proporcionando dados e ferramentas de análises adicionais ao Log Analytics.  Podem também definir novos tipos de registos a serem recolhidos e que podem ser analisados com as Pesquisas de Registos ou através de uma interface de utilizador adicional disponibilizada pela solução no dashboard.  A imagem de exemplo abaixo mostra a [solução Controlo de Alterações](log-analytics-change-tracking.md)

![Solução de Controlo de Alterações](media/log-analytics-overview/change-tracking.png)

Estão disponíveis soluções para diversas funções e são constantemente adicionadas novas.  Pode procurar facilmente as soluções disponíveis e [adicioná-las à sua área de trabalho do OMS](log-analytics-add-solutions.md), a partir da Galeria de Soluções ou do Azure Marketplace.  Muitas serão implementadas automaticamente e começarão a funcionar de imediato, ao passo que outras poderão precisar de alguma configuração.

![Galeria de Soluções](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Componentes do Log Analytics
No centro do Log Analytics, está o repositório do OMS, que está alojado na nuvem do Azure.  Os dados são recolhidos no repositório a partir de origens ligadas mediante a configuração das origens de dados e da adição de soluções à sua subscrição.  As origens de dados e as soluções criarão, cada uma, diferentes tipos de registos que têm os seus próprios conjuntos de propriedades, mas que podem ser analisados em conjunto em consultas feitas no repositório.  Desta forma, pode utilizar as mesmas ferramentas e métodos para trabalhar com diversos tipos de dados recolhidos por diferentes origens.

![Repositório do OMS](media/log-analytics-overview/overview.png)

As origens ligadas são os computadores e outros recursos que geram os dados recolhidos pelo Log Analytics.  Estes recursos podem incluir os agentes instalados nos computadores [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md) que se ligam diretamente ou os agentes num [grupo de gestão ligado do System Center Operations Manager](log-analytics-om-agents.md).  Relativamente aos recursos do Azure, o Log Analytics recolhe dados do [Azure Monitor e dos Diagnósticos do Azure](log-analytics-azure-storage.md).

As [origens de dados](log-analytics-data-sources.md) são os diferentes tipos de dados recolhidos a partir de cada origem ligada.  Incluem [eventos](log-analytics-data-sources-windows-events.md) e [dados de desempenho](log-analytics-data-sources-performance-counters.md) de agentes do [Windows](log-analytics-data-sources-windows-events.md) e do Linux, além de origens como [registos do IIS](log-analytics-data-sources-iis-logs.md) e [registos de texto personalizado](log-analytics-data-sources-custom-logs.md).  Pode configurar cada origem de dados que pretende recolher e a configuração é entregue automaticamente a cada origem ligada.

Se tiver requisitos personalizados, pode utilizar a [API de Recoletor de Dados HTTP](log-analytics-data-collector-api.md) para escrever dados no repositório de um cliente de API REST.

## <a name="log-analytics-architecture"></a>Arquitetura do Log Analytics
Os requisitos de implementação do Log Analytics são mínimos, uma vez que os componentes centrais estão alojados na nuvem do Azure.  Estes componentes incluem o repositório, para além dos serviços que lhe permitem correlacionar e analisar os dados recolhidos.  O portal pode ser acedido a partir de qualquer browser, pelo que não é necessário nenhum software cliente.

Tem de instalar agentes nos computadores [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md), mas não são precisos agentes adicionados para os computadores que já sejam membros de um [grupo de gestão ligado do SCOM](log-analytics-om-agents.md).  Os agentes do SCOM continuarão a comunicar com os servidores de gestão, que reencaminharão os respetivos dados para o Log Analytics.  Contudo, algumas soluções vão precisar de agentes que comuniquem diretamente com o Log Analytics.  Os requisitos de comunicação de cada solução estão especificados nas respetivas documentações.

Quando se [inscrever no Log Analytics](log-analytics-get-started.md), vai criar uma área de trabalho do OMS.  Pode encarar a área de trabalho como um ambiente do Log Analytics exclusivo com os respetivos repositórios de dados, origens de dados e soluções. Pode criar várias origens de dados na sua subscrição, de modo a suportar múltiplos ambientes, como produção e teste.

![Arquitetura do Log Analytics](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>Passos seguintes
* [Inscrever-se numa conta do Log Analytics gratuita](log-analytics-get-started.md) para testar no seu ambiente.
* Ver as diferentes [Origens de Dados](log-analytics-data-sources.md) disponíveis para recolher dados para o repositório do OMS.
* [Procurar as soluções disponíveis na Galeria de Soluções](log-analytics-add-solutions.md) para acrescentar funcionalidades ao Log Analytics.



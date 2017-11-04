---
title: "O mapeamento de aplicações no Azure Application Insights | Microsoft Docs"
description: "Uma apresentação visual de dependências entre os componentes de aplicação, etiquetadas com alertas e KPIs."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: e1eb2177d6032142781e6e31af6c7f6313d38f4d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="application-map-in-application-insights"></a>Mapa de aplicação no Application Insights
No [Azure Application Insights](app-insights-overview.md), o mapeamento de aplicações é um esquema visual das relações de dependência dos componentes da aplicação. Cada componente mostra KPIs, tais como a carga, desempenho, falhas e os alertas, para o ajudar a detetar qualquer componente causar um problema de desempenho ou a falha. Pode clicar sucessivamente de qualquer componente para obter um diagnóstico mais detalhado, tais como eventos do Application Insights. Se a sua aplicação utiliza serviços do Azure, também pode clicar sucessivamente para obter um diagnóstico do Azure, tais como recomendações do Assistente de base de dados do SQL Server.

Como outros gráficos, pode afixar um mapa de aplicação para o dashboard do Azure, onde este fica totalmente funcional. 

## <a name="open-the-application-map"></a>Abra o mapa de aplicação
Abra o mapa do painel de descrição geral para a sua aplicação:

![Abra o mapa de aplicação](./media/app-insights-app-map/01.png)

![mapa de aplicação](./media/app-insights-app-map/02.png)

O mapa mostra:

* Testes de disponibilidade
* Componente do lado do cliente (monitorizada com o SDK de JavaScript)
* Componente do lado do servidor
* Dependências dos componentes de cliente e servidor

Pode expandir e fechar a grupos de ligação de dependência:

![Fechar](./media/app-insights-app-map/03.png)

Se tiver uma grande quantidade de dependências de um tipo (SQL Server, etc. HTTP), que possam aparecer agrupados. 

![dependências agrupadas](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Problemas de lugar para cima
Cada nó tem indicadores de desempenho relevantes, tais como as taxas de carga, desempenho e falha desse componente. 

Ícones de aviso destacar informações sobre problemas possíveis. Um aviso laranja significa que existem falhas nos pedidos, vistas de página ou chamadas de dependência. Vermelho significa uma taxa de falhas superior a 5%. Se pretender ajustar estes limiares, abra as opções.

![ícones de falha](./media/app-insights-app-map/04.png)

Active Directory também alertas Mostrar cópias de segurança: 

![alertas ativos](./media/app-insights-app-map/05.png)

Se utilizar o SQL Azure, há um ícone que mostra quando existem recomendações sobre como melhorar o desempenho. 

![recomendação do Azure](./media/app-insights-app-map/06.png)

Clique em qualquer ícone para obter mais detalhes:

![recomendação do Azure](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Clique em diagnóstico através do
Cada um de nós no mapa oferece clique visado através de diagnóstico. As opções variam dependendo do tipo de nó.

![Opções de servidor](./media/app-insights-app-map/09.png)

Para os componentes que estão alojados no Azure, as opções incluem hiperligações diretas aos mesmos.

## <a name="filters-and-time-range"></a>Filtros e o intervalo de tempo
Por predefinição, o mapa resume os dados disponíveis para o intervalo de tempo escolhido. Mas pode filtrá-la para incluir apenas os nomes de operações específicas ou dependências.

* Nome da operação: inclui os vistas de página e os tipos de pedido do lado do servidor. Com esta opção, o mapa mostra o KPI no nó do lado do servidor/cliente para apenas as operações selecionados. Mostra as dependências chamadas no contexto dessas operações específicas.
* Nome de base de dependência: Isto inclui as dependências de browser de AJAX e dependências do lado do servidor. Se o relatório telemetria de dependência personalizadas com a API de TrackDependency, também aparecem aqui. Pode selecionar as dependências para mostrar no mapa. Atualmente esta seleção não filtrar os pedidos do lado do servidor ou as vistas de página do lado do cliente.

![Conjunto de filtros](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Guardar filtros
Para guardar os filtros que aplicou, afixar a vista filtrada para um [dashboard](app-insights-dashboards.md).

![Afixar ao dashboard](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Painel de erro
Ao clicar num nó no mapa, um painel de erro é apresentada no lado direito resumir falhas para esse nó. Falhas são agrupadas primeiro por ID de operação e, em seguida, agrupadas por ID do problema.

![Painel de erro](./media/app-insights-app-map/error-pane.png)

Clicar numa falha de demora para a instância mais recente do que falha.

## <a name="resource-health"></a>Estado de funcionamento de recursos
Alguns tipos de recurso, o estado de funcionamento do recurso é apresentado na parte superior do painel de erro. Por exemplo, ao clicar num nó do SQL Server irá mostrar o estado de funcionamento da base de dados e todos os alertas que tenham desencadeou.

![Estado de funcionamento de recursos](./media/app-insights-app-map/resource-health.png)

Pode clicar no nome de recurso para ver as métricas de descrição geral de padrão para esse recurso.

## <a name="end-to-end-system-app-maps"></a>Mapas de aplicação de sistema de ponto a ponto

*Requer SDK versão 2.3 ou superior*

Se a aplicação tem de vários componentes - por exemplo, um serviço de back-end além da aplicação web –, em seguida, pode apresentar todos os num mapa de uma aplicação integrada.

![Conjunto de filtros](./media/app-insights-app-map/multi-component-app-map.png)

O mapa de aplicação localiza nós do servidor seguindo quaisquer chamadas de dependência HTTP feitas entre servidores com o Application Insights SDK instalado. Cada recurso do Application Insights é assumido que contém um servidor.

### <a name="multi-role-app-map-preview"></a>Mapa de aplicação de função multi (pré-visualização)

A funcionalidade de mapa de aplicação de função multi pré-visualização permite-lhe utilizar o mapa de aplicação com vários servidores de envio de dados ao mesmo recurso do Application Insights / chave de instrumentação. Servidores no mapa são segmentadas pela propriedade cloud_RoleName em itens de telemetria. Definir *mapa de aplicação de função Multi* para *no* partir do painel de pré-visualizações para ativar esta configuração.

Esta abordagem poderá ser pretendida numa aplicação microserviços ou noutros cenários em que pretenda correlacionar eventos por vários servidores dentro de um único recurso do Application Insights.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Comentários
Forneça comentários através da opção de comentários do portal.

![Imagem de MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Passos seguintes

* [Portal do Azure](https://portal.azure.com)

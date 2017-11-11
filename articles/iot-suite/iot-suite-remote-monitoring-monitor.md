---
title: "Avançadas de monitorização na solução de monitorização remota - Azure | Microsoft Docs"
description: "Este tutorial mostra como monitorizar dispositivos com o dashboard de solução de monitorização remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 3dfc5809344af920540a88e097ce399cce653363
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Efetuar a monitorização avançada, utilizando a solução de monitorização remota

Este tutorial mostra as funcionalidades do dashboard de monitorização remota. Para apresentar estas capacidades, o tutorial utiliza um cenário na aplicação Contoso IoT.

Neste tutorial, utilize dois dispositivos de camião Contoso simulados para saber como monitorizar dos seus dispositivos no dashboard da solução pré-configurada. Como um operador de Contoso, terá de monitorizar a localização e o comportamento dos seus trucks no campo.

Neste tutorial, ficará a saber como:

>[!div class="checklist"]
> * Filtrar os dispositivos no dashboard
> * Ver telemetria em tempo real
> * Ver detalhes do dispositivo
> * Vista alarmes dos seus dispositivos
> * Ver o sistema KPIs

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada da solução de monitorização remota na sua subscrição do Azure.

Se ainda não implementado a solução de monitorização remota ainda, deve efetuar o [implementar a solução pré-configurada de monitorização remota](iot-suite-remote-monitoring-deploy.md) tutorial.

## <a name="choose-the-devices-to-display"></a>Escolha os dispositivos para apresentar

Para selecionar os dispositivos que apresentam no **Dashboard** página, utilize filtros. Para apresentar apenas o **camião** dispositivos, escolha o incorporado **Trucks** filtro na lista pendente de filtro:

![Filtrar por trucks no dashboard](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Ao aplicar um filtro, apenas os dispositivos que correspondam às condições filtro apresentam no mapa no **Dashboard** página:

![Apresentar trucks no mapa](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

O filtro também determina quais os dispositivos que vê no **telemetria** gráfico:

![Apresenta a telemetria camião no dashboard](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Para criar, editar e eliminar os filtros, escolha **gerir filtros**.

## <a name="view-real-time-telemetry"></a>Ver telemetria em tempo real

A solução pré-configurada rastreia dados de telemetria em tempo real de detalhado no gráfico no **Dashboard** página. O gráfico de telemetria mostra informações de telemetria para os dispositivos selecionados pelo filtro de atual:

![Desenho de telemetria camião](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Para selecionar os valores de telemetria para ver, escolha o tipo de telemetria na parte superior do gráfico:

![Desenho de telemetria camião](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

Para colocar em pausa a apresentação de telemetria em direto, escolha **Flowing**. Para reativar a apresentação em direto, escolha **pausa**:

![Colocar em pausa e reinicie a apresentação de telemetria](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)

## <a name="use-the-map"></a>Utilize o mapa

O mapa apresenta informações sobre os trucks simuladas selecionado pelo filtro de atual. Pode ampliem e desloquem o mapa para visualizar localizações detalhadamente mais ou menos. Os ícones de dispositivo no mapa indicam qualquer **alarmes** ou **avisos** que estão ativos para o dispositivo. Um resumo do número de **alarmes** e **avisos** apresenta à esquerda do mapa.

Para ver os detalhes do dispositivo, efetuar um deslocamento panorâmico e zoom o mapa para localizar os dispositivos e clique no dispositivo no mapa. Os detalhes incluem:

* Valores de telemetria recentes
* Métodos do que dispositivo suporta
* Propriedades do dispositivo

![Ver detalhes do dispositivo no dashboard](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)

## <a name="view-alarms-from-your-devices"></a>Vista alarmes dos seus dispositivos

O mapa realça os dispositivos no filtro atual com **alarmes** e **avisos**. O **alarmes sistema** painel mostra informações detalhadas sobre os alarmes mais recentes dos seus dispositivos:

![Alarmes do sistema de vista no dashboard](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Pode utilizar o **alarmes sistema** filtro para ajustar o intervalo de tempo de alarmes recentes. Por predefinição, o painel apresenta alarmes de última hora:

![Filtrar os alarmes por hora](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

> [!NOTE]
> Desloque para a direita para ver o filtro de span de tempo.

## <a name="view-the-system-kpis"></a>Ver o sistema KPIs

O **Dashboard** página apresenta os KPIs de sistema:

![Filtrar os alarmes por hora](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Pode utilizar o **sistema KPI** filtro para ajustar o intervalo de tempo para a agregação de KPI. Por predefinição, o painel apresenta KPIs agregados ao longo de última hora.

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou como utilizar o **Dashboard** página para filtrar e monitorizar os trucks simuladas aprovisionados na sua solução de monitorização remota:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrar os dispositivos no dashboard
> * Ver telemetria em tempo real
> * Ver detalhes do dispositivo
> * Vista alarmes dos seus dispositivos
> * Ver o sistema KPIs

Agora que aprendeu como monitorizar os seus dispositivos, os passos sugeridos são saber como:

* [Detetar problemas através de regras baseadas em limiares](./iot-suite-remote-monitoring-automate.md).
* [Gerir e configurar os seus dispositivos](./iot-suite-remote-monitoring-manage.md).
* [Resolver problemas e resolver problemas de dispositivo](./iot-suite-remote-monitoring-maintain.md).
* [Testar a sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->
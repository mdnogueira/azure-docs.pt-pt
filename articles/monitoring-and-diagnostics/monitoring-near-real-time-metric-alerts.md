---
title: "Quase em tempo real alertas métricas no Monitor do Azure | Microsoft Docs"
description: "Quase em tempo real métrica alertas permitem-lhe monitorizar métricas de recurso do Azure com uma frequência de 1 min."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Quase em tempo real alertas métricas (pré-visualização)
Monitor do Azure suporta agora um novo tipo de alertas métricas chamado quase em tempo real métrica alertas (pré-visualização). Esta funcionalidade está atualmente em pré-visualização pública.
Estes alertas diferem dos alertas de métricas regulares em algumas formas

- **Melhorado latência** -quase em tempo real métrica alertas podem monitorizar as alterações nos valores métricas logo 1 min.
- **Mais controlo sobre condições métricas** -quase em tempo real métrica alertas, permitir que os utilizadores definir regras de alertas mais rica. Os alertas agora suportam os valores máximo, mínimos, médios e totais das métricas de monitorização.
- **Combinar a monitorização de várias métricas** -quase em tempo real métrica alertas podem monitorizar várias métricas (atualmente duas) com uma única regra. Alerta obtém acionado se ambas as métricas infringir os respetivos limiares para o período de tempo especificado.
- **Sistema de notificação modulares** - quase em tempo real alertas de métrica de utilização [grupos ação](monitoring-action-groups.md). Esta funcionalidade permite aos utilizadores criar ações de forma modular e reutilize-as para várias regras de alertas.

> [!NOTE]
> Quase em tempo real alertas métricas funcionalidade está atualmente em pré-visualização pública. A experiência de utilizador e a funcionalidade está sujeita a alterações.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Os recursos que podem criar quase em tempo real alertas métricas para?
Obter uma lista completa dos tipos de recursos que são suportadas pelo quase em tempo real alertas métricas:

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Network/applicationgateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>Criar um alerta de métrico Near em tempo real
Atualmente, quase em tempo real métrica alertas só podem ser criados através do portal do Azure. Suporte para configurar quase em tempo real alertas métricas através do PowerShell, a interface de linha de comandos (CLI) e API REST da Azure Monitor estará disponível brevemente.

1. No [portal](https://portal.azure.com/), localize o recurso estiver interessado na monitorização e selecione-o. Este recurso deve ser de um dos tipos de recursos listados no [secção anterior](#what-resources-can-i-create-near-real-time-metric-alerts-for). Também pode fazer o mesmo para todos os tipos de recursos suportados centralmente monitor > alertas.

2. Selecione **alertas** ou **regras de alerta** na secção monitorização. Ícone de texto e podem variar devido às ligeiramente diferentes recursos.
   ![Monitorização](./media/insights-alerts-portal/AlertRulesButton.png)

3. Clique em de **adicionar o alerta de métricas de tempo real (pré-visualização) em breve** comando. Se o comando está a cinzento, certifique-se de que o recurso está selecionado no filtro.

    ![Adicionar quase em tempo real métricas botão de alerta](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. **Nome** o alerta de regra e escolha um **Descrição**, que também mostra nos e-mails de notificação.
5. Selecione o **métrica** que pretende monitorizar, em seguida, escolha um **condição**, **agregação hora**, e **limiar** valor para a métrica. Opcionalmente, selecione outro **métrica** que pretende monitorizar, em seguida, escolha um **condição**, **agregação hora**, e **limiar** valor para a segunda métrica. 

    ![Adicionar quase em tempo real métricas Alert1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) ![adicionar quase em tempo real métricas Alert2](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. Escolha o **período** de tempo que as regras de métricas devem ser satisfeitas antes dos acionadores de alerta. Por isso, por exemplo, se utilizar o período "nos últimos 5 minutos" e a procura de alerta para a CPU acima 80% (e NetworkIn acima 500 MB), o alerta acionado quando a CPU foi consistentemente acima 80% para 5 minutos. Depois de ocorre o primeiro acionador, novamente acionado quando a CPU permanece inferior a 80% para 5 minutos. O alerta é avaliado de acordo com o **frequência de avaliação**


6. Escolha um adequado **gravidade** na lista pendente.

7. Especifique se pretende utilizar um novo ou existente **ação grupo**.

8. Se optar por criar **novo** ação grupo, o grupo de ação de dar um nome e um nome abreviado, especifique ações (Webhook SMS, E-Mail,) e preencher os respetivos detalhes.


8. Selecione **OK** quando pronto para criar o alerta.   

Dentro de alguns minutos, o alerta está ativo e aciona conforme descrito anteriormente.

## <a name="managing-near-real-time-metric-alerts"></a>Gerir quase em tempo real alertas métricas
Assim que tiver criado um alerta, pode selecionar e:

* Ver um gráfico que mostra o limiar de métrico e os valores reais do dia anterior.
* Editar ou eliminá-lo.
* **Desativar** ou **ativar** -se de que pretende temporariamente interromper ou retomar a receção de notificações para esse alerta.




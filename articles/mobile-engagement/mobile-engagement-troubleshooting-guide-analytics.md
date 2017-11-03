---
title: "Do Azure Mobile Engagement guia - análise de resolução de problemas"
description: "Resolução de problemas de análise, monitorização, segmentação e Dashboard no Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: e30c9ac0a8421ffcf4fc3e2548cfd7ac49701900
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Guia de resolução de problemas para problemas de análise, monitorização, segmentação e Dashboard
Seguem-se possíveis problemas podem surgir com a forma como o Azure Mobile Engagement recolhe informações sobre as aplicações, dispositivos e utilizadores.

## <a name="missingdelayed-information"></a>Informações de Delayed/em falta
### <a name="issue"></a>Problema
* Informações estão atrasadas na apresentação na análise, segmentação ou Dashboard.
* Faltam informações de monitorização.
* Faltam informações análise, segmentação ou Dashboard.
* Atingir os limites de segmentação.

### <a name="causes"></a>Causas
* Pode utilizar a API de análise, a API do Monitor, não sendo API de segmentos para ver se todos os dados está em falta a IU do visível através das APIs.
* Se o Azure Mobile Engagement SDK não está corretamente integrado na aplicação, em seguida, não poderá ver informações de análise, segmentação, monitorização ou Dashboards.
* Segmentos não podem ser alterado depois de estes são criados, segmentos só podem ser "clonado" (copiados) ou "destruído" (eliminados.). Segmentos só podem conter 10 critérios.
* A melhor forma de testar em falta informações de monitorização é um dispositivo de teste de configuração, desinstale e/ou reinstale a aplicação no dispositivo de teste.
* Informações são atualizadas a cada 24 horas para análise, segmentação ou Dashboards.
* As informações no segmentos novos podem não ser apresentadas até 24 horas após terem sido criadas, mesmo que o segmento baseia-se nas informações anteriores.
* Filtrar os seus dados de análise na IU irá mostrar todos os exemplos deste tipo, independentemente da versão da sua aplicação (por exemplo "Falha" filtrados por nome mostrará da versão 1 e a versão 2 da sua aplicação).
* O período de tempo para a análise baseia-se a data a partir das definições de dispositivo dos utilizadores, pelo que um utilizador cujo telefone com a data incorretamente foi apareçam no período de tempo errado.
* Pushes não do lado do servidor, os dados são registados quando utiliza o botão para "teste", os dados são registados apenas para as campanhas real push.

## <a name="cant-locate-items-in-ui"></a>Não é possível localizar itens na IU
### <a name="issue"></a>Problema
* Não é possível criar segmentos com base em determinados incorporada ou critérios de etiqueta de informações de aplicação personalizada.
* Não é possível localizar determinados incorporada ou informações de aplicação personalizada tag critérios de análise, monitorização ou Dashboards.
* Não é possível interpretar os dados de análise, monitorização, segmentação ou Dashboards.

### <a name="causes"></a>Causas
* Algumas incorporada itens e etiquetas de informações de aplicação só estão disponíveis como critério de push, mas não podem ser adicionado a um segmento ou visível de análise, monitorização ou Dashboard. 
* Para itens incorporados e as etiquetas de informações de aplicação que não não possível adicionar um segmento, terá da lista de configuração de filtragem de critérios de cada campanha para efetuar a mesma função que filtragem com base num segmento.
* Consulte os menus de contexto nas secções análise, monitorização, segmentação e Dashboards da IU do Azure Mobile Engagement para obter mais ajuda e como as informações.

## <a name="crash-troubleshooting"></a>Resolução de problemas de falhas
### <a name="issue"></a>Problema
* Falha da aplicação no análise, monitorização ou Dashboard.

### <a name="causes"></a>Causas
* Para resolver problemas de falha da aplicação de análise, monitorização ou Dashboard Certifique-se verificar as notas de versão para problemas conhecidos com versões anteriores do SDK.
* Para resolver falhas na aplicação efetuar um evento de um dispositivo de teste com a sua aplicação instalada e a procura do ID do dispositivo na secção "Eventos de – do Monitor" da IU do Azure Mobile Engagement. Em seguida, execute o evento que está a causar a aplicação de falhas e procure informações adicionais na secção "– de Monitor de falhas" da IU do Azure Mobile Engagement. 


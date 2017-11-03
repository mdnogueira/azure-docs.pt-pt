---
title: "Analisar padrões de utilização do Azure CDN | Microsoft Docs"
description: "Cliente pode ativar a análise de registos para a CDN do Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: 04e5499011e72dfcc20dff370d5d837227ed29b6
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar padrões de utilização da CDN do Azure

Depois de ativar a CDN para a sua aplicação, pode monitorizar a utilização da CDN, verifique o estado de funcionamento do seu entrega e potenciais problemas de resolução de problemas. CDN do Azure fornece duas formas destas capacidades o seguinte: 

## <a name="verizon-core-reports"></a>Relatórios de núcleos da Verizon

Como um utilizador CDN do Azure com uma standard da Verizon ou um perfil de premium da Verizon, pode ver relatórios de núcleos da Verizon no portal suplementar da Verizon. Relatórios de núcleos da Verizon está acessível através de **gerir** opção do portal do Azure e oferece uma variedade de vistas e gráficos. Para obter mais informações, consulte [relatórios de núcleos da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Relatórios personalizados da Verizon

Como um utilizador CDN do Azure com uma standard da Verizon ou um perfil de premium da Verizon, pode ver da Verizon personalizada relatórios no portal suplementar da Verizon. Relatórios de personalizada da Verizon está acessível através de **gerir** opção do portal do Azure. Mostra o página de relatórios de personalizada da Verizon o número de pedidos ou dados transferido para cada contorno CName que pertencem a um perfil de CDN do Azure. Os dados podem ser agrupados por Estado de cache ou de código de resposta HTTP durante qualquer período de tempo. Para obter mais informações, consulte [relatórios personalizados da Verizon](cdn-verizon-custom-reports.md).

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise de núcleo através de registos de diagnóstico do Azure

Análise de núcleo está disponível para todos os pontos finais da CDN que pertencem da Verizon (Standard e Premium) e perfis da CDN (Standard) Akamai. Registos de diagnóstico do Azure permitem a análise de núcleo ser exportados para o armazenamento do Azure, os event hubs ou análise de registos do Operations Management Suite (OMS). Análise de registos do OMS oferece uma solução com gráficos configurável pelo utilizador e personalizáveis. Para obter mais informações, consulte [registos de diagnóstico do Azure](cdn-azure-diagnostic-logs.md).


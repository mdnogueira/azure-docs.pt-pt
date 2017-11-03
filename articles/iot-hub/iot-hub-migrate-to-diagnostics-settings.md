---
title: "Migrar o IoT Hub do Azure para as definições de diagnóstico | Microsoft Docs"
description: "Como atualizar o IoT Hub do Azure para utilizar as definições de diagnóstico do Azure em vez de operações de monitorização para monitorizar o estado de operações no seu IoT hub em tempo real."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 2716f397ad0e7abfdcd397340da8fa8116a172db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrar o seu IoT Hub a partir de operações de monitorização para as definições de diagnóstico

Clientes que utilizam [operações de monitorização] [ lnk-opsmon] controlar o estado das operações do IoT Hub pode migrar esse fluxo de trabalho [as definições de diagnóstico do Azure] [ lnk-diagnostics-settings], uma funcionalidade do Monitor do Azure. As definições de diagnóstico fornecem informações de diagnóstico de nível de recursos para vários serviços do Azure.

As operações de funcionalidade de monitorização do IoT Hub foi preterida e será removida no futuro. Este artigo fornece os passos para mover as cargas de trabalho de monitorização para as definições de diagnóstico de operações. Para obter mais informações sobre a linha cronológica descontinuação, consulte [monitorizar as suas soluções de IoT do Azure com o Monitor do Azure e o estado de funcionamento de recursos de Azure][lnk-blog-announcement].

## <a name="update-iot-hub"></a>Atualizar o IoT Hub

Para atualizar o seu IoT Hub no portal do Azure, primeiro ative as definições de diagnóstico, em seguida, desative a monitorização de operações.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Desativar a monitorização de operações

Depois de ter testado as novas definições de diagnóstico no seu fluxo de trabalho, pode desativar as funcionalidade de monitorização de operações. 

1. No menu IoT Hub, selecione **operações de monitorização**.
1. Em cada categoria de monitorização, selecione **nenhum**.
1. Guarde as operações de monitorização de alterações.

## <a name="update-applications-that-use-operations-monitoring"></a>Atualizar aplicações que utilizam as operações de monitorização

Os esquemas para operações de monitorização e as definições de diagnóstico variam ligeiramente. É importante que Atualize as aplicações que utilizam as operações de monitorização de hoje para mapear para o esquema utilizado pelas definições de diagnóstico. 

Além disso, diagnóstico definições ofertas de controlo para cinco categorias de novo. Depois de atualizar aplicações para o esquema existente, adicione as categorias de novo:

- Operações de nuvem para o dispositivo duplo
- Operações de dispositivo para nuvem duplo
- Consultas de duplo
- Operações de tarefas
- Métodos diretos

Para as estruturas de esquema específicos, consulte [compreender o esquema para as definições de diagnóstico][lnk-diagnostics-schema].

## <a name="next-steps"></a>Passos seguintes

- [Monitorizar o estado de funcionamento do Azure IoT Hub e diagnosticar problemas rapidamente][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md

---
title: "Como enviar notificações agendadas | Microsoft Docs"
description: "Este tópico descreve a utilizar notificações agendada com Notification Hubs do Azure."
services: notification-hubs
documentationcenter: .net
keywords: "notificações push, notificação push, notificações push de agendamento"
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-scheduled-notifications"></a>Como: Enviar notificações agendadas
## <a name="overview"></a>Descrição geral
Se tiver um cenário no qual pretende enviar uma notificação, a determinada altura no futuro mas não dispõe de uma forma fácil de reativação se o código de back-end para enviar a notificação. Hubs de notificação de escalão Standard suporta uma funcionalidade que permite-lhe agendar notificações de segurança para 7 dias no futuro.

Quando enviar uma notificação, basta utilizar o [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) classe no SDK dos Notification Hubs, conforme mostrado no exemplo seguinte:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Além disso, pode cancelar uma notificação anteriormente agendada utilizando o respetivo notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Não existem não existem limites no número de notificações agendadas, que pode enviar.


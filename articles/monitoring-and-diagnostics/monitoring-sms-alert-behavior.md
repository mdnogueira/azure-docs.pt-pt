---
title: "Comportamento de alerta de SMS em grupos de ação | Microsoft Docs"
description: "Formato de mensagem SMS e responder a mensagens SMS para anular a subscrição, resubscribe ou pedir ajuda."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 3e4eca174209eeb9cbce1d45111d1e5cc30af8b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamento de grupos de ação de alerta de SMS
## <a name="overview"></a>Descrição geral ##
Grupos de ação permitem-lhe configurar uma lista de recetores. Estes grupos, em seguida, podem ser aproveitados quando definir alertas do registo de atividade; garantir que um grupo de ação específica é notificado quando o alerta de registo de atividade é activado. Um dos mecanismos de alertas suportados é SMS; os alertas suportam comunicação bidirecional. Um utilizador pode responder a um alerta para:

- **Anular a subscrição de alertas:** um utilizador pode anular todos os alertas SMS para todos os grupos de ação ou um grupo de ação único.  
- **Resubscribe alertas:** um utilizador pode resubscribe para todos os alertas SMS para todos os grupos de ação ou um grupo de ação único.  
- **Pedir ajuda:** um utilizador pode pedir para obter mais informações sobre o SMS. Serão redirecionados para este artigo

Este artigo abrange o comportamento dos alertas SMS e as ações de resposta o utilizador pode efetuar com base na região do utilizador:

## <a name="usacanada-sms-behavior"></a>Comportamento de SMS EUA/Canadá
### <a name="receiving-an-sms-alert"></a>Receber um alerta SMS
Um recetor SMS, que é configurado como parte de um grupo de ação, irá receber um SMS quando um alerta é acionado. O SMS irá transportar as seguintes informações:
* Shortname do grupo de ação que este alerta foi enviado para
- Título do alerta

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>Unsubscribing a partir dos alertas SMS de um grupo de ação
Um utilizador pode anular a subscrição do SMS para alertas para o grupo de uma ação ao responder a shortcode 20873, com as palavras-chave: "DESATIVAR &lt;Shortname do grupo de ação&gt;".

Ex. Um utilizador novidade anular a subscrição de alertas para um grupo de ação com o shortname "Azure", seriam enviar um SMS para shortcode 20873 que diz "DESATIVAR Azure"

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>Unsubscribing a partir dos alertas SMS para todos os grupos de ação
Um utilizador pode anular todos os alertas SMS para todos os grupos de ação ao responder ao shortcode 20873 com qualquer um dos seguintes palavras-chave:
* PARAR

Ex. Um utilizador novidade anular todos os alertas SMS para todos os grupos de ação, seria enviar um SMS para o shortcode 20873 que diz "Parar"

>[!NOTE]
>Se um utilizador anulou a subscrição do SMS alertas, mas, em seguida, é adicionado a um novo grupo de ação; estes irão receber alertas SMS para este novo grupo de ação, mas permanecem unsubscribed de todos os grupos de ação anterior.
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>Resubscribing a alertas SMS de um grupo de ação
Um utilizador pode resubscribe para SMS para alertas para o grupo de uma ação ao responder a shortcode 20873, com as palavras-chave: "ATIVAR &lt;Shortname do grupo de ação&gt;".

Ex. Um utilizador novidade resubscribe alertas para um grupo de ação com o shortname "Azure", seriam enviar um SMS para shortcode 20873 que diz "ATIVAR o Azure"

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>Resubscribing a alertas SMS para todos os grupos de ação
Um utilizador pode resubscribe para todas as SMS para alertas para todos os grupos de ação ao responder ao shortcode 20873 com qualquer um dos seguintes palavras-chave:

* INICIAR

Ex. Um utilizador novidade anular todos os alertas SMS para todos os grupos de ação, seria enviar um SMS para o shortcode 20873 que diz "START"

### <a name="requesting-help-via-sms"></a>Pedir ajuda por SMS
Um utilizador pode pedir para obter mais informações sobre a SMS que receberam por responder ao shortcode 20873 com qualquer um dos seguintes palavras-chave:
* AJUDA

Será enviada uma resposta para o utilizador com uma ligação a este artigo.

## <a name="next-steps"></a>Passos Seguintes
Obter um [descrição geral dos alertas de registo de atividade](monitoring-overview-alerts.md) e saber como receber alertas  
Saiba mais sobre [limitação de taxa SMS](monitoring-alerts-rate-limiting.md)  
Saiba mais sobre [grupos de ação](monitoring-action-groups.md)

---
title: "Taxa de limitação de SMS, mensagens de correio eletrónico e webhooks | Microsoft Docs"
description: "Compreenda a forma como o Azure limita o número de notificações possíveis de SMS, o e-mail ou o webhook de um grupo de ação."
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
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>Taxa de limitação para mensagens de SMS, mensagens de correio eletrónico e webhook publicações
Limitação de taxa é um suspensão de notificações que ocorre quando existem demasiados notificações são enviadas para um determinado phone número ou endereço de e-mail. Limitação de taxa garante que os alertas são geríveis e passíveis de ação.

As regras de SMS e o e-mail são os mesmos. O limiar de limite de taxa é:

 - **SMS**: 10 mensagens numa hora.
 - **E-mail**: 100 mensagens numa hora.

## <a name="rate-limit-rules"></a>Regras de limite de taxa
- Um número de telefone específico ou o e-mail é limitada quando recebe mensagens mais do que permite que o limiar de taxa.
- Um número de telefone ou e-mail pode fazer parte de grupos de ação entre várias subscrições. Limitação de taxa aplica-se em todas as subscrições. Aplica-se, assim que o limiar for atingido, mesmo se as mensagens são enviadas a partir de várias subscrições.  
- Quando um número de telefone ou e-mail é a velocidade limitada, é enviada uma notificação adicional para comunicar a limitação de taxa. Os Estados de notificação quando expira a limitação de taxa.

## <a name="rate-limit-of-webhooks"></a>Limite de velocidade de webhooks ##
Não há nenhum taxa de limitação no local para webhooks.

## <a name="next-steps"></a>Passos seguintes ##
* Saiba mais sobre [SMS alerta comportamento](monitoring-sms-alert-behavior.md).
* Obter um [descrição geral dos alertas de registo de atividade](monitoring-overview-alerts.md)e saber como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de estado de funcionamento do serviço é publicada](monitoring-activity-log-alerts-on-service-notifications.md).

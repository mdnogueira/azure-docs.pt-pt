---
title: "Quais são as notificações de estado de funcionamento do serviço | Microsoft Docs"
description: "Notificações do Estado de funcionamento do serviço permitem-lhe ver o funcionamento do serviço publicar mensagens pelo Microsoft Azure."
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
ms.openlocfilehash: efdd42d244710b27fc33154b708cfbe40312e3b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="service-health-notifications"></a>Notificações do Estado de funcionamento do serviço
## <a name="overview"></a>Descrição geral

Este artigo mostra como ver notificações de estado de funcionamento de serviço com o portal do Azure.

Notificações do Estado de funcionamento do serviço permitem-lhe ver mensagens de estado de funcionamento de serviço publicadas pela equipa do Azure que possam estar a afetar os recursos na sua subscrição. Estas notificações são uma classe secundárias da atividade de registo de eventos e também podem ser encontradas no registo de atividade. Notificações do Estado de funcionamento do serviço podem ser informativa ou acionável consoante a classe.

Existem cinco classes de notificações do Estado de funcionamento do serviço:  

- **Ação necessária:** de hora a hora Azure poderá reparar algo invulgar acontecer na sua conta. Azure poderá ter de trabalhar para resolver isto. Azure lhe-á enviada uma notificação quer com detalhes sobre as ações precisa de tomar ou com detalhes sobre como contactar o suporte ou de engenharia do Azure.  
- **Recuperação assistida:** tiver ocorrido um evento e engenheiros tem confirmado que ainda estão a experienciar impacto. Tem de engenharia do Azure trabalhar consigo diretamente para restaurar os serviços de estado de funcionamento completo.  
- **Incidente:** um serviço afetar evento está atualmente a afetar um ou mais recursos na sua subscrição.  
- **Manutenção:** trata de uma notificação a informar que uma atividade de manutenção planeada que possam afetar um ou mais dos recursos na sua subscrição.  
- **Informações:** ocasionalmente, Azure pode enviar notificações informam sobre potenciais otimizações que podem ajudar a melhorar a sua utilização de recursos.  
- **Segurança:** segurança urgente relacionadas com as informações relativas a sua solution(s) em execução no Azure.

Cada notificação de estado de funcionamento do serviço inclui detalhes sobre o âmbito e o impacto para os recursos. Detalhes incluem:

Nome da propriedade | Descrição
-------- | -----------
canais | Um dos seguintes valores: "Admin", "Operação"
correlationId | Normalmente, é um GUID no formato de cadeia. Eventos com que pertencem à mesma ação de uber normalmente partilhar o mesmo correlationId.
eventDataId | É o identificador exclusivo de um evento
EventName | É o título do evento
nível | Nível do evento. Um dos seguintes valores: "Críticas", "Error", "Aviso", "Informativa" e "Verboso"
resourceProviderName | Nome do fornecedor de recursos para o recurso afectado
resourceType| O tipo de recurso do recurso afectado
Subestado | Normalmente, o código de estado HTTP do resto correspondente chamar, mas também pode incluir outras cadeias que descrevem um subestado, como estes valores comuns: OK (código de estado de HTTP: 200), criado (código de estado HTTP: 201), aceite (código de estado HTTP: 202), não conteúdo (código de estado de HTTP: 204), pedido incorreto (código de estado de HTTP: 400), não foi encontrado (código de estado de HTTP: 404), conflito (código de estado HTTP : 409), erro de servidor interno (código de estado HTTP: 500), serviço indisponível (código de estado HTTP: 503), tempo limite do Gateway (código de estado HTTP: 504).
eventTimestamp | Timestamp quando o evento foi gerado pelo processamento do pedido correspondente o evento de serviço do Azure.
submissionTimestamp |   Timestamp quando o evento ficou disponível para consulta.
subscriptionId | A subscrição do Azure em que este evento foi registado
status | Cadeia que descrevem o estado da operação. Alguns valores comuns são: foi iniciado em curso, com êxito, falha, Active Directory, a resolvido.
operationName | Nome da operação.
categoria | "ServiceHealth"
resourceId | ID de recurso do recurso afetado.
Properties.title | O título localizado para esta comunicação. Inglês é o idioma predefinido.
Properties.Communication | Os detalhes localizados da comunicação com o markup HTML. Inglês é o predefinido.
Properties.incidentType | Os valores possíveis: AssistedRecovery ActionRequired, informações, incidentes, manutenção, a segurança
Properties.trackingId | Identifica o incidente que está associado este evento. Utilize esta opção para correlacionar os eventos relacionados com um incidente.
Properties.impactedServices | Um blob JSON com caráter de escape que descreve os serviços e regiões que são afetadas pelo incidente. Uma lista de serviços, cada um deles tem uma ServiceName e uma lista de ImpactedRegions, cada um deles tem uma RegionName.
Properties.defaultLanguageTitle | A comunicação em inglês
Properties.defaultLanguageContent | A comunicação em inglês como markup de html ou texto simples
Properties.Stage | Os valores possíveis para AssistedRecovery, ActionRequired, informações, incidentes, de segurança: estão ativos, resolvido. Para manutenção são: Active Directory, planeada, em curso, cancelado, Rescheduled, resolvido, concluída
Properties.communicationId | A comunicação este evento está associado.


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>Ver as notificações de estado de funcionamento do serviço no portal do Azure
1.  No [portal](https://portal.azure.com), navegue para o **Monitor** serviço

    ![Monitorizar](./media/monitoring-service-notifications/home-monitor.png)
2.  Clique em de **Monitor** opção para abrir a experiência de monitorização. Monitor do Azure reúne todas as suas monitorização definições e dados para uma vista consolidada. É aberto pela primeira vez para a secção **Registo de atividade**.

3.  Agora, clique em **alertas** secção

    ![Monitorizar](./media/monitoring-service-notifications/service-health-summary.png)
4. Clique em de **+ adicionar o alerta de registo de atividade** e configurar um alerta para garantir que são notificados para notificações de serviço futuras. Para obter mais informações sobre como configurar alertas sobre as notificações de serviço [visitar a página de alertas de registo de atividade e notificações de serviço](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>Passos Seguintes:
Receber [sempre que uma notificação de estado de funcionamento do serviço de notificações de alertas](monitoring-activity-log-alerts-on-service-notifications.md) é publicada  
Saiba mais sobre [alertas de registo de atividade](monitoring-activity-log-alerts.md)

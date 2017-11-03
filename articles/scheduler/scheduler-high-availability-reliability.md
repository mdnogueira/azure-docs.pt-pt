---
title: Programador de elevada disponibilidade e fiabilidade
description: Programador de elevada disponibilidade e fiabilidade
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-high-availability-and-reliability"></a>Programador de elevada disponibilidade e fiabilidade
## <a name="azure-scheduler-high-availability"></a>O agendador do Azure de elevada disponibilidade
Como um núcleo de serviço de plataforma do Azure, o agendador do Azure está altamente disponível e as funcionalidades de implementação de serviço com redundância geográfica e o tarefa regionais georreplicação da replicação.

### <a name="geo-redundant-service-deployment"></a>Implementação de serviço com redundância geográfica
O agendador do Azure está disponível através da IU em quase todas as região geográfica que está atualmente no Azure. A lista de regiões agendador do Azure está disponível no está [listados aqui](https://azure.microsoft.com/regions/#services). Se um centro de dados numa região alojada é composto indisponível, as capacidades de ativação pós-falha do agendador do Azure são, de modo a que o serviço está disponível a partir do Centro de dados de outro.

### <a name="geo-regional-job-replication"></a>Replicação de tarefa Georreplicação regionais
Não só o agendador do Azure é front-end disponível para pedidos de gestão, mas o seu próprio trabalho também é georreplicação. Quando houver uma falha na região de um, o agendador do Azure a ativação pós-falha e assegura que a tarefa é executada a partir de outro centro de dados na região geográfica emparelhado.

Por exemplo, se criou uma tarefa no Sul Central dos EUA, o agendador do Azure replica automaticamente essa tarefa no Norte Central-nos. Quando existe uma falha no Sul Central dos EUA, o agendador do Azure garante que a tarefa é executada a partir do Norte Central-nos. 

![][1]

Como resultado, o agendador do Azure garante que os seus dados permanecem na mesma região geográfica mais ampla em caso de uma falha do Azure. Como resultado, não precisa de duplicar a tarefa apenas para adicionar a elevada disponibilidade – agendador do Azure fornece automaticamente capacidades de elevada disponibilidade para os trabalhos.

## <a name="azure-scheduler-reliability"></a>Fiabilidade do agendador do Azure
O agendador do Azure garante que as suas próprias elevada disponibilidade e assume uma abordagem diferentes tarefas criados pelo utilizador. Por exemplo, a tarefa pode invocar um ponto final de HTTP que não está disponível. O agendador do Azure nonetheless tenta executar a tarefa com êxito, pelo que lhe confere alternativas opções para lidar com falha. O agendador do Azure efetua este procedimento de duas formas:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Política Repita configurável através de "retryPolicy"
O agendador do Azure permite-lhe configurar uma política de repetição. Por predefinição, se uma tarefa falhar, programador tenta a tarefa novamente quatro vezes mais, intervalos de 30 segundo. Pode reconfigurar esta política de repetição para ser mais agressiva (por exemplo, dez vezes, em intervalos de 30 segundo) ou looser (por exemplo, duas vezes em intervalos diários.)

Como um exemplo de quando isto pode ajudar a, pode criar uma tarefa que é executada uma vez por semana e invoca um ponto final de HTTP. Se o ponto final de HTTP está inativo para algumas horas, quando a tarefa é executada, poderá não querer Aguarde uma semana mais para a tarefa para ser executada novamente, uma vez que irão falhar até a política de repetição de predefinição. Nestes casos, poderá reconfigurar a política de repetição padrão para repetir a cada três horas (por exemplo) em vez de cada 30 segundos.

Para saber como configurar uma política de repetição, consulte [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Capacidade de ponto final alternativo através de "errorAction"
Se o ponto final de destino para a tarefa do agendador do Azure permanece inacessível, agendador do Azure retrocede para o ponto final de processamento de erros alternativo depois de seguir a política de repetição. Se estiver configurado um ponto de final alternativo do processamento de erros, o agendador do Azure invoca-lo. Com um ponto final alternativo, os seus próprios trabalhos são de elevada disponibilidade face a falhas.

Por exemplo, no diagrama abaixo, o agendador do Azure segue a política de repetição para aceder um serviço web de Nova Iorque. Depois das repetições falharem, verifica se há alternativa. Em seguida, passa diretamente e começa a efetuar pedidos alternativo com a mesma política de repetição.

![][2]

Tenha em atenção que se aplica a mesma política de repetição para a ação original e a ação de erro alternativo. Também é possível ter o tipo de ação da ação de erro alternativo ser diferente do tipo de ação da ação principal. Por exemplo, enquanto a ação principal pode ser invocar um ponto final de HTTP, a ação de erro em vez disso, pode ser uma fila de armazenamento, a fila de barramento de serviço ou a ação de tópico de barramento de serviço que suporta o registo de erros.

Para saber como configurar um ponto final alternativo, consulte [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Veja Também
 [O que é o Scheduler?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Scheduler](scheduler-plans-billing.md)

 [Como criar agendas complexas e periodicidade avançada com o Azure Scheduler](scheduler-advanced-complexity.md)

 [Referência da API REST do Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)

 [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Azure Scheduler](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png

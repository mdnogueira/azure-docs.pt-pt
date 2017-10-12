---
title: "O que é o Agendador do Azure? | Microsoft Docs"
description: "O Agendador do Azure permite-lhe descrever detalhamente as ações a executar na nuvem. Então agenda e executa essas ações automaticamente."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: a3bf1aacd6978499d7ef77cbcb451a06b857ac38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-scheduler"></a>O que é o Agendador do Azure?
O Agendador do Azure permite-lhe descrever detalhamente as ações a executar na nuvem. Então agenda e executa essas ações automaticamente.  O Agendador pode fazê-lo através do [Portal do Azure](scheduler-get-started-portal.md), POR código, [API REST](https://msdn.microsoft.com/library/mt629143.aspx) ou do Azure PowerShell.

O Agendador cria, mantém e convoca trabalho agendado.  O Agendador não aloja quaisquer cargas de trabalho ou executa nenhum código. Apenas *invoca* código alojado noutro local, como no Azure, no local, ou noutro fornecedor. Invoca através de HTTP, HTTPS, uma fila de armazenamento, uma fila do barramento de serviço ou de um tópico de barramento de serviço.

O Agendador agenda [tarefas](scheduler-concepts-terms.md), mantém um histórico dos resultados de execução de tarefas que é possível analisar e de forma determinística, agenda a execução das cargas de trabalho. O WebJobs do Azure (parte da funcionalidade de Aplicações Web do Serviço de Aplicações do Azure) e outras funcionalidades de agendamento do Azure, utilizam o Agendador em segundo plano. A [API REST do Agendador](https://msdn.microsoft.com/library/mt629143.aspx) ajuda-o a gerir a comunicação para estas ações. Como tal, o Agendador suporta [agendas complexas e periodicidade avançada](scheduler-advanced-complexity.md) com mais facilidade.

Existem vários cenários que se devem à utilização do Agendador. Por exemplo:

* *Ações de aplicação recorrente:* recolha periódica de dados a partir do Twitter para um feed.
* *Manutenção diária:* eliminação diária de registos, realizar cópias de segurança e outras tarefas de manutenção. Por exemplo, um administrador pode escolher fazer uma cópia de segurança da base de dados às 1:00. todos os dias durante os próximos nove meses.

O Agendador permite-lhe criar, atualizar, eliminar, ver e gerir tarefas e [recolher tarefas](scheduler-concepts-terms.md) de forma programática utilizando scripts e através do portal.

## <a name="see-also"></a>Consultar também
 [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Scheduler](scheduler-plans-billing.md)

 [Como criar agendas complexas e periodicidade avançada com o Azure Scheduler](scheduler-advanced-complexity.md)

 [Referência da API REST do Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)

 [Elevada disponibilidade e fiabilidade do Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Azure Scheduler](scheduler-outbound-authentication.md)


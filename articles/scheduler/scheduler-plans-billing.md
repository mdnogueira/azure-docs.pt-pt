---
title: "Planos e faturação no agendador do Azure"
description: "Planos e faturação no agendador do Azure"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: f0662230c5d1663e37ee2be58f234934ec3d55dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Planos e faturação no agendador do Azure
## <a name="job-collection-plans"></a>Planos de recolha de tarefa
As coleções de tarefas são a entidade sujeito a faturação no agendador do Azure. As coleções de tarefas contém um número de tarefas e são fornecidos em três planos – gratuito, Standard e Premium – que são descritos abaixo.

| **Plano de recolha de tarefa** | **N. º máximo de tarefas por coleção de tarefas** | **Periodicidade máx.** | **Coleções de tarefas de máx. por subscrição** | **Limites** |
|:--- |:--- |:--- |:--- |:--- |
| **Livre** |5 tarefas por coleção de tarefas |Uma vez por hora. Não é possível executar as tarefas mais frequentemente do que uma vez a uma hora |Uma subscrição é permitida até 1 coleção de tarefas gratuita |Não é possível utilizar [objecto de autorização de saída de HTTP](scheduler-outbound-authentication.md) |
| **Standard** |50 tarefas por coleção de tarefas |Uma vez por minuto. Não é possível executar as tarefas mais frequentemente do que uma vez um minuto |Uma subscrição é permitida até 100 coleções de tarefas padrão |Acesso ao conjunto de funcionalidades completo do agendador |
| **P10 Premium** |50 tarefas por coleção de tarefas |Uma vez por minuto. Não é possível executar as tarefas mais frequentemente do que uma vez um minuto |Uma subscrição é permitida até 10 000 coleções de tarefas P10 Premium. <a href="mailto:wapteams@microsoft.com">Contacte-nos</a> para obter mais informações. |Acesso ao conjunto de funcionalidades completo do agendador |
| **P20 Premium** |tarefas de 1000 por coleção de tarefas |Uma vez por minuto. Não é possível executar as tarefas mais frequentemente do que uma vez um minuto |Uma subscrição é permitida até 10 000 coleções de tarefas P20 Premium. <a href="mailto:wapteams@microsoft.com">Contacte-nos</a> para obter mais informações. |Acesso ao conjunto de funcionalidades completo do agendador |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Atualizações e Downgrades dos planos de coleção de tarefas
Pode atualizar ou mudar um plano de recolha em qualquer altura do trabalho, entre os planos de gratuito, Standard e Premium. No entanto, quando a mudança para uma coleção de tarefas gratuita, a mudança para versão anterior pode falhar com um dos seguintes motivos:

* Uma coleção de tarefas gratuita já existe na subscrição
* Uma tarefa na coleção de tarefas tem uma periodicidade superior ao permitido para as tarefas em coleções de tarefas gratuitas. A periodicidade máxima permitida de uma coleção de tarefas gratuitas é uma vez por hora
* Há mais de 5 de tarefas na coleção de tarefas
* Uma tarefa na coleção de tarefas tem uma ação de HTTP ou HTTPS que utiliza um [objecto de autorização de saída de HTTP](scheduler-outbound-authentication.md)

## <a name="billing-and-azure-plans"></a>Planos de faturação e o Azure
Subscrições não serem cobradas gratuitamente coleções de tarefas. Se tiver mais do que 100 padrão as coleções de tarefas (10 unidades de faturação padrão), em seguida, é um melhor grau ter todas as coleções de tarefas no plano de premium.

Se tiver uma coleção de tarefas padrão e a coleção de tarefas um premium, são-lhe cobrada um padrão à unidade de faturação *e* uma unidade de faturação premium. As faturas do serviço de agendador com base no número de coleções de tarefas ativas que estão definidas para standard ou premium; Isto é explicado mais nas dois secções.

## <a name="standard-billable-units"></a>Unidades padrão de facturável
Uma unidade de sujeito a faturação padrão pode incluir até 10 coleções de tarefas padrão. Uma vez que uma coleção de tarefas standard pode ter até 50 tarefas por coleção de tarefas, uma unidade de faturação padrão permite uma subscrição ter até 500 tarefas – até execuções de tarefa quase milhões de 22 por mês.

Se tiver entre 1 e 10 coleções de tarefas standard, serão cobrados de 1 unidade de faturação padrão. Se tiver entre coleções de tarefas padrão de 11 e 20, serão cobrados 2 unidades de faturação padrão. Se tiver entre 21 e coleções de 30 tarefas standard, serão cobrados 3 unidades padrão de faturação e assim sucessivamente.

## <a name="p10-premium-billable-units"></a>P10 Unidades de sujeito a faturação de Premium
Uma unidade de sujeito a faturação premium P10 pode incluir até 10 000 P10 premium as coleções de tarefas. Uma vez que uma coleção de tarefas de premium P10 pode ter até 50 tarefas por coleção de tarefas, uma unidade de faturação premium permite uma subscrição ter até 500 000 tarefas – até execuções de tarefa quase mil milhões de 22 por mês.

Se tiver entre 1 e 10 000 premium as coleções de tarefas, serão cobrados para 1 P10 premium à unidade de faturação. Se tiver entre 10,001 e coleções de tarefas de 20.000 premium, serão cobrados para 2 unidades faturação de premium P10 e assim sucessivamente.

Assim, as coleções de tarefas de premium P10 tem a mesma funcionalidade que as coleções de tarefas standard, mas fornecem uma quebra de preço no caso da aplicação requer um grande número de coleções de tarefas.

## <a name="p20-premium-billable-units"></a>P20 Unidades de sujeito a faturação de Premium
Uma unidade de sujeito a faturação premium P20 pode incluir até 5 000 P20 premium as coleções de tarefas. Uma vez que uma coleção de tarefas de premium P20 pode ter até 1000 tarefas por coleção de tarefas, uma unidade de faturação premium permite uma subscrição ter até 5,000,000 tarefas – até execuções de tarefa quase mil milhões de 220 por mês.

As coleções de tarefas de premium P20 fornece as mesmas capacidades que as coleções de tarefas de premium P10, mas também suporta um tarefas número maior por coleção de tarefas e um maior número total de tarefas gerais que premium P10, permitindo-lhe ter mais de escalabilidade.

## <a name="billing-and-active-status"></a>Estado de faturação e Active Directory
As coleções de tarefas são sempre Active Directory, a menos que a sua subscrição completa tornou-se para algumas temporário estado desativado devido a problemas de faturação. É a única forma de garantir que uma coleção de tarefas não é faturada está a definir para o *livres* plano ou para eliminar a coleção de tarefas.

Embora o utilizador pode desativar todas as tarefas dentro de uma coleção de tarefas numa única operação, não se altera o estado de faturação da coleção de tarefas – a coleção de tarefas irá *ainda* cobrados. Da mesma forma, as coleções de tarefas em branco são consideradas Active Directory e serão cobradas.

## <a name="pricing"></a>Preços
Para detalhes de preços, consulte [programador preços](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Veja Também
 [O que é o Scheduler?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Referência da API REST do Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)

 [Elevada disponibilidade e fiabilidade do Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Azure Scheduler](scheduler-outbound-authentication.md)


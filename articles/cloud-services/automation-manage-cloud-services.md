---
title: "Gerir serviços Cloud do Azure através da automatização do Azure | Microsoft Docs"
description: "Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir cloud services do Azure à escala."
services: cloud-services, automation
documentationcenter: 
author: jodoglevy
manager: timlt
editor: 
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 6b5acac1b8647c324988c316cd5602b3dba98a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Gerir Cloud Services do Azure através da automatização do Azure
Este guia apresenta-lhe o serviço de automatização do Azure e como podem ser utilizada para simplificar a gestão dos seus serviços em nuvem do Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A automatização do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar a gestão de nuvem através de automatização de processos. Utilizar a automatização do Azure, as tarefas de longa execução, manuais, propensas ao erro e frequentemente repetidas podem ser automatizadas para aumentar a fiabilidade, a eficiência e a hora para o valor para a sua organização.

A automatização do Azure fornece um motor de execução do fluxo de trabalho altamente fiáveis e elevada disponibilidade preparada para satisfazer as suas necessidades, à medida que aumenta a sua organização. Na automatização do Azure, os processos podem ser arrancou manualmente, por sistemas de terceiros 3rd ou em intervalos agendados para que as tarefas acontecer exatamente quando necessário.

Reduzir a sobrecarga operacional e libertar IT / equipa de DevOps focar-se no trabalho que adiciona o negócio value ao mover as tarefas de gestão de nuvem a ser executada automaticamente pela automatização do Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Como pode que o automatização do Azure ajuda a gerir cloud services do Azure?
Serviços cloud do Azure podem ser geridos na automatização do Azure, utilizando os cmdlets do PowerShell que estão disponíveis no [ferramentas do Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). A automatização do Azure tem estes cmdlets de PowerShell de serviço de nuvem disponíveis a Box, para que possam executar todas as tarefas de gestão de serviço em nuvem no âmbito do serviço. Também pode ser emparelhado estes cmdlets na automatização do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas em todos os serviços do Azure e 3rd sistemas de terceiros.

Algumas utilizações de exemplo da automatização do Azure para gerir Cloud Services do Azure incluem:

* [Implementação contínua de um serviço em nuvem, sempre que cscfg ou cspkg é atualizado no Blob storage do Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Instâncias de serviço em nuvem em paralelo, um domínio de atualização em vez de reiniciar](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas de automatização do Azure e como podem ser utilizada para gerir cloud services do Azure, siga estas ligações para saber mais sobre a automatização do Azure.

* [Descrição geral da automatização do Azure](../automation/automation-intro.md)
* [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizagem de automatização do Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
